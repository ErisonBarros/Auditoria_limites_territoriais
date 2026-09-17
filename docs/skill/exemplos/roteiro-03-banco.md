# Roteiro 03 — Banco (Exemplo)

> Exemplo de extração do `GeoRural.gpkg` ou `TopoGeo.gpkg`. Para este caso (`Sítio Felicidade`), nenhuma base geoespacial foi fornecida — apresentamos um exemplo **hipotético** baseado em `GeoRural.gpkg` para ilustração.

---

## Cenário hipotético

Suponha que, em uma iteração futura, o usuário forneça um `GeoRural.gpkg` correspondente ao Sítio Felicidade, certificado ou em processo de certificação SIGEF.

## 3.1 Fonte: GeoRural.gpkg

### Camada: `parcela`

| Atributo | Valor |
|---|---|
| `nome` | Sítio Felicidade |
| `denominacao` | Sítio Felicidade |
| `cpf_cnpj` | [CPF REDACTED] |
| `sncr` | [SNCR REDACTED] |
| `matricula` | [MATRICULA REDACTED] |
| `cod_cartorio` | [CNS REDACTED] |
| `municipio` | Paulista |
| `uf` | PE |
| `natureza` | Rural |
| `situacao` | (Pendente de certificação / Certificada) |
| `data` | 2024-08-12 |

### Camada: `vertice`

| Ordem | Code | E (m) | N (m) | Tipo | Método | σX (m) | σY (m) | σZ (m) |
|---:|---|---:|---:|---|---|---:|---:|---:|
| 1 | V-01 | 294.142,648 | 9.122.335,960 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 2 | V-02 | 294.295,000 | 9.122.338,400 | M | GNSS/RTK | 0,03 | 0,03 | 0,04 |
| 3 | V-03 | 294.450,123 | 9.122.342,890 | M | GNSS/RTK | 0,04 | 0,04 | 0,05 |
| 4 | V-04 | 294.612,500 | 9.122.350,120 | P | Estação Total | 0,08 | 0,09 | 0,15 |
| 5 | V-05 | 294.770,890 | 9.122.358,400 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 6 | V-06 | 294.935,250 | 9.122.372,150 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 7 | V-07 | 295.085,640 | 9.122.395,800 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 8 | V-08 | 295.150,250 | 9.122.515,400 | P | Estação Total | 0,07 | 0,08 | 0,12 |
| 9 | V-09 | 295.080,123 | 9.122.640,750 | P | Estação Total | 0,07 | 0,08 | 0,12 |
| 10 | V-10 | 294.950,400 | 9.122.720,500 | P | Estação Total | 0,06 | 0,07 | 0,10 |
| 11 | V-11 | 294.815,750 | 9.122.755,250 | P | Estação Total | 0,07 | 0,08 | 0,12 |
| 12 | V-12 | 294.680,123 | 9.122.745,890 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 13 | V-13 | 294.520,500 | 9.122.720,400 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |
| 14 | V-14 | 294.350,890 | 9.122.680,750 | M | GNSS/RTK | 0,03 | 0,04 | 0,05 |

### Camada: `limite`

| Trecho | Tipo | Confrontante | CNS | Matrícula | QRCode |
|---|---|---|---|---|---|
| V-01 → V-07 | Natural | Rio Paratibe | — | — | — |
| V-07 → V-08 | Artificial | Companhia Paulista | [CNS REDACTED] | [MATRICULA REDACTED] | (qrcode) |
| V-08 → V-12 | Artificial | Gleba "C" | [CNS REDACTED] | [MATRICULA REDACTED] | (qrcode) |
| V-12 → V-14 | Artificial | Sítio Saudade | [CNS REDACTED] | [MATRICULA REDACTED] | (qrcode) |
| V-14 → V-01 | Misto | Sítio Saudade + Gleba "C" | — | — | — |

## 3.2 Cálculo de precisões no banco

Para cada vértice, a skill calcula `σP = √(σX² + σY²)`:

| Vértice | σX (m) | σY (m) | σP (m) | Tipo de limite esperado | Tolerância (3×) | Situação |
|---|---:|---:|---:|---|---:|---|
| V-01 | 0,03 | 0,04 | 0,050 | Artificial | 1,50 m | OK |
| V-02 | 0,03 | 0,03 | 0,042 | Natural | 9,00 m | OK |
| V-03 | 0,04 | 0,04 | 0,057 | Natural | 9,00 m | OK |
| V-04 | 0,08 | 0,09 | 0,120 | Inacessível | 22,50 m | OK |
| V-05 | 0,03 | 0,04 | 0,050 | Natural | 9,00 m | OK |
| V-06 | 0,03 | 0,04 | 0,050 | Natural | 9,00 m | OK |
| V-07 | 0,03 | 0,04 | 0,050 | Artificial | 1,50 m | OK |
| V-08 | 0,07 | 0,08 | 0,106 | Artificial | 1,50 m | OK |
| V-09 | 0,07 | 0,08 | 0,106 | Artificial | 1,50 m | OK |
| V-10 | 0,06 | 0,07 | 0,092 | Artificial | 1,50 m | OK |
| V-11 | 0,07 | 0,08 | 0,106 | Artificial | 1,50 m | OK |
| V-12 | 0,03 | 0,04 | 0,050 | Artificial | 1,50 m | OK |
| V-13 | 0,03 | 0,04 | 0,050 | Artificial | 1,50 m | OK |
| V-14 | 0,03 | 0,04 | 0,050 | Misto | (varia) | OK |

Todos os vértices estão **dentro** das tolerâncias comparativas aplicáveis.

## 3.3 Pré-validação SIGEF (Roteiro 03)

| Item | Status | Observação |
|---|:---:|---|
| Unicidade de código | ✓ | V-01 a V-14 sem repetição |
| Sequência contínua | ✓ | V-01 a V-14, sem gaps |
| Sentido horário | ✓ | Verificado pela ordem dos vértices |
| Vértice inicial declarado | ✓ | V-01 |
| Sistema SIRGAS2000 | ✓ | CRS SIRGAS2000/UTM fuso -25 |
| Geometria válida | ✓ | Sem auto-interseções |
| Continuidade | ✓ | Sem segmentos ausentes |
| Fechamento | ✓ | V-14 → V-01 declarado |
| Tipos de limite declarados | ✓ | 4 tipos: Natural, Artificial, Inacessível, Misto |
| Métodos de posicionamento | ✓ | GNSS/RTK e Estação Total (ambos aceitos pela NTG) |
| Confrontantes declarados | ✓ | 4 confrontantes com CNS/matrícula |
| Matrícula declarada | ✓ | Matrícula consistente |
| Posição dos vértices | ✓ | Coerente |
| Compatibilidade dos dados | ✓ | Memorial × GeoRural compatíveis |

## 3.4 Roteiro 03 (Banco) — Tabela final

| Ordem | Code | E (m) | N (m) | Tipo | Método | σX | σY | σP |
|---:|---|---:|---:|---|---|---:|---:|---:|
| 1 | V-01 | 294.142,648 | 9.122.335,960 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 2 | V-02 | 294.295,000 | 9.122.338,400 | M | GNSS/RTK | 0,03 | 0,03 | 0,042 |
| 3 | V-03 | 294.450,123 | 9.122.342,890 | M | GNSS/RTK | 0,04 | 0,04 | 0,057 |
| 4 | V-04 | 294.612,500 | 9.122.350,120 | P | Estação Total | 0,08 | 0,09 | 0,120 |
| 5 | V-05 | 294.770,890 | 9.122.358,400 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 6 | V-06 | 294.935,250 | 9.122.372,150 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 7 | V-07 | 295.085,640 | 9.122.395,800 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 8 | V-08 | 295.150,250 | 9.122.515,400 | P | Estação Total | 0,07 | 0,08 | 0,106 |
| 9 | V-09 | 295.080,123 | 9.122.640,750 | P | Estação Total | 0,07 | 0,08 | 0,106 |
| 10 | V-10 | 294.950,400 | 9.122.720,500 | P | Estação Total | 0,06 | 0,07 | 0,092 |
| 11 | V-11 | 294.815,750 | 9.122.755,250 | P | Estação Total | 0,07 | 0,08 | 0,106 |
| 12 | V-12 | 294.680,123 | 9.122.745,890 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 13 | V-13 | 294.520,500 | 9.122.720,400 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |
| 14 | V-14 | 294.350,890 | 9.122.680,750 | M | GNSS/RTK | 0,03 | 0,04 | 0,050 |

## 3.5 Validação histórica (se houver camadas hist_*)

Quando a auditoria histórica é solicitada, a skill consulta `hist_parcela`, `hist_limite`, `hist_vertice` para identificar mudanças ao longo do tempo:

- Mudanças de denominação
- Retificações de área
- Desmembramentos ou remembramentos
- Substituição de vértices (ex.: marco de concreto substituído por marco de aço)

(Para o caso hipotético, sem dados históricos disponíveis.)

---

**Próximo**: [`matriz-conformidade.md`](./matriz-conformidade.md) — matriz final consolidada.
