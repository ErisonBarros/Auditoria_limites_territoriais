# Módulo CAD — Auditoria Específica de DXF/DWG

> Detalhamento das seções 42–58 da [`../SKILL.md`](../SKILL.md). Aplica-se quando a planta fornecida está em formato CAD (DXF, DWG).

---

## 1. Por que um módulo específico para CAD

O arquivo CAD de uma planta topográfica é, na prática, **três fontes de informação simultâneas**:

1. **Geometria efetiva** — a polilinha que desenha o limite
2. **Roteiro textual** — tabelas, TEXT, MTEXT que registram azimutes/distâncias
3. **Anotações/blocos** — INSERTs com atributos que representam vértices (código, coordenadas)

Essas três fontes **não são automaticamente coerentes entre si**. Erros comuns em CADs topográficos incluem:

- Blocos `V-NN` com coordenadas de atributos erradas
- Tabela de roteiro com sequência diferente da polilinha
- Sentido horário na polilinha mas anti-horário no roteiro
- Vértices órfãos (blocos sem par na polilinha)
- Códigos duplicados (V-08 aparecendo duas vezes)
- CRS/unidade inconsistente (CAD em mm, memorial em m)

A skill trata cada fonte como **independente** e compara antes de tirar conclusões.

---

## 2. As três classes de informação (seção 42)

### 2.1 Geometria efetiva do limite

**Definição**: O limite realmente desenhado no CAD, representado por entidades geométricas.

**Prioridade de entidades**:

1. `LWPOLYLINE` (Lightweight Polyline) — a mais comum em DXFs modernos
2. `POLYLINE` (Polyline clássica) — encontrada em DXFs legados
3. `3DPOLY` (Polyline 3D)
4. `LINE` em sequência contínua — usada como fallback

**Critérios para validar uma polilinha como limite candidato**:

| Critério | Verificação |
|---|---|
| Fechamento | Primeiro vértice = último vértice (tolerância 1 mm) |
| Quantidade de vértices | ≥ 3 (mínimo para polígono) |
| Continuidade | Sem segmentos degenerados (comprimento = 0) |
| Sentido | Horário ou anti-horário (registrado) |
| Layer | Nome sugestivo (LIMITE, PERIMETRO, IMOVEL, PARCELA) — não obrigatório |
| Posição | Apropriada para a região declarada |
| Ausência de auto-interseção | `is_valid` (OGC SFA) = True |

**Métricas registradas**:

- Layer
- Handle/identificador
- Tipo de entidade
- Número de vértices
- Fechamento (bool)
- Comprimento total
- Área (quando aplicável)
- CRS/unidades disponíveis

### 2.2 Roteiro textual do CAD

**Definição**: Tabelas, TEXT, MTEXT ou atributos de blocos que representam um roteiro perimétrico dentro do próprio CAD.

**Fontes típicas**:

- Tabela com nome `TOPO_ROTEIRO`, `QUADRO_AREAS`, `TABELA_AZIMUTES`
- TEXT/MTEXT posicionados próximo aos vértices da polilinha
- Atributos de blocos especiais (`AZIMUTE_BLOCO`, `DIST_BLOCO`)
- Tags vinculadas via `XDATA`

**Atenção crítica**:

O roteiro textual **NÃO deve ser considerado automaticamente equivalente** à geometria desenhada.

A skill sempre compara:

```
GEOMETRIA CAD ↔ ROTEIRO CAD
```

antes de usar o roteiro como evidência para validação contra o memorial.

### 2.3 Blocos e anotações de vértices

**Definição**: Entidades INSERT (blocos) que representam vértices individuais, frequentemente com atributos como coordenadas, azimute, distância.

**Campos extraídos**:

- Nome/código do bloco (ex.: `V-01`, `MARCO_M01`)
- Atributos (campos chave-valor dentro do bloco)
- Coordenada de inserção (posição geométrica do bloco)
- Layer
- Rotação
- Escala
- Coordenadas declaradas nos atributos (que **podem diferir** da posição de inserção!)

**Três tipos de inconsistência detectados**:

| ID | Tipo | Descrição |
|---|---|---|
| `CAD-001` | Bloco órfão | Sem par na polilinha |
| `CAD-002` | Código duplicado | Mesmo código em coordenadas distintas |
| `CAD-003` | Atributo incompatível | Código do bloco ≠ atributo ≠ posição |

---

## 3. Matriz de consistência CAD (seção 43)

A skill gera uma tabela para cada vértice, comparando as três fontes:

```
| Vértice | Polilinha | Roteiro CAD | Bloco/Anotação | Código | ΔXY | Situação |
| V-01 | ✓ | ✓ | ✓ | V-01 | 0,00 m | Compatível |
| V-02 | ✓ | ✓ | ✓ | V-02 | 0,05 m | Compatível |
| V-03 | ✓ | ✗ | ✓ | V-03 | 0,12 m | CAD-001 — Bloco órfão |
```

Cada fonte permanece identificada na matriz. A skill **nunca** corrige o CAD; ela apenas relata.

---

## 4. Detecções específicas (seções 44–49)

### 4.1 Bloco órfão (CAD-001)

Considera-se potencialmente órfão quando:

- Não corresponde a nenhum vértice da polilinha
- Está fora da tolerância espacial
- Possui código inexistente no roteiro
- Possui coordenadas incompatíveis com sua posição gráfica

**Classificação**: `BLOCO ÓRFÃO — REVISÃO NECESSÁRIA`

### 4.2 Código duplicado (CAD-002)

Detectado quando:

- Dois ou mais blocos possuem o mesmo código
- O mesmo código aparece com coordenadas distintas
- O mesmo vértice possui múltiplas representações conflitantes

**Classificação**: `CÓDIGO DE VÉRTICE DUPLICADO`

**Relatório apresenta**:

- Código
- Posição de cada ocorrência
- Coordenadas dos atributos
- Diferença espacial

### 4.3 Código incompatível (CAD-003)

Comparação entre:

- Código do bloco
- Código do roteiro
- Código/sequência da geometria

Exemplo:

- Bloco = `V-01`
- Atributo = coordenadas de `V-07`
- Posição do bloco = próxima de `V-07`

**Classificação**: `CÓDIGO DE ANOTAÇÃO INCOMPATÍVEL COM ATRIBUTO`

### 4.4 Posição de inserção incompatível (CAD-004)

A posição geométrica do bloco difere do que o atributo declara.

Exemplo:

- Inserção do bloco V-01 em (294142,650 ; 9122335,960)
- Atributo do bloco V-01 declara (294142,648 ; 9122335,960)
- ΔXY inserção–atributo = 0,002 m (dentro da tolerância)
- Mas: vértice V-01 da polilinha está em (294142,648 ; 9122335,960)
- ΔXY inserção–polilinha = 0,002 m (dentro)
- ΔXY atributo–polilinha = 0,000 m (perfeito)

→ Inserção com pequeno offset, mas atributo correto. Provavelmente erro de "snap" do operador.

### 4.5 Anotação fora do limite (CAD-006)

Para cada bloco de vértice, a skill calcula a distância até o vértice correspondente da polilinha e classifica:

| ΔXY | Classificação |
|---:|---|
| ≤ 0,005 m | COINCIDENTE |
| ≤ tolerância (0,50 m artificial) | PRÓXIMO |
| > tolerância | FORA DA TOLERÂNCIA |
| Sem par | SEM CORRESPONDÊNCIA |

### 4.6 Segmento curto (CAD-007 variante)

Quando um segmento possui comprimento muito pequeno em relação aos demais, a skill emite:

```
SEGMENTO CURTO — MAIOR SENSIBILIDADE AO ARREDONDAMENTO ANGULAR
```

Exemplo: se 13 segmentos têm ~80–150 m e 1 segmento tem 0,5 m, esse segmento curto é sensível a arredondamento angular. Pequenos erros de coordenada geram grandes erros angulares.

A skill **nunca classifica** diferença angular isolada como erro de levantamento sem considerar comprimento e precisão.

---

## 5. Distinção entre três coordenadas (seção 48)

Para cada bloco de vértice, a skill distingue:

| Coordenada | Definição | Origem |
|---|---|---|
| **A. Posição de inserção** | Coordenada geométrica do bloco | Atributo `INSERT` no DXF |
| **B. Coordenada do atributo** | Coordenada armazenada no bloco | Atributo `ATTRIB` no DXF |
| **C. Coordenada da polilinha** | Coordenada efetiva do limite | Entidade `LWPOLYLINE` no DXF |

Tabela gerada:

```
| Código | Inserção (A) | Atributo (B) | Polilinha (C) | ΔA-C | ΔB-C |
| V-01 | ... | ... | ... | ... | ... |
```

Essas três coordenadas podem (e frequentemente diferem) por arredondamento, erro de "snap" ou erro humano.

---

## 6. Consistência tripla (seção 55)

Quando todas as três fontes (geometria + roteiro + blocos) estão presentes, cada vértice é classificado como:

| Classificação | Critério |
|---|---|
| `CONSISTÊNCIA TRIPLA` | Os três elementos correspondem |
| `CONSISTÊNCIA PARCIAL` | Dois elementos correspondem e um diverge |
| `INCONSISTÊNCIA` | Os três elementos apresentam informações conflitantes |
| `NÃO VERIFICÁVEL` | Dados insuficientes |

---

## 7. Integração com memorial (seção 56)

Após a auditoria interna do CAD, a skill compara separadamente:

```
MEMORIAL × GEOMETRIA CAD
MEMORIAL × ROTEIRO CAD
MEMORIAL × BLOCOS CAD
```

Isso permite distinguir quatro tipos de divergência:

- Divergência do memorial (planta ok entre si)
- Divergência geométrica (geometria CAD ≠ outras fontes)
- Divergência de tabela (roteiro CAD ≠ outras fontes)
- Divergência de anotação (blocos CAD ≠ outras fontes)

---

## 8. Regra de interpretação (seção 57)

Quando a polilinha, o roteiro e os blocos dão informações contraditórias sobre o mesmo vértice, a skill **não decide automaticamente** qual fonte está correta. Em vez disso, ela registra a divergência com detalhes.

**Exemplo**:

```
Polilinha vértice = V-07 (em coordenadas de V-07)
Roteiro CAD = V-07
Bloco = V-01
Atributo do bloco = coordenadas de V-07
Posição do bloco = próxima de V-07
```

A skill registra `CAD-003 — Código de anotação incompatível` e descreve:

> O bloco rotulado como `V-01` está geometricamente posicionado nas coordenadas de `V-07`, e seus atributos também declaram coordenadas de `V-07`. **Conclusão técnica**: trata-se de um erro de **rotulagem do bloco** — provavelmente o operador CAD usou o template do bloco V-01 mas copiou as coordenadas de V-07. **Recomendação**: revisar o bloco e renomeá-lo para `V-07` ou substituir pelo bloco correto.

A skill **não renomeia automaticamente**.

---

## 9. O que a skill NUNCA faz no CAD (seção 53)

A skill nunca:

- Move blocos
- Renomeia vértices
- Altera coordenadas
- Edita polilinhas
- Apaga entidades
- Reordena vértices
- Substitui atributos

Ela pode apenas:

- Detectar
- Medir
- Comparar
- Classificar
- Sugerir revisão

---

## 10. Teste prático

### Cenário

Você recebe o arquivo `gleba_ribeirao.dwg` com 18 vértices e descobre:

1. Bloco `V-15` sem par na polilinha
2. Bloco `V-08` em duas coordenadas distintas
3. Bloco rotulado `V-09` com atributos de `V-12`
4. Bloco `V-05` com offset de 4,20 m da polilinha
5. Roteiro textual em sentido oposto à polilinha

### O que a skill entrega

- 5 ocorrências detectadas (CAD-001, CAD-002, CAD-003, CAD-006, CAD-005)
- Severidades: 3 ALTAS, 1 MÉDIA, 1 BAIXA
- Tabela com cada ocorrência
- Recomendações de revisão para cada uma
- JSON estruturado para integração com outros sistemas

---

**Próximo passo**: [`../../exemplos/`](../../exemplos/) — exemplos de outputs reais.
