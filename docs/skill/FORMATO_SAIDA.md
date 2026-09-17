# Formato de Saída — Skill `auditoria-limites-territoriais`

> Estrutura completa do relatório gerado pela skill. Para o fluxo, veja [`COMO_FUNCIONA.md`](./COMO_FUNCIONA.md). Para o quickstart, veja [`GUIA_RAPIDO.md`](./GUIA_RAPIDO.md).

---

## Visão geral

O relatório sempre contém **11 blocos numerados**, conforme seção 37 da [`SKILL.md`](./SKILL.md):

| # | Bloco | Obrigatório | Condição |
|---|---|:---:|---|
| 1 | Resumo da auditoria | ✓ | Sempre |
| 2 | Roteiro 01 — Memorial | ✓ | Sempre |
| 3 | Roteiro 02 — Planta | ✓ | Sempre |
| 4 | Roteiro 03 — Banco | quando houver TopoGeo/GeoRural | |
| 5 | Comparação Memorial × Planta × Banco | ✓ | Sempre |
| 6 | Confrontantes | ✓ | Sempre |
| 7 | Fechamento | ✓ | Sempre |
| 8 | Matriz normativa | ✓ | Sempre |
| 9 | Divergências | ✓ | Sempre |
| 10 | Conclusão técnica | ✓ | Sempre |
| 11 | Anexos (JSON interno) | ✓ | Sempre |

---

## Bloco 1 — Resumo da auditoria

```
## RESUMO DA AUDITORIA

| Item | Valor |
|---|---|
| Finalidade | [Regularização fundiária / Certificação SIGEF / Retificação de área / Outro] |
| Modo de trabalho | [TOPOGRÁFICO / CADASTRAL / GEORREFERENCIAMENTO_RURAL / CERTIFICACAO_SIGEF / HÍBRIDO / INDETERMINADO] |
| Arquivos analisados | [lista de arquivos] |
| Base geoespacial | [TopoGeo.gpkg / GeoRural.gpkg / nenhuma] |
| CRS original memorial | [SIRGAS2000/UTM fuso -25 / outro] |
| CRS original planta | [idêntico / outro] |
| CRS de comparação | [sistema métrico comum adotado] |
| Norma principal aplicável | [NBR 13133:2021 / Norma Técnica INCRA 3ª ed. / Manual SIGEF 2ª ed.] |
| Área declarada (memorial) | [valor] m² |
| Área calculada (planta) | [valor] m² |
| Δ Área | [valor] m² ([percentual]%) |
| Perímetro declarado (memorial) | [valor] m |
| Perímetro calculado (planta) | [valor] m |
| Δ Perímetro | [valor] m ([percentual]%) |
| Quantidade de vértices | [n] |
| Quantidade de segmentos | [n] |
| Divergências detectadas | [n CRÍTICAS, n ALTAS, n MÉDIAS, n BAIXAS] |
```

---

## Bloco 2 — Roteiro 01 — Memorial

Tabela completa extraída do memorial descritivo:

```
## ROTEIRO 01 — MEMORIAL

| Ordem | De | Para | E inicial | N inicial | E final | N final | Az decl. | Az calc. | ΔAz | Dist decl. | Dist calc. | ΔDist | Confrontante | Tipo limite |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|---|
| 1 | V-01 | V-02 | 294.142,648 | 9.122.335,960 | ... | ... | 87°32'15" | 87°32'18" | 0°00'03" | 152,34 | 152,33 | 0,01 | Rio Paratibe | Natural |
| 2 | V-02 | V-03 | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |
| ... |
```

**Colunas**:

- **Ordem**: sequência numérica do segmento
- **De / Para**: códigos dos vértices inicial e final
- **E / N inicial e final**: em notação original do memorial
- **Az decl.**: azimute declarado (DMS quando aplicável)
- **Az calc.**: azimute calculado pela skill a partir das coordenadas
- **ΔAz**: diferença circular entre declarado e calculado
- **Dist decl.**: distância declarada no memorial
- **Dist calc.**: distância calculada pela skill
- **ΔDist**: diferença linear
- **Confrontante**: confrontante declarado no trecho
- **Tipo limite**: tipo de limite declarado (artificial/natural/inacessível/curso d'água/etc.)

---

## Bloco 3 — Roteiro 02 — Planta

Tabela extraída da geometria da planta. Inclui sub-abas para módulo CAD quando a planta é DXF/DWG:

```
## ROTEIRO 02 — PLANTA

### 2.1 Geometria efetiva

| Ordem | De | Para | E inicial | N inicial | E final | N final | Az calc. | Dist calc. | Layer | Entidade | Handle |
|---:|---|---|---:|---:|---:|---:|---:|---:|---|---|---|
| 1 | V-01 | V-02 | 294.142,65 | 9.122.335,96 | 294.295,00 | 9.122.338,40 | 87°32'18" | 152,33 | LIMITE | LWPOLYLINE | 1F2 |
| ... |

### 2.2 Roteiro textual do CAD (quando existir)

| Ordem | De | Para | E | N | Az | Dist | Confrontante | Fonte |
|---:|---|---|---:|---:|---:|---:|---|---|
| 1 | V-01 | V-02 | 294.142,65 | 9.122.335,96 | 87°32'15" | 152,34 | Rio Paratibe | Tabela TOPO_ROTEIRO |
| ... |

### 2.3 Blocos e anotações (quando existirem)

| Código | Inserção E | Inserção N | Atributo E | Atributo N | Layer | ΔXY inserção–polilinha |
|---|---:|---:|---:|---:|---|---:|
| V-01 | 294.142,65 | 9.122.335,96 | 294.142,65 | 9.122.335,96 | VERTICES | 0,00 |
| ... |
```

### Matriz de consistência CAD

Quando a planta é DXF/DWG, é gerada a matriz da seção 43:

```
| Vértice | Polilinha | Roteiro CAD | Bloco/Anotação | Código | ΔXY | Situação |
|---|:---:|:---:|:---:|---|---:|---|
| V-01 | ✓ | ✓ | ✓ | V-01 | 0,00 | Compatível |
| V-02 | ✓ | ✓ | ✓ | V-02 | 0,05 | Compatível |
| V-03 | ✓ | ✗ (ausente) | ✓ (órfão) | V-03 | 0,12 | CAD-001 — Bloco órfão |
| ... |
```

---

## Bloco 4 — Roteiro 03 — Banco (quando houver)

```
## ROTEIRO 03 — BASE GEOESPACIAL

### 4.1 Fonte: TopoGeo.gpkg / GeoRural.gpkg

#### Camada: property_area_a / parcela

| Atributo | Valor |
|---|---|
| Property / Denominação | [valor] |
| Registry / Matrícula | [valor] |
| Owner / Proprietário | [valor] |
| Address / Endereço | [valor] |
| County / Município | [valor] |
| State / UF | [valor] |
| Survey_date / Data | [valor] |
| Surveyor / Responsável | [valor] |
| Area declarada | [valor] m² |
| Perímetro declarado | [valor] m |
| CRS | [SIRGAS2000/UTM -25] |

#### Camada: limit_point_p / vertice

| Ordem | Code | E | N | Tipo | Método | σX | σY | σZ |
|---:|---|---:|---:|---|---|---:|---:|---:|
| 1 | V-01 | 294.142,648 | 9.122.335,960 | M (Marco) | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| ... |

#### Camada: boundary_element_l / limite

| Trecho | Tipo | Confrontante | CNS | Matrícula | Autorizável |
|---|---|---|---|---|---|
| V-01 → V-07 | Natural | Rio Paratibe | — | — | ANA |
| V-07 → V-12 | Artificial | Gleba C | [valor] | [valor] | [valor] |
| ... |
```

---

## Bloco 5 — Comparação Memorial × Planta × Banco

```
## COMPARAÇÃO

| Trecho | Fonte | E ini | N ini | E fim | N fim | Az | Dist | Confrontante | Limite |
|---|---|---:|---:|---:|---:|---:|---:|---|---|
| V-01→V-02 | Memorial | 294.142,648 | 9.122.335,960 | ... | ... | 87°32'15" | 152,34 | Rio Paratibe | Natural |
| V-01→V-02 | Planta | 294.142,65 | 9.122.335,96 | ... | ... | 87°32'18" | 152,33 | (não declarada) | (não declarado) |
| V-01→V-02 | Banco | 294.142,648 | 9.122.335,960 | ... | ... | — | — | Rio Paratibe | Natural |
| **ΔXY(mem-planta)** | | | | **0,002** | | | | | |
| **ΔXY(mem-banco)** | | | | **0,000** | | | | | |
| **ΔAz(mem-planta)** | | | | | | **0°00'03"** | | | |
| **Resultado** | | | | | | **Compatível** (abaixo da tolerância de 1,50 m) | | | |
```

---

## Bloco 6 — Confrontantes

```
## CONFRONTANTES

| Trecho | Memorial | Planta | Banco | Situação |
|---|---|---|---|---|
| V-01 → V-07 | Rio Paratibe | (não declarado) | Rio Paratibe | D4 — Divergência documental (planta) |
| V-07 → V-12 | Gleba C | Gleba C | Gleba C | Compatível |
| V-12 → V-14 | Sítio Saudade | Sítio Saudade | Sítio Saudade | Compatível |
| ... |
```

---

## Bloco 7 — Fechamento

```
## FECHAMENTO

### Memorial
- ΣΔE = 0,012 m
- ΣΔN = 0,008 m
- Erro linear e = 0,014 m
- Perímetro = 1.205,72 m
- Precisão relativa = 1:86.123 (compatível com NBR 13133 classe I)

### Planta
- ΣΔE = 0,003 m
- ΣΔN = 0,002 m
- Erro linear e = 0,004 m
- Perímetro = 1.205,68 m
- Precisão relativa = 1:301.420

### Banco
- ΣΔE = 0,000 m (fechamento perfeito — base processada)
- ΣΔN = 0,000 m
- Erro linear e = 0,000 m
- Perímetro = 1.205,72 m (do cadastro)

### Interpretação
A precisão do memorial é compatível com NBR 13133. A precisão da planta é superior à do memorial — comportamento esperado quando o CAD é produzido a partir de uma base já processada.
```

---

## Bloco 8 — Matriz normativa

```
## MATRIZ NORMATIVA

| Item | Valor observado | Tolerância/critério | Referência | Situação |
|---|---:|---:|---|---|
| Coordenadas E,V-01 | 294.142,648 m | 0,001 m | Decreto 5.334/2005 (precisão DGPS) | OK |
| Coordenadas N,V-01 | 9.122.335,960 m | 0,001 m | Decreto 5.334/2005 | OK |
| σP vértice V-01 | 0,05 m | ≤ 0,50 m (artificial) | INCRA NTG 3ª ed. | OK |
| Azimute V-01→V-02 | 87°32'15" declarado | Δ ≤ 30" (1:12.000 × 152,34 m) | NBR 13133:2021 | OK |
| Distância V-01→V-02 | 152,34 m declarado | Δ ≤ 0,013 m (1:12.000) | NBR 13133:2021 | OK |
| Fechamento | 1:86.123 | ≥ 1:12.000 | NBR 13133:2021 | OK |
| Precisão relativa | 86.123 | ≥ 12.000 | NBR 13133:2021 | OK |
| Área | 61.186,90 m² | Δ ≤ 0,01% | Boa prática topográfica | OK |
| Perímetro | 1.205,72 m | Δ ≤ 0,05 m | Boa prática topográfica | OK |
| Sequência | V-01 a V-14 contínua | Sem gaps | — | OK |
| Sentido | Horário | Obrigatório em SIGEF | Manual SIGEF 2ª ed. | OK |
| Código dos vértices | V-01 a V-14 | Únicos, sem repetição | — | OK |
| Limites | Natural/Artificial | Classificação compatível com base | INCRA NTG 3ª ed. | OK |
| Confrontantes | 4 trechos | Coerentes com base | — | OK |
| Geometria | Válida (sem auto-interseção) | is_valid = True | OGC SFA | OK |
| Sobreposição | Nenhuma | — | — | OK |
```

---

## Bloco 9 — Divergências

```
## DIVERGÊNCIAS

### CRÍTICAS (nenhuma)

### ALTAS (nenhuma)

### MÉDIAS

| ID | Tipo | Trecho | Diferença | Tolerância | Norma | Observação |
|---|---|---|---:|---:|---|---|
| DIV-001 | D4 | V-03→V-04 | Confrontante não declarado na planta | — | — | Memorial cita "Gleba C", planta não declara |

### BAIXAS

| ID | Tipo | Trecho | Diferença | Tolerância | Norma | Observação |
|---|---|---|---:|---:|---|---|
| DIV-002 | D1 | V-02→V-03 | ΔE = 0,002 m | ≤ 0,005 m (arredondamento) | Boa prática | Coordenadas da planta com 2 casas vs 3 casas do memorial |
```

---

## Bloco 10 — Conclusão técnica

A conclusão responde a 11 perguntas padrão (seção 38 da SKILL):

```
## CONCLUSÃO TÉCNICA

### Fato
O memorial descritivo do imóvel X declara uma área de 61.186,90 m² e perímetro de 1.205,72 m, com 14 vértices em SIRGAS2000/UTM fuso -25. A planta DXF/DWG apresenta geometria compatível, com pequenas diferenças de arredondamento. [continua...]

### Cálculo
A skill calculou área de 61.186,94 m² (ΔA = 0,04 m² = 0,00007%) e perímetro de 1.205,68 m (ΔP = 0,04 m = 0,003%). O erro de fechamento do memorial é 1:86.123, superior ao mínimo de 1:12.000 da NBR 13133:2021.

### Critério
Aplicada a NBR 13133:2021 para o levantamento topográfico e a Norma Técnica INCRA 3ª ed. para o tipo de limite (artificial: 0,50 m / natural: 3,00 m / inacessível: 7,50 m).

### Limitação
Não foi fornecida base geoespacial (TopoGeo/GeoRural) para Roteiro 03. A comparação limita-se a Memorial × Planta.

### Respostas às 11 perguntas padrão

1. **Memorial e planta representam a mesma geometria?**
   Sim. As coordenadas diferem apenas por arredondamento (≤ 0,005 m em todos os vértices).

2. **Os vértices correspondem?**
   Sim. V-01 a V-14 da planta correspondem 1:1 aos do memorial.

3. **As coordenadas correspondem dentro do critério adotado?**
   Sim. ΔXY máximo = 0,004 m, dentro da tolerância de arredondamento.

4. **Os azimutes correspondem?**
   Sim. ΔAz máximo = 0°00'03", dentro da tolerância angular da NBR 13133 para a classe do levantamento.

5. **As distâncias correspondem?**
   Sim. ΔDist máximo = 0,01 m, dentro da precisão linear 1:12.000.

6. **Área e perímetro correspondem?**
   Sim. ΔA = 0,00007%, ΔP = 0,003%, dentro de qualquer critério razoável.

7. **Os confrontantes correspondem por trecho?**
   Parcialmente. Memorial cita 4 confrontantes, planta cita apenas 2.

8. **O fechamento é adequado ao critério aplicável?**
   Sim. Precisão relativa 1:86.123 > 1:12.000 (NBR 13133:2021).

9. **Existem divergências cadastrais?**
   Não aplicável (sem base geoespacial fornecida).

10. **Existem divergências normativas?**
    Não. CRS, datum, projeção, sentido horário e demais requisitos estão conformes.

11. **Quais limitações impedem conclusão mais ampla?**
    a) Ausência de TopoGeo.gpkg / GeoRural.gpkg
    b) Confrontantes da planta parcialmente declarados
    c) Sem acesso ao relatório de ajustamento da rede geodésica

### Veredicto
**PRÉ-VALIDAÇÃO FAVORÁVEL** — Memorial e planta são geometricamente compatíveis dentro dos critérios aplicáveis. Recomenda-se complementar com base geoespacial para validação cadastral completa.
```

---

## Bloco 11 — Anexos (JSON interno)

Conforme seção 36 da SKILL, cada segmento é representado internamente como:

```json
{
  "ordem": 1,
  "vertice_inicial": "V-01",
  "vertice_final": "V-02",
  "e_inicial": 294142.648,
  "n_inicial": 9122335.960,
  "e_final": 294295.000,
  "n_final": 9122338.400,
  "azimute_declarado": 87.537500,
  "distancia_declarada": 152.34,
  "azimute_calculado": 87.538333,
  "distancia_calculada": 152.33,
  "erro_azimute": 0.000833,
  "erro_distancia": 0.01,
  "confrontante": "Rio Paratibe",
  "tipo_limite": "Natural",
  "fonte": "MEMORIAL p.3"
}
```

E cada divergência:

```json
{
  "id": "DIV-001",
  "tipo": "CONFRONTANTE",
  "segmento": "V-03-V-04",
  "fonte_a": "MEMORIAL",
  "fonte_b": "PLANTA",
  "valor_a": "Gleba C",
  "valor_b": null,
  "diferenca": null,
  "tolerancia": null,
  "norma": null,
  "item_normativo": null,
  "resultado": "D4 — Divergência documental (planta não declara)",
  "observacao": "Verificar tabela de confrontações no arquivo CAD"
}
```

Quando a planta é DXF/DWG, também há registros `CAD-001` a `CAD-010`:

```json
{
  "id": "CAD-001",
  "tipo": "BLOCO_ORFAO",
  "arquivo": "SITIO_FELICIDADE_SANITIZADO.dwg",
  "layer": "VERTICES",
  "entidade": "INSERT (1A3)",
  "codigo": "V-12",
  "coordenada": {"E": 294850.123, "N": 9122120.456},
  "referencia": "Vértice V-12 da polilinha em (294850.118, 9122120.451)",
  "diferenca": 0.007,
  "tolerancia": 0.50,
  "situacao": "PRÓXIMO",
  "observacao": "Bloco dentro da tolerância de proximidade, mas com offset de 7 mm"
}
```

---

**Próximo passo**: [`EXEMPLOS.md`](./EXEMPLOS.md) — 4 estudos de caso com outputs reais.
