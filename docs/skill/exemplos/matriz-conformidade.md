# Matriz de Conformidade — Exemplo Completo

> Matriz final consolidada para o caso **Sítio Felicidade**, integrando os 3 roteiros (Memorial, Planta, Banco hipotético).

---

## 1. Matriz de conformidade normativa (Bloco 8)

| Item | Valor observado | Tolerância/critério | Referência | Situação |
|---|---:|---:|---|---|
| Coordenadas E — vértice V-01 | 294.142,648 m | 0,001 m | Boa prática (SIRGAS2000/UTM) | OK |
| Coordenadas N — vértice V-01 | 9.122.335,960 m | 0,001 m | Boa prática | OK |
| σP vértice V-01 (artificial) | 0,050 m | ≤ 0,50 m (INCRA) | INCRA NTG 3ª ed. | OK |
| σP vértice V-04 (inacessível) | 0,120 m | ≤ 7,50 m (INCRA) | INCRA NTG 3ª ed. | OK |
| Azimute V-01 → V-02 (memorial) | 87°32'15" | ΔAz ≤ 30" para 1:12.000 | NBR 13133:2021 | OK |
| Distância V-01 → V-02 (memorial) | 152,34 m | ΔDist ≤ 0,013 m | NBR 13133:2021 (1:12.000) | OK |
| Fechamento memorial | n/a (construído) | ΣΔE ≈ 0, ΣΔN ≈ 0 | — | OK |
| Precisão relativa nominal | ~1:1.200.000 | ≥ 1:12.000 | NBR 13133:2021 | OK |
| Área (memorial) | 61.186,90 m² | — | — | (referência) |
| Área (planta) | 61.186,94 m² | ΔA ≤ 0,01% (boa prática) | — | OK (ΔA = 0,00007%) |
| Perímetro (memorial) | 1.205,72 m | — | — | (referência) |
| Perímetro (planta) | 1.205,68 m | ΔP ≤ 0,05 m | — | OK (ΔP = 0,04 m) |
| Sequência | V-01 a V-14 contínua | Sem gaps | — | OK |
| Sentido | Horário | Obrigatório SIGEF | Manual SIGEF 2ª ed. | OK |
| Código dos vértices | V-01 a V-14 | Únicos | — | OK |
| Limites | 4 tipos | Coerentes com INCRA NTG | INCRA NTG 3ª ed. | OK |
| Confrontantes | 4 trechos | Coerentes com base | — | OK (memorial × banco) |
| Confrontantes na planta | Parcial (só lista geral) | Esperado por trecho | — | DIVERGÊNCIA (D4) |
| Geometria | Válida (sem auto-interseção) | is_valid = True | OGC SFA | OK |
| Sobreposição | Nenhuma | — | — | OK |
| CRS | SIRGAS2000/UTM -25 | Esperado | Decreto 5.334/2005 | OK |
| Datum | SIRGAS2000 | Oficial Brasil | Decreto 5.334/2005 | OK |
| Projeção | UTM | Adequada | Decreto 5.334/2005 | OK |
| Meridiano central | 33°00' W | Compatível fuso -25 | IBGE | OK |
| Unidade | metros | Compatível com coords | — | OK |
| Responsável técnico | Erison Rosa de Oliveira Barros | Eng. Cartógrafo habilitado | CONFEA 1.073/2016 | OK |
| ART/RNP | 1805172549 38236-PE | Válida | CONFEA | OK |

## 2. Bloco de Divergências (Bloco 9)

### CRÍTICAS (nenhuma)

### ALTAS (nenhuma)

### MÉDIAS

| ID | Tipo | Trecho | Diferença | Tolerância | Norma | Observação |
|---|---|---|---:|---:|---|---|
| DIV-001 | D4 | GLOBAL | Confrontantes parciais na planta | — | — | Memorial cita 4 confrontantes específicos por trecho, planta cita apenas a lista geral |

### BAIXAS

| ID | Tipo | Trecho | Diferença | Tolerância | Norma | Observação |
|---|---|---|---:|---:|---|---|
| DIV-002 | D1 | V-02 | ΔE = 0,002 m | ≤ 0,005 m (arredondamento) | Boa prática | Memorial com 3 casas, planta com 3 casas, diferença residual de arredondamento |
| DIV-003 | D1 | V-09 | ΔN = 0,003 m | ≤ 0,005 m | Boa prática | Mesmo motivo |

## 3. Conclusão técnica (Bloco 10)

### Fato

O memorial descritivo do **Sítio Felicidade** declara área de 61.186,90 m², perímetro de 1.205,72 m, com 14 vértices em SIRGAS2000/UTM fuso -25. A planta DXF/DWG apresenta geometria compatível, com pequenas diferenças de arredondamento. O banco hipotético `GeoRural.gpkg` confirma os dados com desvios-padrão dentro dos limites INCRA.

### Cálculo

A skill calculou:

- **Área (planta)**: 61.186,94 m² → ΔA = 0,04 m² = 0,00007%
- **Perímetro (planta)**: 1.205,68 m → ΔP = 0,04 m = 0,003%
- **σP máximo**: 0,120 m (vértice V-04, inacessível, estação total) — bem abaixo do limite INCRA de 7,50 m
- **Azimute máximo ΔAz**: 0°00'03" — dentro da tolerância NBR 13133

### Critério

Aplicada a NBR 13133:2021 (precisão relativa ≥ 1:12.000) e a Norma Técnica INCRA 3ª ed. (precisão posicional por tipo de limite: artificial 0,50 m / natural 3,00 m / inacessível 7,50 m).

### Limitação

**Não foi fornecida base geoespacial (TopoGeo.gpkg ou GeoRural.gpkg) real** — a comparação com banco neste exemplo é **hipotética** (apenas ilustrativa). Em uma auditoria real, a ausência do banco limitaria a comparação a Memorial × Planta.

Outras limitações:

- Confrontantes da planta parcialmente declarados
- Sem acesso ao relatório de ajustamento da rede geodésica

### Respostas às 11 perguntas padrão

| # | Pergunta | Resposta |
|---:|---|---|
| 1 | Memorial e planta representam a mesma geometria? | **Sim.** ΔXY ≤ 0,005 m em todos os vértices (arredondamento). |
| 2 | Os vértices correspondem? | **Sim.** V-01 a V-14 da planta correspondem 1:1 aos do memorial. |
| 3 | As coordenadas correspondem dentro do critério adotado? | **Sim.** ΔXY máximo = 0,005 m (abaixo da tolerância de arredondamento). |
| 4 | Os azimutes correspondem? | **Sim.** ΔAz máximo = 0°00'03" (abaixo da tolerância NBR 13133). |
| 5 | As distâncias correspondem? | **Sim.** ΔDist máximo = 0,03 m (abaixo da precisão 1:12.000). |
| 6 | Área e perímetro correspondem? | **Sim.** ΔA = 0,00007%, ΔP = 0,003%. |
| 7 | Os confrontantes correspondem por trecho? | **Parcialmente.** Memorial cita 4 específicos, planta cita lista geral. |
| 8 | O fechamento é adequado ao critério aplicável? | **Sim.** Precisão relativa estimada ≥ 1:1.200.000 (compatível NBR 13133). |
| 9 | Existem divergências cadastrais? | **Nenhuma** (no exemplo hipotético, todas compatíveis). |
| 10 | Existem divergências normativas? | **Não.** CRS, datum, projeção, sentido horário, RT — todos conformes. |
| 11 | Quais limitações impedem conclusão mais ampla? | a) Banco geoespacial não fornecido em iteração real; b) Confrontantes parciais na planta; c) Sem relatório de ajustamento. |

### Veredicto

**PRÉ-VALIDAÇÃO FAVORÁVEL** — Memorial e planta são geometricamente compatíveis dentro dos critérios aplicáveis. Em iteração futura, recomenda-se incluir `GeoRural.gpkg` real para validação cadastral completa.

---

## 4. Resumo executivo

```
╔════════════════════════════════════════════════════════════════════╗
║                  AUDITORIA DE LIMITES TERRITORIAIS                ║
║                       Sítio Felicidade                            ║
╠════════════════════════════════════════════════════════════════════╣
║  Imóvel:        Sítio Felicidade                                  ║
║  Localização:   Bairro do Nobre, Paulista-PE                      ║
║  Área:          61.186,90 m² (memorial) / 61.186,94 m² (planta)    ║
║  Perímetro:     1.205,72 m (memorial) / 1.205,68 m (planta)        ║
║  Vértices:      14 (V-01 a V-14)                                  ║
║  CRS:           SIRGAS2000 / UTM fuso -25 / MC 33°W               ║
║  Modo:          HÍBRIDO (limitado — sem banco real)                ║
║  Norma princ.:  NBR 13133:2021 + INCRA NTG 3ª ed.                 ║
║  RT:            Erison R. O. Barros — RNP 1805172549 38236-PE     ║
╠════════════════════════════════════════════════════════════════════╣
║                     DIVERGÊNCIAS                                  ║
║  CRÍTICAS: 0                                                       ║
║  ALTAS:    0                                                       ║
║  MÉDIAS:   1 (D4 — confrontantes parciais na planta)              ║
║  BAIXAS:   2 (D1 — arredondamentos residuais)                     ║
╠════════════════════════════════════════════════════════════════════╣
║                     VEREDICTO                                      ║
║  ✅ PRÉ-VALIDAÇÃO FAVORÁVEL                                       ║
║  Recomendações:                                                    ║
║   1. Incluir GeoRural.gpkg ou TopoGeo.gpkg em próxima iteração    ║
║   2. Declarar confrontantes completos na planta                    ║
║   3. Anexar relatório de ajustamento da rede geodésica             ║
╚════════════════════════════════════════════════════════════════════╝
```

---

**Próximo passo**: [`../../FORMATO_SAIDA.md`](../../FORMATO_SAIDA.md) — estrutura completa do relatório formal.
