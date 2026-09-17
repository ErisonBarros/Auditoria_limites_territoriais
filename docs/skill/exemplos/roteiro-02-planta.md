# Roteiro 02 — Planta (Exemplo)

> Exemplo de extração da planta DXF/DWG do **Sítio Felicidade**. Para o módulo CAD completo, veja [`../referencias/modulos-cad.md`](../referencias/modulos-cad.md).

---

## Dados extraídos da planta

```
Arquivo: SITIO_FELICIDADE_SANITIZADO.dwg
Formato: AutoCAD R2000 (AC1015)
CRS: SIRGAS2000 / UTM fuso -25 / MC 33°W
Unidade: metros (assumida — não declarada explicitamente)
Total de layers: 8
Total de entidades: 412
```

## Layers identificadas

| Layer | Tipo conteúdo | Qtd entities |
|---|---|---:|
| `LIMITE` | LWPOLYLINE fechada do perímetro | 1 (14 vértices) |
| `VERTICES` | INSERT de blocos V-01 a V-14 | 14 |
| `COTAS` | TEXT com azimutes e distâncias | 14 |
| `QUADRO_AREAS` | Tabela com área, perímetro, RT | 1 (tabela) |
| `CARIMBO_DADOS` | Sanitizado (sem dados pessoais) | 4 (TEXT + retângulos) |
| `TITULO` | Texto do título da prancha | 2 |
| `NORTENORTE` | Seta de norte magnético | 2 |
| `QUADRO` | Moldura e greide | 4 (LINE + TEXT) |

## 2.1 Geometria efetiva do limite

A skill identificou a `LWPOLYLINE` na layer `LIMITE` como candidato a perímetro:

```
Handle: 1F2
Layer: LIMITE
Fechada: Sim
Vértices: 14
```

### Vértices da polilinha (extraídos da geometria)

| Ordem | Código (inferido) | E (m) | N (m) |
|---:|---|---:|---:|
| 1 | V-01 | 294.142,648 | 9.122.335,960 |
| 2 | V-02 | 294.295,000 | 9.122.338,400 |
| 3 | V-03 | 294.450,123 | 9.122.342,890 |
| 4 | V-04 | 294.612,500 | 9.122.350,120 |
| 5 | V-05 | 294.770,890 | 9.122.358,400 |
| 6 | V-06 | 294.935,250 | 9.122.372,150 |
| 7 | V-07 | 295.085,640 | 9.122.395,800 |
| 8 | V-08 | 295.150,250 | 9.122.515,400 |
| 9 | V-09 | 295.080,123 | 9.122.640,750 |
| 10 | V-10 | 294.950,400 | 9.122.720,500 |
| 11 | V-11 | 294.815,750 | 9.122.755,250 |
| 12 | V-12 | 294.680,123 | 9.122.745,890 |
| 13 | V-13 | 294.520,500 | 9.122.720,400 |
| 14 | V-14 | 294.350,890 | 9.122.680,750 |

### Métricas

```
Comprimento total (perímetro): 1.205,68 m
Área: 61.186,94 m²
Fechamento: Válido (primeiro vértice = último vértice)
Validade topológica: Válida (sem auto-interseções)
Sentido: Horário
CRS detectado: SIRGAS2000/UTM fuso -25 (inferido pelos valores numéricos)
```

## 2.2 Roteiro textual do CAD

A layer `COTAS` contém TEXT com azimutes e distâncias por trecho:

| Trecho | Azimute (texto) | Distância (texto) |
|---|---:|---:|
| V-01 → V-02 | 87°32'15" | 152,34 |
| V-02 → V-03 | 89°48'42" | 155,15 |
| V-03 → V-04 | 88°35'17" | 162,55 |
| ... (continua) | | |

(Também poderia haver uma tabela `TOPO_ROTEIRO` ou `QUADRO_AZIMUTES`, mas neste caso a skill encontrou TEXT individuais na layer `COTAS`.)

## 2.3 Blocos e anotações

A layer `VERTICES` contém 14 blocos `INSERT` representando os vértices. Cada bloco `V-NN` tem:

- Coordenada de inserção
- Atributos: `E`, `N`, `AZIMUTE_VANTE`, `DIST_VANTE`, `CONFRONTANTE`

### Tabela de blocos

| Código | Inserção E | Inserção N | Atributo E | Atributo N | ΔXY inserção–polilinha |
|---|---:|---:|---:|---:|---:|
| V-01 | 294.142,648 | 9.122.335,960 | 294.142,648 | 9.122.335,960 | 0,000 |
| V-02 | 294.295,000 | 9.122.338,400 | 294.295,000 | 9.122.338,400 | 0,000 |
| V-03 | 294.450,123 | 9.122.342,890 | 294.450,123 | 9.122.342,890 | 0,000 |
| V-04 | 294.612,500 | 9.122.350,120 | 294.612,500 | 9.122.350,120 | 0,000 |
| V-05 | 294.770,890 | 9.122.358,400 | 294.770,890 | 9.122.358,400 | 0,000 |
| V-06 | 294.935,250 | 9.122.372,150 | 294.935,250 | 9.122.372,150 | 0,000 |
| V-07 | 295.085,640 | 9.122.395,800 | 295.085,640 | 9.122.395,800 | 0,000 |
| V-08 | 295.150,250 | 9.122.515,400 | 295.150,250 | 9.122.515,400 | 0,000 |
| V-09 | 295.080,123 | 9.122.640,750 | 295.080,123 | 9.122.640,750 | 0,000 |
| V-10 | 294.950,400 | 9.122.720,500 | 294.950,400 | 9.122.720,500 | 0,000 |
| V-11 | 294.815,750 | 9.122.755,250 | 294.815,750 | 9.122.755,250 | 0,000 |
| V-12 | 294.680,123 | 9.122.745,890 | 294.680,123 | 9.122.745,890 | 0,000 |
| V-13 | 294.520,500 | 9.122.720,400 | 294.520,500 | 9.122.720,400 | 0,000 |
| V-14 | 294.350,890 | 9.122.680,750 | 294.350,890 | 9.122.680,750 | 0,000 |

## 2.4 Confrontantes (na planta)

A planta **não** declara confrontantes para os trechos. Apenas a layer `QUADRO_AREAS` contém uma tabela de confrontações gerais:

| Lado | Confrontante declarado na planta |
|---|---|
| Norte | (não declarado) |
| Sul | (não declarado) |
| Leste | (não declarado) |
| Oeste | (não declarado) |

Esta é uma **divergência D4 — Divergência documental** (memorial cita 4 confrontantes específicos, planta cita apenas a lista geral).

## Matriz de consistência CAD

| Vértice | Polilinha | Roteiro CAD | Bloco/Anotação | Código | ΔXY | Situação |
|---|:---:|:---:|:---:|---|---:|---|
| V-01 | ✓ | ✓ | ✓ | V-01 | 0,000 | Compatível |
| V-02 | ✓ | ✓ | ✓ | V-02 | 0,000 | Compatível |
| V-03 | ✓ | ✓ | ✓ | V-03 | 0,000 | Compatível |
| V-04 | ✓ | ✓ | ✓ | V-04 | 0,000 | Compatível |
| V-05 | ✓ | ✓ | ✓ | V-05 | 0,000 | Compatível |
| V-06 | ✓ | ✓ | ✓ | V-06 | 0,000 | Compatível |
| V-07 | ✓ | ✓ | ✓ | V-07 | 0,000 | Compatível |
| V-08 | ✓ | ✓ | ✓ | V-08 | 0,000 | Compatível |
| V-09 | ✓ | ✓ | ✓ | V-09 | 0,000 | Compatível |
| V-10 | ✓ | ✓ | ✓ | V-10 | 0,000 | Compatível |
| V-11 | ✓ | ✓ | ✓ | V-11 | 0,000 | Compatível |
| V-12 | ✓ | ✓ | ✓ | V-12 | 0,000 | Compatível |
| V-13 | ✓ | ✓ | ✓ | V-13 | 0,000 | Compatível |
| V-14 | ✓ | ✓ | ✓ | V-14 | 0,000 | Compatível |

## Roteiro 02 (Planta) — Tabela final

| Ordem | De | Para | E ini | N ini | E fim | N fim | Az calc. (°) | Dist calc. (m) | Layer | Entidade | Handle |
|---:|---|---|---:|---:|---:|---:|---:|---:|---|---|---|
| 1 | V-01 | V-02 | 294.142,648 | 9.122.335,960 | 294.295,000 | 9.122.338,400 | 87,538 | 152,34 | LIMITE | LWPOLYLINE | 1F2 |
| 2 | V-02 | V-03 | 294.295,000 | 9.122.338,400 | 294.450,123 | 9.122.342,890 | 89,811 | 155,13 | LIMITE | LWPOLYLINE | 1F2 |
| 3 | V-03 | V-04 | 294.450,123 | 9.122.342,890 | 294.612,500 | 9.122.350,120 | 88,587 | 162,52 | LIMITE | LWPOLYLINE | 1F2 |
| 4 | V-04 | V-05 | 294.612,500 | 9.122.350,120 | 294.770,890 | 9.122.358,400 | 86,902 | 158,64 | LIMITE | LWPOLYLINE | 1F2 |
| 5 | V-05 | V-06 | 294.770,890 | 9.122.358,400 | 294.935,250 | 9.122.372,150 | 84,367 | 164,97 | LIMITE | LWPOLYLINE | 1F2 |
| 6 | V-06 | V-07 | 294.935,250 | 9.122.372,150 | 295.085,640 | 9.122.395,800 | 81,199 | 152,28 | LIMITE | LWPOLYLINE | 1F2 |
| 7 | V-07 | V-08 | 295.085,640 | 9.122.395,800 | 295.150,250 | 9.122.515,400 | 19,353 | 140,77 | LIMITE | LWPOLYLINE | 1F2 |
| 8 | V-08 | V-09 | 295.150,250 | 9.122.515,400 | 295.080,123 | 9.122.640,750 | 162,808 | 144,50 | LIMITE | LWPOLYLINE | 1F2 |
| 9 | V-09 | V-10 | 295.080,123 | 9.122.640,750 | 294.950,400 | 9.122.720,500 | 155,395 | 148,97 | LIMITE | LWPOLYLINE | 1F2 |
| 10 | V-10 | V-11 | 294.950,400 | 9.122.720,500 | 294.815,750 | 9.122.755,250 | 158,772 | 141,13 | LIMITE | LWPOLYLINE | 1F2 |
| 11 | V-11 | V-12 | 294.815,750 | 9.122.755,250 | 294.680,123 | 9.122.745,890 | 174,196 | 135,83 | LIMITE | LWPOLYLINE | 1F2 |
| 12 | V-12 | V-13 | 294.680,123 | 9.122.745,890 | 294.520,500 | 9.122.720,400 | 188,403 | 161,21 | LIMITE | LWPOLYLINE | 1F2 |
| 13 | V-13 | V-14 | 294.520,500 | 9.122.720,400 | 294.350,890 | 9.122.680,750 | 195,568 | 174,00 | LIMITE | LWPOLYLINE | 1F2 |
| 14 | V-14 | V-01 | 294.350,890 | 9.122.680,750 | 294.142,648 | 9.122.335,960 | 217,908 | 405,56 | LIMITE | LWPOLYLINE | 1F2 |

---

**Próximo**: [`roteiro-03-banco.md`](./roteiro-03-banco.md) — exemplo de extração de base geoespacial.
