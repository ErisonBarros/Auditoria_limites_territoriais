# Exemplos — Skill `auditoria-limites-territoriais`

> Quatro estudos de caso ilustrativos. Para o formato completo do relatório, veja [`FORMATO_SAIDA.md`](./FORMATO_SAIDA.md). Para o fluxo, veja [`COMO_FUNCIONA.md`](./COMO_FUNCIONA.md).

---

## Caso 1 — Sítio Felicidade (caso real deste repositório)

**Localização**: Bairro do Nobre, Paulista-PE
**Imóvel**: Sítio Felicidade
**Área**: 61.186,90 m² (6,12 ha)
**Perímetro**: 1.205,72 m
**Sistema**: SIRGAS2000 / UTM fuso -25 / MC 33°W
**Vértices**: 14 (V-01 a V-14)
**Responsável Técnico**: Erison Rosa de Oliveira Barros — Eng. Cartógrafo RNP 1805172549 38236-PE

### Arquivos fornecidos

| Arquivo | Função | Status |
|---|---|---|
| `MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx` | Memorial descritivo (DOCX) | Sanitizado LGPD |
| `SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf` | Prancha A1 (PDF) | Sanitizado LGPD |
| `SITIO_FELICIDADE_SANITIZADO.dwg` | Vetor (DWG AC1015) | Sanitizado LGPD |

### Limites perimétricos (resumo)

| Trecho | Confrontante | Tipo |
|---|---|---|
| V-01 → V-07 | Rio Paratibe + Companhia Paulista | Natural + Artificial |
| V-07 → V-12 | Gleba "C" | Artificial |
| V-12 → V-14 | Sítio Saudade + Gleba "C" | Misto |
| V-14 → V-01 | (fechamento) | — |

### Confronto de tipos de limite (INCRA NTG 3ª ed.)

| Tipo | Precisão absoluta | Tolerância comparativa (3×) |
|---|---:|---:|
| Artificial | 0,50 m | 1,50 m |
| Natural | 3,00 m | 9,00 m |

### Resultado esperado

Para a pré-validação favorável (memorial × planta), a skill deve produzir:

- Divergências: 0 CRÍTICAS, 0 ALTAS, n MÉDIAS (confrontantes parciais na planta), n BAIXAS (arredondamento)
- Precisão relativa: ~1:86.000 (compatível NBR 13133)
- Veredicto: **PRÉ-VALIDAÇÃO FAVORÁVEL** com recomendação de incluir `TopoGeo.gpkg` para validação cadastral completa

Veja `exemplos/roteiro-01-memorial.md`, `exemplos/roteiro-02-planta.md` e `exemplos/matriz-conformidade.md` para outputs de exemplo deste caso.

---

## Caso 2 — Pequena propriedade rural em Sirinhaém-PE (hipotético)

**Imóvel**: Fazenda Boa Esperança
**Área**: 24,5 ha
**Sistema**: SIRGAS2000 / UTM fuso -25
**Vértices**: 8 (M-01 a M-08)
**Modo**: `CERTIFICACAO_SIGEF`
**Base**: `GeoRural.gpkg`

### Cenário

O usuário submete um memorial descritivo, uma planta DXF e a base GeoRural para verificar se a parcela está apta para submissão ao SIGEF.

### Pré-validações SIGEF esperadas

- [x] Códigos únicos (M-01 a M-08, sem repetição)
- [x] Sequência contínua (sem gaps)
- [x] Sentido horário
- [x] Sistema SIRGAS2000 com fuso correto
- [x] Validade geométrica
- [x] Fechamento dentro da tolerância
- [x] Tipos de limite declarados
- [x] Métodos de posicionamento registrados (GNSS/RTK, estação total, etc.)
- [x] Confrontantes declarados com CNS ou matrícula
- [x] σP de cada vértice ≤ 1,50 m (limite artificial comparativo)

### Resultado esperado

- 0 divergências bloqueantes
- 1 observação MÉDIA (limite "Córrego da Onça" classificado como "Natural" no memorial mas como "Curso d'água perene" na base — D4)
- Veredicto: **PRÉ-VALIDAÇÃO SIGEF FAVORÁVEL**

---

## Caso 3 — Imóvel urbano em Recife-PE (hipotético)

**Imóvel**: Lote 15, Quadra 7, Loteamento Boa Viagem
**Área**: 480,00 m²
**Sistema**: SIRGAS2000 / UTM fuso -25
**Vértices**: 5 (P-01 a P-05)
**Modo**: `CADASTRAL`
**Base**: `TopoGeo.gpkg`

### Cenário

Retificação de área em cartório. O memorial declara 480,00 m² mas a planta recalculada produz 478,35 m² (ΔA = -1,65 m² = -0,34%). A base TopoGeo tem registro de 480,00 m².

### Análise esperada

- ΔA_memorial_planta = -1,65 m² (-0,34%)
- ΔA_memorial_banco = 0,00 m² (0,00%)
- ΔA_planta_banco = -1,65 m² (-0,34%)

A skill deve registrar:

- **DIV-001** — D3 (Diferença geométrica relevante) entre planta e banco
- Origem provável: erro de fechamento da poligonal original, ou escala de plotagem imprecisa
- Recomendação: refazer levantamento ou solicitar retificação administrativa

### Resultado esperado

- 1 divergência ALTA (D3)
- Veredicto: **PRÉ-VALIDAÇÃO NÃO FAVORÁVEL — divergência geométrica relevante entre planta e base cadastral**
- Recomendação: revisar medição e/ou topologia

---

## Caso 4 — Diagnóstico de CAD problemático (hipotético)

**Imóvel**: Gleba Ribeirão do Cerrado
**Arquivo CAD**: `gleba_ribeirao.dwg` com 18 vértices
**Problemas introduzidos intencionalmente para teste**:

1. **Bloco órfão** — bloco `V-15` sem vértice correspondente na polilinha
2. **Código duplicado** — `V-08` aparece duas vezes (em coordenadas diferentes)
3. **Código incompatível** — bloco rotulado `V-09` mas com atributos de `V-12`
4. **Anotação fora do limite** — bloco `V-05` com offset de 4,20 m da polilinha (acima da tolerância)
5. **Sentido invertido** — roteiro textual no CAD segue ordem V-14→V-01, polilinha segue V-01→V-14

### Resultado esperado

A skill deve detectar 5 ocorrências:

| ID | Tipo | Severidade | Descrição |
|---|---|---|---|
| CAD-001 | Bloco órfão | ALTA | Bloco V-15 não corresponde a nenhum vértice |
| CAD-002 | Código duplicado | ALTA | V-08 em duas coordenadas |
| CAD-003 | Código incompatível | MÉDIA | Bloco rotulado V-09 com atributos de V-12 |
| CAD-006 | Anotação fora do limite | ALTA | Bloco V-05 com ΔXY = 4,20 m (tol. 0,50 m) |
| CAD-005 | Roteiro divergente da geometria | BAIXA | Sentido oposto entre tabela e polilinha |

### Veredicto

**PRÉ-VALIDAÇÃO NÃO FAVORÁVEL — múltiplas inconsistências no CAD**
**Recomendação**: revisar CAD antes de submeter a qualquer processo oficial

---

## Casos extremos a documentar em iterações futuras

- Imóvel com CRS misto (parte em SIRGAS2000, parte em WGS84)
- Memorial com coordenadas em graus-minutos-segundos + UTM no mesmo documento
- Planta sem layer de limite (apenas pontos cotados)
- GeoRural.gpkg corrompido parcialmente
- DWG criptografado ou em formato proprietário não conversível
- Memorial em imagem escaneada (sem OCR)
- Conflito entre duas bases (TopoGeo vs GeoRural divergentes)

Cada caso-limite deve gerar uma entrada neste documento com:

- Descrição do cenário
- Comportamento esperado da skill
- Limitações declaradas
- Recomendações de revisão

---

**Próximo passo**: [`referencias/normas.md`](./referencias/normas.md) — referencial normativo completo.
