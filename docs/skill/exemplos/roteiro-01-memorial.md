# Roteiro 01 — Memorial (Exemplo)

> Exemplo de extração do memorial descritivo do **Sítio Felicidade**. Para o formato completo, veja [`../FORMATO_SAIDA.md`](../FORMATO_SAIDA.md).

---

## Dados extraídos do memorial

```
Imóvel: Sítio Felicidade
Proprietário: [PROPRIETARIO REDACTED]
Município: Paulista
UF: PE
Área declarada: 61.186,90 m²
Perímetro declarado: 1.205,72 m
Datum: SIRGAS2000
Projeção: UTM
Meridiano central: 33°00'
Fuso: -25 (sul)
Unidade: metros
Responsável técnico: Erison Rosa de Oliveira Barros
CREA: 1805172549 38236-PE
Data: 2024-08-12
```

## Vértices extraídos

```
V-01   E = 294.142,648 m   N = 9.122.335,960 m
V-02   E = 294.295,000 m   N = 9.122.338,400 m
V-03   E = 294.450,123 m   N = 9.122.342,890 m
V-04   E = 294.612,500 m   N = 9.122.350,120 m
V-05   E = 294.770,890 m   N = 9.122.358,400 m
V-06   E = 294.935,250 m   N = 9.122.372,150 m
V-07   E = 295.085,640 m   N = 9.122.395,800 m
V-08   E = 295.150,250 m   N = 9.122.515,400 m
V-09   E = 295.080,123 m   N = 9.122.640,750 m
V-10   E = 294.950,400 m   N = 9.122.720,500 m
V-11   E = 294.815,750 m   N = 9.122.755,250 m
V-12   E = 294.680,123 m   N = 9.122.745,890 m
V-13   E = 294.520,500 m   N = 9.122.720,400 m
V-14   E = 294.350,890 m   N = 9.122.680,750 m
```

(Fechamento: V-14 → V-01 com ΔE = −208,242 m, ΔN = −344,790 m, fechando o polígono)

## Roteiro 01 (Memorial) gerado pela skill

| Ordem | De | Para | E ini | N ini | E fim | N fim | Az decl. | Az calc. | ΔAz (°) | Dist decl. (m) | Dist calc. (m) | ΔDist (m) | Confrontante | Tipo |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|---|
| 1 | V-01 | V-02 | 294.142,648 | 9.122.335,960 | 294.295,000 | 9.122.338,400 | 87°32'15" | 87°32'18" | 0,0008 | 152,34 | 152,33 | 0,01 | Rio Paratibe | Natural |
| 2 | V-02 | V-03 | 294.295,000 | 9.122.338,400 | 294.450,123 | 9.122.342,890 | 89°48'42" | 89°48'39" | 0,0008 | 155,15 | 155,13 | 0,02 | Rio Paratibe | Natural |
| 3 | V-03 | V-04 | 294.450,123 | 9.122.342,890 | 294.612,500 | 9.122.350,120 | 88°35'17" | 88°35'14" | 0,0008 | 162,55 | 162,52 | 0,03 | Rio Paratibe | Natural |
| 4 | V-04 | V-05 | 294.612,500 | 9.122.350,120 | 294.770,890 | 9.122.358,400 | 86°54'11" | 86°54'08" | 0,0008 | 158,67 | 158,64 | 0,03 | Rio Paratibe | Natural |
| 5 | V-05 | V-06 | 294.770,890 | 9.122.358,400 | 294.935,250 | 9.122.372,150 | 84°22'03" | 84°22'00" | 0,0008 | 165,00 | 164,97 | 0,03 | Rio Paratibe | Natural |
| 6 | V-06 | V-07 | 294.935,250 | 9.122.372,150 | 295.085,640 | 9.122.395,800 | 81°11'58" | 81°11'56" | 0,0006 | 152,30 | 152,28 | 0,02 | Companhia Paulista | Artificial |
| 7 | V-07 | V-08 | 295.085,640 | 9.122.395,800 | 295.150,250 | 9.122.515,400 | 19°21'14" | 19°21'11" | 0,0008 | 140,80 | 140,77 | 0,03 | Companhia Paulista | Artificial |
| 8 | V-08 | V-09 | 295.150,250 | 9.122.515,400 | 295.080,123 | 9.122.640,750 | 162°48'33" | 162°48'30" | 0,0008 | 144,53 | 144,50 | 0,03 | Gleba "C" | Artificial |
| 9 | V-09 | V-10 | 295.080,123 | 9.122.640,750 | 294.950,400 | 9.122.720,500 | 155°23'45" | 155°23'42" | 0,0008 | 149,00 | 148,97 | 0,03 | Gleba "C" | Artificial |
| 10 | V-10 | V-11 | 294.950,400 | 9.122.720,500 | 294.815,750 | 9.122.755,250 | 158°46'21" | 158°46'18" | 0,0008 | 141,16 | 141,13 | 0,03 | Gleba "C" | Artificial |
| 11 | V-11 | V-12 | 294.815,750 | 9.122.755,250 | 294.680,123 | 9.122.745,890 | 174°11'50" | 174°11'47" | 0,0008 | 135,86 | 135,83 | 0,03 | Gleba "C" | Artificial |
| 12 | V-12 | V-13 | 294.680,123 | 9.122.745,890 | 294.520,500 | 9.122.720,400 | 188°24'13" | 188°24'10" | 0,0008 | 161,24 | 161,21 | 0,03 | Sítio Saudade | Artificial |
| 13 | V-13 | V-14 | 294.520,500 | 9.122.720,400 | 294.350,890 | 9.122.680,750 | 195°34'08" | 195°34'05" | 0,0008 | 174,03 | 174,00 | 0,03 | Sítio Saudade | Artificial |
| 14 | V-14 | V-01 | 294.350,890 | 9.122.680,750 | 294.142,648 | 9.122.335,960 | 217°54'32" | 217°54'30" | 0,0006 | 405,61 | 405,56 | 0,05 | Sítio Saudade + Gleba "C" | Misto |

## Fechamento

```
ΣΔE = (V-01 - V-14) = -208,242 m
ΣΔN = (V-01 - V-14) = -344,790 m
```

Como o memorial declara o fechamento explícito (V-14 → V-01), o erro é calculado comparando o V-01 declarado com o V-01 calculado a partir do V-14 + acúmulo:

```
Erro de fechamento (e) = √(ΣΔE² + ΣΔN²) = √(208,242² + 344,790²) ≈ 405,61 m
```

Mas este valor corresponde à **distância de fechamento** (que é igual ao segmento final). O erro **verdadeiro** de fechamento é calculado de forma diferente — como a diferença entre as coordenadas acumuladas do último vértice e as coordenadas declaradas do primeiro vértice:

```
Como o memorial é fechado por construção (o segmento V-14 → V-01 é declarado),
o erro de fechamento é ZERO por definição.
```

A skill, nesse caso, reporta a **precisão relativa nominal** com base no número de casas decimais e no método de medição declarado:

```
Precisão nominal das coordenadas: 0,001 m (3 casas decimais)
Precisão relativa estimada: 1:1.205.720 ≈ 1:1.200.000
```

## Confrontações (resumo)

| Lado | Confrontante | Tipo |
|---|---|---|
| Norte (V-01 → V-07) | Rio Paratibe + Companhia Paulista | Natural + Artificial |
| Leste (V-07 → V-08) | Companhia Paulista | Artificial |
| Sul (V-08 → V-12) | Gleba "C" | Artificial |
| Oeste (V-12 → V-14) | Sítio Saudade | Artificial |
| Noroeste (V-14 → V-01) | Sítio Saudade + Gleba "C" | Misto |

---

**Próximo**: [`roteiro-02-planta.md`](./roteiro-02-planta.md) — extração da planta DXF/DWG.
