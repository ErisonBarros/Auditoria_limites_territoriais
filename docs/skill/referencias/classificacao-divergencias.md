# Classificação de Divergências e Severidade

> Códigos `D1–D7` para divergências e `CRÍTICA / ALTA / MÉDIA / BAIXA` para severidade. Para o contexto operacional, veja [`../COMO_FUNCIONA.md`](../COMO_FUNCIONA.md) (Fase 6).

---

## 1. Códigos de divergência (D1–D7)

A skill classifica toda divergência detectada em um dos 7 códigos abaixo. Cada código é mutuamente exclusivo — uma divergência recebe exatamente um código, o mais específico aplicável.

### D1 — Arredondamento

**Definição**: Diferença entre representações que decorre apenas de arredondamento numérico.

**Critério objetivo**:

```
Δ ≤ 0,5 × 10^(-d)
```

onde `d` é o menor número de casas decimais entre as duas representações.

**Exemplos**:

| Caso | Δ | Casas | D1? |
|---|---:|---:|:---:|
| Memorial E = 294142,648 vs Planta E = 294142,65 | 0,002 | 2 vs 3 | ✓ |
| Memorial E = 294142,65 vs Planta E = 294142,6 | 0,05 | 1 vs 2 | ✓ |
| Memorial E = 294142,6 vs Planta E = 294142,7 | 0,1 | 1 vs 1 | ✗ (não é arredondamento) |

**Severidade típica**: BAIXA

---

### D2 — Diferença geométrica pequena

**Definição**: Diferença geométrica acima do limite de arredondamento mas dentro da tolerância comparativa aplicável.

**Critério objetivo**:

```
D1 < Δ ≤ tolerância comparativa (3× precisão)
```

**Exemplos**:

| Caso | Tipo | Δ | Tolerância | D2? |
|---|---|---:|---:|:---:|
| Limite artificial, ΔXY = 0,31 m | Artificial | 0,31 m | 1,50 m | ✓ |
| Limite natural, ΔXY = 2,8 m | Natural | 2,8 m | 9,00 m | ✓ |

**Severidade típica**: MÉDIA

---

### D3 — Diferença geométrica relevante

**Definição**: Diferença geométrica acima da tolerância comparativa mas até 3× a tolerância.

**Critério objetivo**:

```
tolerância < Δ ≤ 3 × tolerância
```

**Exemplos**:

| Caso | Tipo | Δ | Tolerância | 3× Tol | D3? |
|---|---|---:|---:|---:|:---:|
| Limite artificial, ΔXY = 2,0 m | Artificial | 2,0 m | 1,50 m | 4,50 m | ✓ |
| Limite natural, ΔXY = 12,5 m | Natural | 12,5 m | 9,00 m | 27,00 m | ✓ |

**Severidade típica**: ALTA

**Quando Δ > 3× tolerância**: ainda é `D3` mas pode ser reclassificado como **CRÍTICA** se indicar inconsistência grave (ver seção 2 abaixo).

---

### D4 — Divergência documental

**Definição**: Diferença em nome, grafia, formatação ou descrição que não impacta a geometria.

**Exemplos**:

- Memorial cita "Rio Paratibe", planta cita "Rio P."
- Memorial cita "Gleba C", banco cita "GLEBA-C"
- Memorial usa "km 5", planta usa "5 km"
- Memorial cita "E =", planta cita "X ="

**Severidade típica**: BAIXA (quando puramente documental) ou MÉDIA (quando dificulta identificação inequívoca)

---

### D5 — Divergência cadastral

**Definição**: Diferença em identificadores cadastrais (matrícula, CNS, SNCR, CCIR, CIB).

**Exemplos**:

- Matrícula 12.345 no memorial, 12.346 no GeoRural
- CNS 12.345-6 no memorial, 12.347-0 na base
- SNCR 987.654.321.0 no memorial, ausente na base

**Severidade típica**: MÉDIA (anomalia menor) ou ALTA (matrícula inexistente ou contraditória)

---

### D6 — Divergência topológica

**Definição**: Problema na estrutura geométrica do polígono (fechamento, continuidade, sequência, sentido).

**Exemplos**:

- Polígono aberto (último vértice ≠ primeiro vértice)
- Segmento ausente entre V-03 e V-04
- Auto-interseção entre V-05→V-06 e V-09→V-10
- Sentido anti-horário em parcela SIGEF
- Vértice duplicado em coordenadas diferentes
- Vértice órfão (sem par na polilinha)

**Severidade típica**: ALTA (maioria) ou CRÍTICA (polígono aberto, auto-interseção)

---

### D7 — Divergência normativa

**Definição**: Não atendimento a critério formal de norma técnica.

**Exemplos**:

- Sentido anti-horário em parcela SIGEF (Manual SIGEF 2ª ed.)
- CRS inadequado (não SIRGAS2000) para fins de certificação
- Método de posicionamento não aceito pela NTG INCRA
- Memória de cálculo ausente para certificação rural
- ART não registrada
- RT sem habilitação para o serviço

**Severidade típica**: ALTA (maioria) ou CRÍTICA (certificação impedida)

---

## 2. Níveis de severidade

A skill atribui uma de quatro severidades a cada divergência.

### CRÍTICA

**Definição**: Impede o prosseguimento do processo ou invalida o resultado.

**Casos típicos**:

- Polígono aberto
- Vértice inexistente (referenciado mas ausente)
- Auto-interseção relevante
- Segmento ausente
- Incompatibilidade grave de sistema
- Conflito geométrico relevante (Δ > 3× tolerância)
- Geometria inválida detectada por `is_valid`

**Ação recomendada**: NÃO submeter à certificação até resolução.

---

### ALTA

**Definição**: Indica inconsistência séria que requer ação corretiva antes de prosseguir.

**Casos típicos**:

- ΔXY acima do critério aplicável
- Azimute divergente acima da tolerância
- Distância divergente acima da tolerância
- Confrontante incompatível (declaração de confrontante que não confere com a base)
- Área incompatível (ΔA > tolerância)
- Perímetro incompatível (ΔP > tolerância)
- Código de vértice duplicado
- Matrícula divergente entre memorial e base
- Método de posicionamento inadequado

**Ação recomendada**: revisar antes de submeter.

---

### MÉDIA

**Definição**: Indica anomalia que pode ser resolvida sem grande impacto, mas deve ser documentada.

**Casos típicos**:

- Pequena diferença geométrica dentro de tolerância expandida
- Nomenclatura diferente mas reconhecível como equivalente
- Confrontante parcialmente declarado
- Divergência de precisão (σP acima do mínimo mas dentro de tolerância expandida)
- Divergência cadastral em identificador secundário (CNS quando matrícula confere)

**Ação recomendada**: documentar e prosseguir se o impacto for considerado aceitável pelo RT.

---

### BAIXA

**Definição**: Indica anomalia menor, sem impacto geométrico ou normativo.

**Casos típicos**:

- Grafia diferente
- Formatação diferente (separador decimal `,` vs `.`, separador de milhar)
- Casas decimais com arredondamento (D1)
- Espaços extras em nomes
- Diferença de caixa (maiúscula/minúscula)

**Ação recomendada**: pode ser corrigida sem impacto documental significativo.

---

## 3. Matriz de decisão (resumo)

| Tipo | D1 | D2 | D3 | D4 | D5 | D6 | D7 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Arredondamento | ✓ | | | | | | |
| Diferença ≤ tolerância comparativa | | ✓ | | | | | |
| Diferença > tolerância comparativa | | | ✓ | | | | |
| Nome/grafia sem impacto | | | | ✓ | | | |
| Matrícula/CNS divergente | | | | | ✓ | | |
| Topologia (fechamento, sequência) | | | | | | ✓ | |
| Norma não atendida | | | | | | | ✓ |

| Severidade | CRÍTICA | ALTA | MÉDIA | BAIXA |
|---|:---:|:---:|:---:|:---:|
| Impede certificação | ✓ | | | |
| Requer revisão antes | | ✓ | | |
| Documentar e prosseguir | | | ✓ | |
| Impacto mínimo | | | | ✓ |

---

## 4. Aplicação a casos específicos

### Caso: Polígono aberto detectado

A skill registra:

```
Tipo: D6
Severidade: CRÍTICA
Descrição: Polígono aberto — vértice final (V-14) não coincide com vértice inicial (V-01).
Δfechamento: 152,34 m (diferença entre V-14 e V-01)
Norma aplicável: NBR 13133:2021 (fechamento obrigatório)
Recomendação: refazer levantamento ou declarar inconsistência formal
```

### Caso: ΔXY = 0,31 m em limite artificial

A skill registra:

```
Tipo: D2
Severidade: MÉDIA
Descrição: Diferença geométrica pequena entre V-03 do memorial e V-03 da planta.
ΔXY = 0,31 m
Tolerância aplicável: 1,50 m (3× precisão 0,50 m para limite artificial)
Norma: INCRA NTG 3ª ed., §3.1
Resultado: DENTRO DA TOLERÂNCIA — não impede prosseguimento
```

### Caso: Sentido anti-horário em parcela SIGEF

A skill registra:

```
Tipo: D7
Severidade: ALTA
Descrição: Sentido do perímetro é anti-horário; SIGEF exige sentido horário.
Norma: Manual SIGEF 2ª ed., seção 4.1 (sentido horário obrigatório)
Recomendação: inverter a ordem dos vértices antes de submeter ao SIGEF
```

### Caso: Matrícula divergente entre memorial e GeoRural

A skill registra:

```
Tipo: D5
Severidade: ALTA
Descrição: Matrícula declarada no memorial (12.345) diverge da matrícula registrada no GeoRural (12.346).
Norma: Lei 10.267/2001 (consistência cadastro × registro)
Recomendação: verificar procedência do imóvel e retificar memorial ou registro
```

---

## 5. Rastreabilidade

Toda classificação de divergência inclui:

```
{
  "id": "DIV-001",
  "tipo": "COORDENADA",        // tipo de dado divergente
  "codigo": "D3",               // código de classificação
  "segmento": "V-07-V-08",      // localização exata
  "fonte_a": "MEMORIAL",        // fonte A
  "fonte_b": "PLANTA",          // fonte B
  "valor_a": "...",             // valor observado em A
  "valor_b": "...",             // valor observado em B
  "diferenca": "...",           // diferença calculada
  "tolerancia": "...",          // tolerância aplicável
  "norma": "INCRA NTG 3ª ed.", // norma consultada
  "item_normativo": "§3.1",     // item específico da norma
  "resultado": "DENTRO DA TOLERÂNCIA" | "ACIMA DA TOLERÂNCIA" | "NÃO DETERMINADO",
  "observacao": "..."           // interpretação adicional
}
```

---

**Próximo passo**: [`schema-json.md`](./schema-json.md) — estrutura JSON completa do relatório.
