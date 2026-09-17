# Como Funciona — Skill `auditoria-limites-territoriais`

> Descrição narrativa detalhada do fluxo operacional. Para a especificação formal, veja [`SKILL.md`](./SKILL.md). Para começar, veja [`GUIA_RAPIDO.md`](./GUIA_RAPIDO.md).

---

## Visão geral do processo

A skill opera em **6 fases sequenciais**, cada uma com saídas verificáveis:

```
┌──────────────────────────────────────────────────────────┐
│ FASE 1 — INGESTÃO E IDENTIFICAÇÃO                        │
│   • Receber arquivos                                     │
│   • Classificar por extensão e conteúdo                  │
│   • Determinar modo de trabalho                          │
│   • Identificar CRS e normas aplicáveis                  │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│ FASE 2 — EXTRAÇÃO INDEPENDENTE                           │
│   • Roteiro 01: Memorial descritivo                      │
│   • Roteiro 02: Geometria da planta                      │
│   • Roteiro 03: Base geoespacial (se houver)             │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│ FASE 3 — NORMALIZAÇÃO                                    │
│   • Padronizar CRS                                       │
│   • Padronizar unidades (métrica)                        │
│   • Padronizar azimutes (decimal + DMS original)        │
│   • Validar geometrias (fechamento, validade topológica) │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│ FASE 4 — COMPARAÇÃO TRIPLA                               │
│   • Correspondência de vértices (código → banco → seq. → │
│     proximidade espacial)                                │
│   • Cálculo de ΔXY, ΔAz, ΔDist, ΔA, ΔPerímetro          │
│   • Fechamento individual de cada fonte                  │
│   • Comparação por trecho                                │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│ FASE 5 — APLICAÇÃO DE CRITÉRIOS                          │
│   • NBR 13133:2021 (topográfico)                         │
│   • INCRA 3ª ed. (rural: 0,50 m / 3,00 m / 7,50 m)     │
│   • SIGEF (sentido horário, unicidade, sequência)       │
│   • Tolerâncias comparativas (3× precisão)                │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│ FASE 6 — DOCUMENTAÇÃO                                    │
│   • Classificar divergências (D1–D7)                     │
│   • Atribuir severidade (CRÍTICA/ALTA/MÉDIA/BAIXA)      │
│   • Gerar matriz de conformidade                         │
│   • Produzir conclusão técnica com limitação declarada   │
└──────────────────────────────────────────────────────────┘
```

---

## Fase 1 — Ingestão e identificação

### 1.1 Receber arquivos

A skill aceita entradas em quatro formatos:

- **Documentais**: PDF, DOC, DOCX, TXT, imagens (com OCR quando disponível)
- **CAD**: DXF, DWG
- **Geoespaciais**: GeoPackage (`.gpkg`), SHP, GeoJSON, KML
- **Bancos de referência**: `TopoGeo.gpkg`, `GeoRural.gpkg`

### 1.2 Classificar por extensão e conteúdo

Cada arquivo é triado por:

- Extensão (filtragem rápida)
- Magic bytes (verificação real do formato — um `.dxf` pode ser binário corrompido)
- Conteúdo textual (primeiras linhas para identificar tipo)

A skill nunca confia apenas na extensão.

### 1.3 Determinar modo de trabalho

A skill aplica o seguinte decisor:

| Indício | Modo |
|---|---|
| `TopoGeo.gpkg` presente | `TOPOGRÁFICO` ou `CADASTRAL` |
| `GeoRural.gpkg` presente, palavras "INCRA", "SIGEF", "certificação", "imóvel rural", "matrícula rural", "SNCR", "CCIR", "CIB" | `GEORREFERENCIAMENTO_RURAL` ou `CERTIFICACAO_SIGEF` |
| Ambos presentes ou mistura de indícios | `HÍBRIDO` |
| Apenas memorial + planta, sem base | `HÍBRIDO` (limitado) ou `INDETERMINADO` |

### 1.4 Identificar CRS e normas

Para cada arquivo, a skill registra:

- CRS original (SIRGAS2000/UTM, WGS84/Geográfica, SAD69, etc.)
- Datum (SIRGAS2000, WGS84, SAD69, Córrego Alegre)
- Projeção (UTM, LTM, Topográfica Local, Geográfica)
- Meridiano central e fuso (quando UTM/LTM)
- Unidade (m, grau, grau-minuto-segundo)

A skill **nunca calcula distância ou azimute em coordenadas geográficas em graus**. Primeiro transforma para sistema métrico projetado.

---

## Fase 2 — Extração independente

### 2.1 Roteiro 01 — Memorial

A skill lê o memorial e extrai, para cada vértice:

- Código (V-01, M-01, P-01, etc.)
- Coordenada E (com notação original `E = 294.142,648`)
- Coordenada N (com notação original `N = 9.122.335,960`)
- Azimute (em DMS ou decimal — ambos preservados)
- Distância (em metros)
- Confrontante
- Tipo de limite

A skill reconhece variações de notação:

- `E=`, `E =`, `ESTE:`, `X:`
- `N=`, `N =`, `NORTE:`, `Y:`
- `AZ:`, `AZIMUTE:`, `AZIM.`
- `DIST:`, `DISTÂNCIA:`, `D=`

Para cada par de vértices consecutivos, gera uma linha do Roteiro 01 com:

- ΔE calculado (E_final − E_inicial)
- ΔN calculado
- Azimute calculado (atan2 de ΔE/ΔN, normalizado 0°–360°)
- Distância calculada (√(ΔE² + ΔN²))
- Erro angular (|Az_declarado − Az_calculado|, com diferença circular)
- Erro linear (|D_declarada − D_calculada|)

### 2.2 Roteiro 02 — Planta

A skill lê a planta e distingue três classes de informação (seção 42 da SKILL):

#### 2.2.1 Geometria efetiva do limite

Prioriza entidades:

- `LWPOLYLINE` (Lightweight Polyline — mais comum em DXF)
- `POLYLINE` (Polyline clássica)
- `3DPOLY` (Polyline 3D)
- `LINE` em sequência contínua (caso polilinha não exista)

Quando há múltiplas polilinhas fechadas candidatas, a skill:

1. Verifica fechamento (primeiro vértice = último vértice)
2. Conta vértices (mais que 3 é requisito mínimo para polígono)
3. Verifica continuidade (sem segmentos degenerados)
4. Identifica a layer mais provável (contendo palavras como "LIMITE", "PERIMETRO", "IMOVEL", "PARCELA")
5. Verifica ausência de auto-interseções
6. Calcula comprimento e área (quando aplicável)

#### 2.2.2 Roteiro textual do CAD

Quando o CAD contém tabelas, `TEXT`, `MTEXT` ou atributos de blocos que representam um roteiro perimétrico (ex.: tabela `TOPO_ROTEIRO`), a skill extrai separadamente.

**Atenção**: o roteiro textual do CAD **NÃO é automaticamente equivalente** à geometria desenhada. A skill compara sempre `GEOMETRIA CAD ↔ ROTEIRO CAD` antes de usar o roteiro como evidência.

#### 2.2.3 Blocos e anotações de vértices

A skill extrai cada bloco/anotação que represente um vértice:

- Nome do bloco (V-01, MARCO_01, etc.)
- Atributos (especialmente `COORD_E`, `COORD_N`, `AZIMUTE`, `DISTANCIA`)
- Coordenada de inserção do bloco
- Layer, rotação, escala
- Coordenadas declaradas nos atributos (que podem diferir da posição de inserção!)

A skill detecta três tipos de inconsistência de bloco:

- **Bloco órfão** (CAD-001): não corresponde a nenhum vértice da polilinha
- **Bloco duplicado** (CAD-002): mesmo código em coordenadas distintas
- **Código incompatível** (CAD-003): código do bloco ≠ atributo ≠ posição

### 2.3 Roteiro 03 — Base geoespacial

#### 2.3.1 TopoGeo.gpkg (topográfico/cadastral)

A skill consulta as camadas:

- `property_area_a` — polígono da propriedade (campos: `property`, `registry`, `owner`, `address`, `county`, `state`, `survey_date`, `surveyor`, `tech_manager`, `prof_id`, `area`, `perimeter`, `transcript`)
- `limit_point_p` — pontos vértices (campos: `type`, `sequence`, `code`, geometria)
- `boundary_element_l` — elementos de limite (campos: `borderer`, `borderer_label`, `start_pnt_descr`, `authorizer`, `authorizer_id`, `borderer_registry`)

#### 2.3.2 GeoRural.gpkg (rural/SIGEF)

A skill consulta as camadas:

- `parcela` — imóvel rural (campos: `nome`, `denominacao`, `cpf_cnpj`, `sncr`, `matricula`, `cod_cartorio`, `municipio`, `uf`, `natureza`, `situacao`, `data`)
- `limite` — confrontações (campos: `tipo`, `confrontan`, `cns`, `matricula`, `qrcode`)
- `vertice` — vértices georreferenciados (campos: `indice`, `metodo_pos`, `tipo_verti`, `vertice`, `sigma_x`, `sigma_y`, `sigma_z`, geometria)

Para auditoria histórica, também: `hist_parcela`, `hist_limite`, `hist_vertice`.

---

## Fase 3 — Normalização

### 3.1 Padronizar CRS

A skill identifica o CRS de cada fonte (via metadados do arquivo, declaração no memorial, ou inspeção dos valores numéricos). Em seguida, todas as fontes são transformadas para um **sistema métrico comum apropriado** — tipicamente SIRGAS2000/UTM no fuso correto do imóvel.

### 3.2 Validar geometrias

Para cada polígono extraído, a skill valida:

- Fechamento (primeiro vértice = último vértice com tolerância de 1 mm)
- Validade topológica (sem auto-interseções)
- Orientação (horária ou anti-horária)
- Área e perímetro (via `shapely`)
- Presença de geometrias inválidas (`is_valid`)
- Presença de geometrias vazias
- Duplicidades (geometrias sobrepostas)

### 3.3 Padronizar azimutes

A skill preserva a representação original (DMS quando aplicável) e calcula internamente em graus decimais para todas as comparações.

A normalização azimutal segue:

```
0° ≤ azimute < 360°
```

A diferença circular é calculada como:

```
ΔAz = min(|Az1 − Az2|, 360° − |Az1 − Az2|)
```

---

## Fase 4 — Comparação tripla

### 4.1 Correspondência de vértices

A skill executa a correspondência na seguinte ordem (seção 15 da SKILL):

1. **Código direto**: se os códigos forem iguais, `CORRESPONDÊNCIA DIRETA`.
2. **Banco**: tenta correspondência com `TopoGeo.gpkg` ou `GeoRural.gpkg`.
3. **Sequência**: compara posição ordinal (V-01 da fonte A pode corresponder a V-03 da fonte B se houver inversão).
4. **Proximidade espacial**: calcula `ΔXY = √((E1−E2)² + (N1−N2)²)` e classifica como forte (≤ 0,50 m), provável (≤ 5,00 m) ou sem correspondência.

### 4.2 Teste de inversão

A skill também testa a **sequência inversa** (V-01↔V-14, V-02↔V-13, etc.). Se a geometria coincidir mas o sentido for oposto, registra `MESMA GEOMETRIA — SENTIDO INVERSO`.

Para SIGEF, a skill verifica separadamente o requisito de **sentido horário** (regra do sistema, não da geometria).

### 4.3 Comparação por trecho

Para cada par de vértices consecutivos, a skill gera uma linha de comparação:

```
| Trecho | ΔVértices | ΔAz | ΔDistância | Memorial | Planta | Banco | Resultado |
```

### 4.4 Fechamento individual

Para cada fonte, a skill calcula:

- `ΣΔE = Σ(E_final − E_inicial)` sobre todos os segmentos
- `ΣΔN = Σ(N_final − N_inicial)`
- `e = √(ΣΔE² + ΣΔN²)` (erro de fechamento linear)
- `Precisão relativa = Perímetro / e`

A skill **nunca fecha o polígono artificialmente** unindo o último vértice ao primeiro se o memorial/planta não fizer isso explicitamente. Quando não há fechamento declarado, isso é registrado como `DIV-D6` (divergência topológica — polígono aberto).

---

## Fase 5 — Aplicação de critérios

### 5.1 NBR 13133:2021 (Levantamento topográfico)

Quando o modo é topográfico e a NBR 13133 é aplicável:

- **Precisão linear mínima**: 1:12.000
- **Tolerância angular**: `Tα = 3 × p × √n + 10"` (p = precisão nominal, n = estações)

A skill compara a precisão relativa calculada com a tolerância.

### 5.2 INCRA — Norma Técnica para Georreferenciamento (3ª ed.)

Para limites rurais, a skill aplica a tabela:

| Tipo de limite | Precisão posicional absoluta |
|---|---:|
| Artificial | 0,50 m |
| Natural | 3,00 m |
| Inacessível | 7,50 m |

A **tolerância comparativa** (3× a precisão) é usada quando se compara a planta/memorial com uma parcela certificada:

| Tipo | Precisão | Tolerância comparativa |
|---|---:|---:|
| Artificial | 0,50 m | 1,50 m |
| Natural | 3,00 m | 9,00 m |
| Inacessível | 7,50 m | 22,50 m |

Esses valores **não são tolerâncias universais** — são aplicadas conforme a finalidade e o contexto normativo.

### 5.3 SIGEF — Regras de pré-validação

Quando o modo é `CERTIFICACAO_SIGEF`, a skill verifica os pré-requisitos:

- Unicidade de código de vértice
- Sequência correta
- Sentido horário
- Vértice inicial declarado
- Sistema de referência adequado
- Validade geométrica (sem auto-interseções)
- Continuidade (sem segmentos ausentes)
- Fechamento
- Tipo de limite declarado
- Método de posicionamento registrado
- Confrontante declarado
- CNS do confrontante (quando aplicável)
- Matrícula declarada
- Posição dos vértices (sem inversões suspeitas)
- Compatibilidade dos dados

**Atenção**: a skill é uma **pré-validação**. A certificação oficial é ato do INCRA/SIGEF.

---

## Fase 6 — Documentação

### 6.1 Classificação de divergências (D1–D7)

| Código | Tipo | Critério |
|---|---|---|
| `D1` | Arredondamento | Diferença ≤ 5 cm em coords com 2 casas decimais |
| `D2` | Diferença geométrica pequena | ΔXY ≤ tolerância comparativa aplicável |
| `D3` | Diferença geométrica relevante | ΔXY > tolerância mas ≤ 3× tolerância |
| `D4` | Divergência documental | Nomes, grafia, formatação sem impacto geométrico |
| `D5` | Divergência cadastral | Matrícula, CNS, SNCR, CCIR divergentes |
| `D6` | Divergência topológica | Segmento ausente, polígono aberto, auto-interseção |
| `D7` | Divergência normativa | Sentido incorreto, CRS inadequado, método inadequado |

### 6.2 Severidade

| Severidade | Critério |
|---|---|
| **CRÍTICA** | Polígono aberto, vértice inexistente, auto-interseção, incompatibilidade grave |
| **ALTA** | ΔXY > critério, azimute divergente, distância divergente, área incompatível |
| **MÉDIA** | Pequena diferença geométrica, nomenclatura diferente |
| **BAIXA** | Grafia, formatação, casas decimais sem impacto |

A severidade é **informativa**, não representa julgamento jurídico.

### 6.3 Matriz de conformidade

Sempre gerada (seção 28 da SKILL), com 15 itens padrão:

```
| Item | Valor observado | Tolerância/critério | Referência | Situação |
```

### 6.4 Conclusão técnica

Responde a 11 perguntas padrão (seção 38), separando:

- **Fato**: o que os dados mostram
- **Cálculo**: o que foi calculado
- **Critério**: qual norma foi aplicada
- **Limitação**: o que não pôde ser verificado

---

## Detecção de inconsistências no CAD

A skill aplica detecções específicas para o módulo CAD (seções 42–58):

- **CAD-001** — bloco órfão (sem vértice correspondente)
- **CAD-002** — código de vértice duplicado
- **CAD-003** — código de anotação incompatível com atributo
- **CAD-004** — posição de inserção incompatível com atributo
- **CAD-005** — roteiro CAD divergente da geometria
- **CAD-006** — anotação fora da tolerância espacial
- **CAD-007** — segmento CAD divergente do memorial
- **CAD-008** — geometria aberta
- **CAD-009** — entidade duplicada
- **CAD-010** — inconsistência de unidade/CRS

Veja [`referencias/modulos-cad.md`](./referencias/modulos-cad.md) para detalhes.

---

## Princípio final

A ordem obrigatória de raciocínio é:

```
EXTRAIR → NORMALIZAR → CALCULAR → COMPARAR → APLICAR CRITÉRIO → DOCUMENTAR
```

Sempre separar:

```
DADO ORIGINAL → RESULTADO CALCULADO → TOLERÂNCIA → INTERPRETAÇÃO → CONCLUSÃO
```

A conclusão deve ser sustentada pelos dados efetivamente encontrados nos arquivos. **Nunca** por inferência, presunção ou regra genérica.

---

**Próximos passos**:
- [`FORMATO_SAIDA.md`](./FORMATO_SAIDA.md) — estrutura completa do relatório
- [`EXEMPLOS.md`](./EXEMPLOS.md) — 4 estudos de caso
- [`referencias/normas.md`](./referencias/normas.md) — referencial normativo
