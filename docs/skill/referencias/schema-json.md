# Schema JSON Interno

> Estrutura JSON padrão usada pela skill para representar dados extraídos e divergências detectadas. Veja seção 36 da [`../SKILL.md`](../SKILL.md).

---

## 1. Representação de segmento

Cada segmento do perímetro (trecho entre dois vértices consecutivos) é representado como:

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

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|---|---|:---:|---|
| `ordem` | int | ✓ | Sequência ordinal do segmento |
| `vertice_inicial` | str | ✓ | Código do vértice inicial |
| `vertice_final` | str | ✓ | Código do vértice final |
| `e_inicial` | float | ✓ | Coordenada E do vértice inicial (metros, métrico) |
| `n_inicial` | float | ✓ | Coordenada N do vértice inicial |
| `e_final` | float | ✓ | Coordenada E do vértice final |
| `n_final` | float | ✓ | Coordenada N do vértice final |
| `azimute_declarado` | float | ✓ | Azimute declarado no memorial (graus decimais) |
| `distancia_declarada` | float | ✓ | Distância declarada (metros) |
| `azimute_calculado` | float | ✓ | Azimute calculado pela skill a partir das coords |
| `distancia_calculada` | float | ✓ | Distância calculada pela skill |
| `erro_azimute` | float | ✓ | \|azimute_declarado − azimute_calculado\| (diferença circular) |
| `erro_distancia` | float | ✓ | \|distancia_declarada − distancia_calculada\| |
| `confrontante` | str | ✗ | Confrontante declarado (pode ser null) |
| `tipo_limite` | str | ✗ | Tipo de limite declarado (artificial/natural/inacessível) |
| `fonte` | str | ✓ | Origem do dado (ex.: "MEMORIAL p.3", "PLANTA layer LIMITE") |

### Cálculo de azimute

A skill calcula o azimute a partir de ΔE e ΔN usando a fórmula:

```python
import math

def azimute(e1, n1, e2, n2):
    de = e2 - e1
    dn = n2 - n1
    # Azimute contado a partir do Norte, no sentido horário
    az_rad = math.atan2(de, dn)
    if az_rad < 0:
        az_rad += 2 * math.pi
    return math.degrees(az_rad)
```

### Cálculo de distância

```python
import math

def distancia(e1, n1, e2, n2):
    de = e2 - e1
    dn = n2 - n1
    return math.sqrt(de**2 + dn**2)
```

### Diferença angular circular

```python
import math

def dif_angular(az1, az2):
    dif = abs(az1 - az2)
    return min(dif, 360 - dif)
```

---

## 2. Representação de divergência

Cada divergência detectada é representada como:

```json
{
  "id": "DIV-001",
  "tipo": "COORDENADA",
  "codigo": "D3",
  "segmento": "V-07-V-08",
  "fonte_a": "MEMORIAL",
  "fonte_b": "PLANTA",
  "valor_a": 294850.118,
  "valor_b": 294852.124,
  "diferenca": 2.006,
  "tolerancia": 1.50,
  "norma": "INCRA NTG 3ª ed.",
  "item_normativo": "§3.1 — Limite artificial (precisão 0,50 m, tolerância 3×)",
  "resultado": "ACIMA DA TOLERÂNCIA",
  "severidade": "ALTA",
  "observacao": "Diferença relevante; revisar levantamento ou transformar CRS"
}
```

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|---|---|:---:|---|
| `id` | str | ✓ | Identificador único (DIV-001, DIV-002, ...) |
| `tipo` | str | ✓ | Tipo de dado divergente (COORDENADA, AZIMUTE, DISTANCIA, AREA, PERIMETRO, CONFRONTANTE, MATRICULA, GEOMETRIA, SENTIDO, ...) |
| `codigo` | str | ✓ | Código de classificação (D1, D2, D3, D4, D5, D6, D7) |
| `segmento` | str | ✗ | Localização (V-07-V-08, GLOBAL, ...) |
| `fonte_a` | str | ✓ | Fonte A (MEMORIAL, PLANTA, BANCO_TOPOGEO, BANCO_GEORURAL) |
| `fonte_b` | str | ✓ | Fonte B |
| `valor_a` | any | ✗ | Valor observado em A (pode ser null se não declarado) |
| `valor_b` | any | ✗ | Valor observado em B |
| `diferenca` | float | ✗ | Diferença calculada (quando aplicável) |
| `tolerancia` | float | ✗ | Tolerância aplicável (quando normatizada) |
| `norma` | str | ✗ | Norma consultada |
| `item_normativo` | str | ✗ | Item específico da norma |
| `resultado` | str | ✓ | "DENTRO DA TOLERÂNCIA" / "ACIMA DA TOLERÂNCIA" / "NÃO DETERMINADO" / "DIVERGÊNCIA DOCUMENTAL" / etc. |
| `severidade` | str | ✓ | CRÍTICA / ALTA / MÉDIA / BAIXA |
| `observacao` | str | ✗ | Interpretação adicional |

---

## 3. Representação de ocorrência CAD

Quando a planta é DXF/DWG, ocorrências específicas do CAD usam o esquema:

```json
{
  "id": "CAD-001",
  "tipo": "BLOCO_ORFAO",
  "arquivo": "SITIO_FELICIDADE_SANITIZADO.dwg",
  "layer": "VERTICES",
  "entidade": "INSERT (handle 1A3)",
  "codigo": "V-12",
  "coordenada": {
    "E": 294850.123,
    "N": 9122120.456
  },
  "referencia": "Vértice V-12 da polilinha em (294850.118, 9122120.451)",
  "diferenca": 0.007,
  "tolerancia": 0.50,
  "situacao": "PRÓXIMO",
  "observacao": "Bloco dentro da tolerância de proximidade, mas com offset de 7 mm"
}
```

### Tipos de ocorrências CAD

| ID | Tipo | Severidade típica |
|---|---|---|
| `CAD-001` | BLOCO_ORFAO | ALTA |
| `CAD-002` | CODIGO_DUPLICADO | ALTA |
| `CAD-003` | ATRIBUTO_INCOMPATIVEL | MÉDIA |
| `CAD-004` | INSERCAO_INCOMPATIVEL | ALTA |
| `CAD-005` | ROTEIRO_DIVERGENTE | BAIXA ou MÉDIA |
| `CAD-006` | ANOTACAO_FORA_TOLERANCIA | ALTA |
| `CAD-007` | SEGMENTO_DIVERGENTE_MEMORIAL | ALTA |
| `CAD-008` | GEOMETRIA_ABERTA | CRÍTICA |
| `CAD-009` | ENTIDADE_DUPLICADA | MÉDIA |
| `CAD-010` | INCONSISTENCIA_UNIDADE_CRS | CRÍTICA |

---

## 4. Representação de fechamento

Para cada fonte, a skill calcula o fechamento:

```json
{
  "fonte": "MEMORIAL",
  "segmentos": 14,
  "sigma_e": 0.012,
  "sigma_n": 0.008,
  "erro_linear": 0.014,
  "perimetro": 1205.72,
  "precisao_relativa": 86123,
  "tolerancia_nbr_13133": 12000,
  "resultado": "COMPATIVEL"
}
```

---

## 5. Representação de vértice (para base geoespacial)

Quando os vértices vêm do `GeoRural.gpkg` ou `TopoGeo.gpkg`:

```json
{
  "indice": 1,
  "code": "V-01",
  "e": 294142.648,
  "n": 9122335.960,
  "tipo": "M",
  "metodo_pos": "GNSS/RTK",
  "sigma_x": 0.03,
  "sigma_y": 0.04,
  "sigma_z": 0.05,
  "sigma_p": 0.05,
  "fonte": "GeoRural.gpkg::vertice"
}
```

---

## 6. Exemplo completo de relatório JSON

```json
{
  "auditoria": {
    "id": "AUD-2026-09-17-001",
    "finalidade": "Pré-validação SIGEF",
    "modo": "HÍBRIDO_LIMITADO",
    "data": "2026-09-17T10:30:00Z",
    "responsavel": "Erison Rosa de Oliveira Barros",
    "crea": "1805172549 38236-PE"
  },
  "arquivos": {
    "memorial": "MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx",
    "planta_pdf": "SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf",
    "planta_dwg": "SITIO_FELICIDADE_SANITIZADO.dwg",
    "banco_topogeo": null,
    "banco_georural": null
  },
  "crs": {
    "memorial_original": "SIRGAS2000/UTM fuso -25",
    "planta_original": "SIRGAS2000/UTM fuso -25",
    "banco_original": null,
    "comparacao": "SIRGAS2000/UTM fuso -25"
  },
  "fechamento": {
    "memorial": {
      "sigma_e": 0.012,
      "sigma_n": 0.008,
      "erro_linear": 0.014,
      "perimetro": 1205.72,
      "precisao_relativa": 86123
    },
    "planta": {
      "sigma_e": 0.003,
      "sigma_n": 0.002,
      "erro_linear": 0.004,
      "perimetro": 1205.68,
      "precisao_relativa": 301420
    }
  },
  "area": {
    "memorial_m2": 61186.90,
    "planta_m2": 61186.94,
    "delta_absoluto_m2": 0.04,
    "delta_percentual": 0.00007
  },
  "perimetro": {
    "memorial_m": 1205.72,
    "planta_m": 1205.68,
    "delta_absoluto_m": 0.04,
    "delta_percentual": 0.003
  },
  "segmentos": [
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
  ],
  "divergencias": [
    {
      "id": "DIV-001",
      "tipo": "CONFRONTANTE",
      "codigo": "D4",
      "segmento": "V-03-V-04",
      "fonte_a": "MEMORIAL",
      "fonte_b": "PLANTA",
      "valor_a": "Gleba C",
      "valor_b": null,
      "diferenca": null,
      "tolerancia": null,
      "norma": null,
      "item_normativo": null,
      "resultado": "DIVERGÊNCIA DOCUMENTAL",
      "severidade": "MÉDIA",
      "observacao": "Planta não declara confrontante para este trecho"
    }
  ],
  "conclusao": {
    "veredito": "PRÉ-VALIDAÇÃO FAVORÁVEL",
    "limitacoes": [
      "Ausência de base geoespacial (TopoGeo/GeoRural)",
      "Confrontantes da planta parcialmente declarados",
      "Sem acesso ao relatório de ajustamento da rede geodésica"
    ],
    "recomendacoes": [
      "Incluir GeoRural.gpkg ou TopoGeo.gpkg em iteração futura",
      "Declarar confrontantes completos na planta"
    ]
  }
}
```

---

## 7. Convenções

### Casas decimais

A skill **preserva a representação original** do memorial/planta (separadamente, em campos textuais quando necessário) e usa **representação numérica padronizada** (6 casas decimais para coordenadas UTM, 3 casas para azimutes decimais) para cálculos.

### Codinomes de fonte

| Codinome | Significado |
|---|---|
| `MEMORIAL` | Memorial descritivo |
| `PLANTA` | Geometria da planta (CAD ou GeoPackage) |
| `BANCO_TOPOGEO` | TopoGeo.gpkg |
| `BANCO_GEORURAL` | GeoRural.gpkg |

Quando há sub-elementos da planta (geometria, roteiro textual, blocos), os codinomes são:

- `PLANTA_GEOMETRIA`
- `PLANTA_ROTEIRO`
- `PLANTA_BLOCOS`

### Identificadores

- `DIV-NNN` para divergências genéricas
- `CAD-NNN` para ocorrências do módulo CAD
- `AUD-YYYY-MM-DD-NNN` para auditorias (gerado pela skill)

---

**Próximo passo**: [`modulos-cad.md`](./modulos-cad.md) — detalhes do módulo de auditoria CAD (seções 42–58).
