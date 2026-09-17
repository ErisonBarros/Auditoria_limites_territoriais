# Introdução à Skill `auditoria-limites-territoriais`

> Documentação explicativa completa, modular e rastreável para a `SKILL.md` mestre desta pasta.

Esta documentação foi gerada para acompanhar o arquivo [`SKILL.md`](./SKILL.md) (1.478 linhas, 58 seções). A `SKILL.md` é a **especificação técnica formal** da skill — curta, seca, auditável. Os arquivos desta pasta `docs/skill/` são o **manual de uso**, com narrativa, exemplos e fluxos operacionais.

---

## 1. O que é esta skill

A skill `auditoria-limites-territoriais` é um **auditor digital de limites territoriais** que analisa, de forma independente e rastreável, três fontes de informação potencialmente conflitantes sobre um mesmo imóvel:

1. **Memorial descritivo** (DOC, DOCX, PDF, TXT) — descrição textual dos vértices, azimutes, distâncias e confrontantes.
2. **Planta CAD ou geoespacial** (DXF, DWG, GeoPackage, SHP, GeoJSON, KML) — geometria vetorial do limite.
3. **Bases geoespaciais oficiais** (`TopoGeo.gpkg`, `GeoRural.gpkg`) — cadastros territoriais e rurais.

A skill **nunca presume que uma fonte está correta**. Em vez disso, trata cada uma como independente, extrai os dados de cada uma, compara-os matematicamente, aplica critérios normativos (NBR 13133:2021, INCRA, SIGEF) e documenta as divergências com severidade classificada.

### Domínios de aplicação

A skill opera em cinco modos de trabalho, identificados automaticamente:

| Modo | Fonte preferencial | Critério principal | Norma de referência |
|---|---|---|---|
| **Topográfico** | `TopoGeo.gpkg` | Precisão linear 1:12.000 | NBR 13133:2021 |
| **Cadastral** | `TopoGeo.gpkg` | Coerência geométrica + documental | NBR 17047:2022 |
| **Georreferenciamento Rural** | `GeoRural.gpkg` | Precisão posicional por tipo de limite | Norma Técnica INCRA 3ª ed. |
| **Certificação SIGEF** | `GeoRural.gpkg` | Validação geométrica + sentido horário | Manual SIGEF 2ª ed. |
| **Híbrido** | Ambas as bases | Combinação de critérios | Contexto-normativo |
| **Indeterminado** | — | Diferença calculada sem juízo | Documentação |

---

## 2. Princípio fundamental: independência das fontes

A skill opera sob um princípio não-negociável:

> **Memorial ≠ Planta ≠ Banco**

Isso significa que a correspondência entre as três fontes **deve ser demonstrada**, nunca presumida. A skill **nunca corrige silenciosamente** uma fonte usando outra como referência. Em vez disso, ela apresenta:

- Valor observado na fonte A
- Valor observado na fonte B
- Diferença calculada
- Critério técnico aplicável
- Tolerância (quando normatizada)
- Referência normativa
- Resultado da comparação

Quando a norma aplicável não está definida, a skill registra **"diferença calculada; avaliação normativa não determinada"** — explicitando a limitação em vez de inventar um veredicto.

---

## 3. Os três roteiros perimétricos independentes

A construção de roteiros perimétricos é o coração da skill. Cada roteiro é uma sequência tabular de segmentos `(vértice_inicial → vértice_final)`, com coordenadas, azimutes e distâncias.

### Roteiro 01 — Memorial

Extraído **exclusivamente** do memorial descritivo. Cada linha corresponde a um trecho descrito textualmente, com:

- Código do vértice inicial e final
- Coordenadas E e N declaradas
- Azimute declarado (em DMS ou decimal)
- Distância declarada
- Confrontante declarado
- Tipo de limite declarado
- Fonte (página, linha do DOC, trecho do PDF)

### Roteiro 02 — Planta

Extraído **exclusivamente** da geometria presente no arquivo CAD/geoespacial. Quando a planta é DXF/DWG, a skill identifica:

- A polilinha fechada candidata ao limite (priorizando `LWPOLYLINE`, `POLYLINE`, `3DPOLY`)
- As entidades de texto/MTEXT/blocos que representam o roteiro textual interno do CAD
- As anotações/blocos que representam códigos de vértice

Esses três sub-elementos são tratados como **fontes independentes** dentro da própria planta, e são comparados antes de confrontar com o memorial.

### Roteiro 03 — Base geoespacial

Extraído do `TopoGeo.gpkg` (modo topográfico/cadastral) ou `GeoRural.gpkg` (modo rural/SIGEF). A skill consulta camadas específicas:

- **`TopoGeo.gpkg`** → `property_area_a`, `limit_point_p`, `boundary_element_l`
- **`GeoRural.gpkg`** → `parcela`, `limite`, `vertice` (e `hist_*` para auditoria histórica)

---

## 4. Fluxo operacional resumido

A `SKILL.md` define 26 passos de execução na seção 40. Aqui está o resumo narrativo:

```
1. Receber arquivos
   ↓
2. Identificar modo de trabalho (topográfico/cadastral/rural/SIGEF/híbrido)
   ↓
3. Identificar normas aplicáveis
   ↓
4. Extrair memorial → Roteiro 01
   ↓
5. Validar fechamento do memorial (ΣΔE, ΣΔN, erro linear)
   ↓
6. Extrair planta → Roteiro 02
   ↓
7. Validar geometria da planta (fechamento, auto-interseção, sequência)
   ↓
8. Normalizar CRS (todas as fontes em sistema métrico comum)
   ↓
9. Selecionar base apropriada (TopoGeo ou GeoRural)
   ↓
10. Extrair base → Roteiro 03
   ↓
11. Associar vértices (código → banco → sequência → proximidade)
   ↓
12. Comparar coordenadas, azimutes, distâncias, área, perímetro
   ↓
13. Comparar confrontantes por trecho
   ↓
14. Aplicar tolerâncias (NBR 13133, INCRA, SIGEF)
   ↓
15. Pré-validar SIGEF quando aplicável
   ↓
16. Classificar divergências (D1–D7) e severidade (CRÍTICA/ALTA/MÉDIA/BAIXA)
   ↓
17. Gerar matriz normativa
   ↓
18. Gerar relatório final com conclusão técnica
```

---

## 5. Classificação de divergências e severidade

Cada divergência recebe um código `D1–D7` e uma severidade. Veja [`referencias/classificacao-divergencias.md`](./referencias/classificacao-divergencias.md).

| Código | Tipo | Exemplo |
|---|---|---|
| `D1` | Arredondamento | Diferença de 1 cm por arredondamento de 2 casas decimais |
| `D2` | Diferença geométrica pequena | ΔXY = 0,31 m em limite artificial (tol. INCRA 0,50 m) |
| `D3` | Diferença geométrica relevante | ΔXY = 4,20 m em limite natural (tol. INCRA 3,00 m) |
| `D4` | Divergência documental | Memorial cita "Rio Paratibe", planta cita "Rio P." |
| `D5` | Divergência cadastral | Matrícula 12.345 no memorial, 12.346 no GeoRural |
| `D6` | Divergência topológica | Segmento V-07→V-08 ausente na planta |
| `D7` | Divergência normativa | Sentido anti-horário em parcela SIGEF |

---

## 6. O que a skill **NÃO faz**

A skill opera sob regras de segurança técnica estritas (seção 39 da `SKILL.md`):

- **Nunca** inventa coordenadas, confrontantes, layers, matrículas ou tolerâncias.
- **Nunca** altera geometrias silenciosamente.
- **Nunca** corrige o memorial automaticamente.
- **Nunca** declara certificação oficial SIGEF.
- **Nunca** declara "CONFORME" ou "NÃO CONFORME" sem apresentar valor, tolerância e norma.
- **Nunca** remove blocos órfãos ou duplicados do CAD — apenas os detecta e sugere revisão.

Quando falta dado, a skill registra: *"Não foi possível verificar este item com os arquivos fornecidos."*

---

## 7. Como usar esta documentação

Esta pasta está organizada assim:

```
docs/skill/
├── SKILL.md                          ← especificação formal (58 seções)
├── README.md                         ← este arquivo (introdução)
├── COMO_FUNCIONA.md                  ← narrativa detalhada de operação
├── EXEMPLOS.md                       ← 4 estudos de caso
├── FORMATO_SAIDA.md                  ← estrutura do relatório final
├── GUIA_RAPIDO.md                    ← quickstart para uso imediato
├── referencias/
│   ├── normas.md                     ← NBR 13133, INCRA, SIGEF — recortes
│   ├── classificacao-divergencias.md ← D1–D7 e severidades
│   ├── schema-json.md                ← JSON interno padrão
│   └── modulos-cad.md                ← módulo CAD (seções 42–58)
└── exemplos/
    ├── roteiro-01-memorial.md        ← exemplo de Roteiro 01
    ├── roteiro-02-planta.md          ← exemplo de Roteiro 02
    ├── roteiro-03-banco.md           ← exemplo de Roteiro 03
    └── matriz-conformidade.md        ← exemplo de matriz final
```

Para começar rapidamente, leia [`GUIA_RAPIDO.md`](./GUIA_RAPIDO.md). Para entender a fundo, leia [`COMO_FUNCIONA.md`](./COMO_FUNCIONA.md). Para ver o output real, veja [`FORMATO_SAIDA.md`](./FORMATO_SAIDA.md).

---

## 8. Aplicação no repositório

O arquivo `Sitio_Felicidade/` na raiz deste repositório é o **primeiro caso de teste real** publicado. Ele contém:

- Memorial descritivo (DOCX sanitizado)
- Planta (PDF sanitizado, A1 com carimbo)
- Vetor original (DWG sanitizado)

Os dados pessoais (CPF, RG, telefone, email, endereço exato, nome completo do proprietário) foram substituídos por placeholders `[XXX REDACTED]` antes da publicação, em conformidade com a LGPD.

A skill pode ser executada sobre este material seguindo os passos do `GUIA_RAPIDO.md`.

---

## 9. Auditoria e revisão

Esta documentação é **viva** e deve ser revisada sempre que:

- Uma nova norma técnica é publicada ou revisada
- Um novo tipo de arquivo é adicionado ao escopo
- Uma divergência real evidencia um caso-limite não coberto
- Um usuário reporta comportamento inesperado

Mudanças devem ser registradas no commit com referência à seção da `SKILL.md` afetada.

---

**Mantenedor**: Prof. Erison Rosa de Oliveira Barros — Eng. Cartógrafo RNP 1805172549 38236-PE
**Versão da skill**: 1.0 (2026-09-17)
**Licença**: ver [`LICENSE`](../../LICENSE) na raiz
