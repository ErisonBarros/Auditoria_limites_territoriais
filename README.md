# Auditoria de Limites Territoriais

> Repositório de skill, documentação e material de teste para auditoria de limites territoriais, gerando roteiros perimétricos e comparando vértices, coordenadas, azimutes, distâncias, áreas, perímetros e confrontantes entre **memorial descritivo**, **planta CAD/geoespacial** e **bases oficiais** (`TopoGeo.gpkg` / `GeoRural.gpkg`), aplicando critérios técnicos da **NBR 13133:2021**, **NBR 17047:2022**, **Norma Técnica INCRA 3ª ed.** e **Manual SIGEF 2ª ed.**

---

## 📑 Índice

1. [Visão geral](#1-visão-geral)
2. [O que é esta skill](#2-o-que-é-esta-skill)
3. [Estrutura do repositório](#3-estrutura-do-repositório)
4. [Casos de uso](#4-casos-de-uso)
5. [Material publicado](#5-material-publicado)
6. [Quickstart](#6-quickstart)
7. [Documentação](#7-documentação)
8. [Princípios fundamentais](#8-princípios-fundamentais)
9. [Modo de trabalho e normas aplicáveis](#9-modo-de-trabalho-e-normas-aplicáveis)
10. [Saída padrão (relatório)](#10-saída-padrão-relatório)
11. [Classificação de divergências](#11-classificação-de-divergências)
12. [Módulo CAD específico (DXF/DWG)](#12-módulo-cad-específico-dxfdwg)
13. [LGPD e tratamento de dados pessoais](#13-lgpd-e-tratamento-de-dados-pessoais)
14. [Limitações](#14-limitações)
15. [Contribuição e revisão](#15-contribuição-e-revisão)
16. [Licença](#16-licença)
17. [Mantenedor e contato](#17-mantenedor-e-contato)

---

## 1. Visão geral

Este repositório contém três pilares:

1. **Skill técnica** (`docs/skill/`) — especificação formal (`SKILL.md`, 1.478 linhas, 58 seções) + documentação explicativa modular (12 arquivos Markdown)
2. **Material de teste real** (`Sitio_Felicidade/`) — caso de auditoria completa com memorial sanitizado, planta A1 e DWG
3. **Infraestrutura de documentação** (`README.md`, `LICENSE`) — referência institucional do projeto

A skill `auditoria-limites-territoriais` é um **auditor digital de limites territoriais** que trata três fontes de informação potencialmente conflitantes — memorial descritivo, planta CAD/geoespacial, base geoespacial oficial — como **independentes**, extrai dados de cada uma, normaliza em CRS métrico comum, compara matematicamente e aplica critérios normativos brasileiros com severidade classificada.

---

## 2. O que é esta skill

A skill é uma especificação **declarativa** (no padrão `agentskills` — <https://agentskills.io>) que define como um agente de IA deve executar auditoria de limites territoriais. **Não há código a instalar**: o arquivo `docs/skill/SKILL.md` é a especificação que o agente lê e executa.

### Capacidades

- ✅ Extrair roteiros perimétricos de **memoriais descritivos** (DOC, DOCX, PDF, TXT)
- ✅ Extrair geometria de **plantas** em DXF, DWG, GeoPackage, SHP, GeoJSON, KML
- ✅ Consultar bases oficiais **TopoGeo.gpkg** (topográfico/cadastral) e **GeoRural.gpkg** (rural/SIGEF)
- ✅ Calcular azimutes, distâncias, áreas, perímetros, fechamento, precisão relativa
- ✅ Aplicar tolerâncias da NBR 13133:2021, NBR 17047:2022, INCRA NTG 3ª ed. e SIGEF 2ª ed.
- ✅ Classificar divergências em D1–D7 com severidade CRÍTICA / ALTA / MÉDIA / BAIXA
- ✅ Detectar inconsistências específicas de CAD (blocos órfãos, códigos duplicados, atributos incompatíveis)
- ✅ Gerar relatório de 11 blocos com matriz de conformidade e conclusão técnica rastreável
- ❌ **Nunca** corrige silenciosamente, **nunca** inventa dados, **nunca** declara certificação oficial

---

## 3. Estrutura do repositório

```
Auditoria_limites_territoriais/
├── README.md                                  ← este arquivo (índice + visão geral)
├── LICENSE                                    ← licença MIT
│
├── Sitio_Felicidade/                          ← material de teste real (1º caso publicado)
│   ├── README.md                              ← metadados do caso + nota LGPD
│   ├── MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx   (38 KB)
│   ├── SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf               (1.3 MB)
│   └── SITIO_FELICIDADE_SANITIZADO.dwg                        (486 KB)
│
├── docs/                                      ← documentação completa da skill
│   └── skill/                                 ← pasta dedicada à skill
│       │
│       ├── SKILL.md                           ← ESPECIFICAÇÃO FORMAL (1.478 linhas, 58 seções)
│       │                                       • Princípios, entradas, cálculos, fechamento
│       │                                       • TopoGeo/GeoRural, correspondência de vértices
│       │                                       • NBR 13133, INCRA, SIGEF, pré-validação
│       │                                       • Módulo CAD (DXF/DWG) detalhado
│       ├── README.md                          ← introdução narrativa à skill
│       ├── GUIA_RAPIDO.md                     ← quickstart operacional
│       ├── COMO_FUNCIONA.md                   ← fluxo detalhado em 6 fases
│       ├── FORMATO_SAIDA.md                  ← estrutura completa do relatório
│       ├── EXEMPLOS.md                        ← 4 estudos de caso (incluindo Sítio Felicidade)
│       │
│       ├── referencias/                       ← referencial técnico
│       │   ├── normas.md                      ← NBR 13133, NBR 17047, INCRA NTG, SIGEF, Decreto 5.334
│       │   ├── classificacao-divergencias.md  ← códigos D1–D7 + severidades
│       │   ├── schema-json.md                 ← JSON interno padrão
│       │   └── modulos-cad.md                 ← módulo CAD (seções 42–58 expandidas)
│       │
│       └── exemplos/                          ← outputs reais de exemplo
│           ├── roteiro-01-memorial.md         ← exemplo de Roteiro 01
│           ├── roteiro-02-planta.md           ← exemplo de Roteiro 02 (com matriz CAD)
│           ├── roteiro-03-banco.md            ← exemplo de Roteiro 03 (GeoRural hipotético)
│           └── matriz-conformidade.md         ← matriz final + conclusão técnica
│
└── (futuro) outros casos de auditoria publicados
```

### Estrutura explicada em camadas

#### Camada 1 — Raiz do repositório

| Arquivo | Função |
|---|---|
| `README.md` | Índice principal, visão geral, instruções de uso |
| `LICENSE` | Licença MIT |

#### Camada 2 — Casos publicados

A pasta `Sitio_Felicidade/` é o **primeiro caso real** publicado. Futuros casos seguirão o mesmo padrão:

```
<NomeImobiliario>/
├── README.md                    ← metadados do caso (área, perímetro, RT, datas)
├── MEMORIAL_DESCRITIVO_<...>_SANITIZADO.docx
├── <IMOVEL>_Folha_A1_SANITIZADO.pdf
└── <IMOVEL>_SANITIZADO.dwg
```

Todos os arquivos têm **LGPD estritamente aplicada**: dados pessoais (CPF, RG, telefone, email, endereço, nome do proprietário) são substituídos por placeholders `[XXX REDACTED]` antes da publicação.

#### Camada 3 — Documentação da skill

A pasta `docs/skill/` contém **12 arquivos Markdown** totalizando ~90 KB de documentação:

| Categoria | Arquivo | Tamanho aproximado | Função |
|---|---|---:|---|
| **Especificação formal** | `SKILL.md` | 64 KB | Especificação técnica (1.478 linhas, 58 seções) |
| **Documentação narrativa** | `README.md` | 9,7 KB | Introdução e visão geral |
| | `GUIA_RAPIDO.md` | 5,0 KB | Quickstart operacional |
| | `COMO_FUNCIONA.md` | 17,8 KB | Fluxo detalhado em 6 fases |
| | `FORMATO_SAIDA.md` | 14,2 KB | Estrutura do relatório (11 blocos) |
| | `EXEMPLOS.md` | 6,4 KB | 4 estudos de caso |
| **Referências técnicas** | `referencias/normas.md` | 10,6 KB | NBR 13133, NBR 17047, INCRA, SIGEF |
| | `referencias/classificacao-divergencias.md` | 9,5 KB | Códigos D1–D7 + severidades |
| | `referencias/schema-json.md` | 10,2 KB | JSON interno padrão |
| | `referencias/modulos-cad.md` | 10,5 KB | Módulo CAD (seções 42–58) |
| **Exemplos de outputs** | `exemplos/roteiro-01-memorial.md` | 5,6 KB | Roteiro 01 exemplo |
| | `exemplos/roteiro-02-planta.md` | 7,6 KB | Roteiro 02 exemplo + matriz CAD |
| | `exemplos/roteiro-03-banco.md` | 6,7 KB | Roteiro 03 exemplo (GeoRural hipotético) |
| | `exemplos/matriz-conformidade.md` | 9,1 KB | Matriz final + conclusão técnica |

#### Camada 4 — Casos futuros (planejado)

Serão adicionados conforme produção:

- Caso 2: imóvel urbano (modo `CADASTRAL`)
- Caso 3: imóvel rural em SIGEF (modo `CERTIFICACAO_SIGEF`)
- Caso 4: caso-limite com CAD problemático (teste de detecção CAD-001 a CAD-010)

---

## 4. Casos de uso

A skill é aplicável em:

| Caso de uso | Modo | Norma principal |
|---|---|---|
| **Regularização fundiária urbana** | `CADASTRAL` | NBR 17047:2022 |
| **Levantamento topográfico para projeto** | `TOPOGRÁFICO` | NBR 13133:2021 |
| **Georreferenciamento rural (Lei 10.267/2001)** | `GEORREFERENCIAMENTO_RURAL` | INCRA NTG 3ª ed. |
| **Pré-validação para submissão SIGEF** | `CERTIFICACAO_SIGEF` | Manual SIGEF 2ª ed. |
| **Retificação administrativa de área** | `CADASTRAL` | NBR 17047:2022 + Lei 10.267/2001 |
| **Auditoria de levantamento existente** | `HÍBRIDO` | Contexto-dependente |
| **Diagnóstico de CAD problemático** | `HÍBRIDO` | Módulo CAD (seções 42–58) |

---

## 5. Material publicado

### Caso atual: **Sítio Felicidade**

| Item | Valor |
|---|---|
| Localização | Bairro do Nobre, Paulista-PE |
| Área | 61.186,90 m² (6,12 ha) |
| Perímetro | 1.205,72 m |
| Datum | SIRGAS2000 |
| Projeção | UTM (MC 33°W, fuso -25) |
| Sistema Geodésico | SGB — RBMC Brasília |
| Vértices | V-01 a V-14 (14 vértices, azimutes + distâncias) |
| Limites | N: Rio Paratibe + Companhia Paulista / S: Gleba "C" / L: Rio Paratibe / O: Sítio Saudade + Gleba "C" |
| Responsável Técnico | Erison Rosa de Oliveira Barros — Eng. Cartógrafo RNP 1805172549 38236-PE |
| Data | 2024-08-12 |
| Arquivos | DOCX + PDF + DWG (todos sanitizados) |
| Status | Pré-validação favorável (hipotética com banco) |

### Arquivos disponíveis

| Arquivo | Tamanho | Função |
|---|---:|---|
| `Sitio_Felicidade/README.md` | 1,7 KB | Metadados do caso + nota LGPD |
| `Sitio_Felicidade/MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx` | 38 KB | Memorial descritivo (Word) |
| `Sitio_Felicidade/SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf` | 1.342 KB | Prancha A1 com planta + carimbo + tabela |
| `Sitio_Felicidade/SITIO_FELICIDADE_SANITIZADO.dwg` | 497 KB | Vetor original (DWG AC1015) |

---

## 6. Quickstart

### Para usar a skill em um agente compatível

```bash
# 1. Copiar a pasta da skill para o diretório de skills do agente
cp -r docs/skill/ ~/.agentskills/engineering/auditoria-limites-territoriais/

# 2. Carregar a skill no agente (sintaxe depende do agente)
# Exemplo genérico:
agent load-skill auditoria-limites-territoriais
```

### Para executar a auditoria sobre o material deste repositório

```
Atue como especialista em auditoria de limites territoriais usando a skill
`auditoria-limites-territoriais`. Analise:

- Memorial: Sitio_Felicidade/MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx
- Planta PDF: Sitio_Felicidade/SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf
- Vetor DWG: Sitio_Felicidade/SITIO_FELICIDADE_SANITIZADO.dwg

Aplique o modo HÍBRIDO sem banco (apenas Memorial × Planta) e gere o
relatório completo conforme seção 37 da SKILL.md.
```

### Para ler a documentação

| Objetivo | Comece por |
|---|---|
| Entender o que é a skill | [`docs/skill/README.md`](./docs/skill/README.md) |
| Usar a skill rapidamente | [`docs/skill/GUIA_RAPIDO.md`](./docs/skill/GUIA_RAPIDO.md) |
| Entender o fluxo detalhado | [`docs/skill/COMO_FUNCIONA.md`](./docs/skill/COMO_FUNCIONA.md) |
| Ver o formato do relatório | [`docs/skill/FORMATO_SAIDA.md`](./docs/skill/FORMATO_SAIDA.md) |
| Ver casos de exemplo | [`docs/skill/EXEMPLOS.md`](./docs/skill/EXEMPLOS.md) |
| Consultar norma específica | [`docs/skill/referencias/normas.md`](./docs/skill/referencias/normas.md) |
| Entender classificação D1–D7 | [`docs/skill/referencias/classificacao-divergencias.md`](./docs/skill/referencias/classificacao-divergencias.md) |
| Auditoria de DXF/DWG | [`docs/skill/referencias/modulos-cad.md`](./docs/skill/referencias/modulos-cad.md) |
| Estrutura JSON | [`docs/skill/referencias/schema-json.md`](./docs/skill/referencias/schema-json.md) |
| Ver outputs modelo | [`docs/skill/exemplos/`](./docs/skill/exemplos/) |

---

## 7. Documentação

Toda a documentação da skill está em [`docs/skill/`](./docs/skill/). A documentação segue uma divisão em três níveis:

### Nível 1 — Especificação formal (`SKILL.md`)

Arquivo único de 1.478 linhas, 58 seções numeradas. É a fonte da verdade. Cada documento abaixo faz referência a seções específicas desta especificação.

### Nível 2 — Documentação narrativa

| Documento | Estilo | Leitura estimada |
|---|---|---|
| `README.md` | Introdutório | 10 min |
| `GUIA_RAPIDO.md` | Operacional | 5 min |
| `COMO_FUNCIONA.md` | Narrativo detalhado (6 fases) | 25 min |
| `FORMATO_SAIDA.md` | Referência estrutural | 15 min |
| `EXEMPLOS.md` | Casos ilustrativos | 15 min |

### Nível 3 — Referências técnicas

| Documento | Conteúdo |
|---|---|
| `referencias/normas.md` | Recortes das normas (NBR 13133, NBR 17047, INCRA NTG, SIGEF) + 11-item glossary |
| `referencias/classificacao-divergencias.md` | Códigos D1–D7 com critérios objetivos e severidades |
| `referencias/schema-json.md` | JSON interno padrão para integração com outros sistemas |
| `referencias/modulos-cad.md` | Módulo CAD (seções 42–58) expandido com exemplos |

### Nível 4 — Exemplos de outputs

| Documento | Conteúdo |
|---|---|
| `exemplos/roteiro-01-memorial.md` | Tabela completa de extração do memorial (14 vértices) |
| `exemplos/roteiro-02-planta.md` | Tabela completa de extração da planta + matriz CAD |
| `exemplos/roteiro-03-banco.md` | Exemplo hipotético de extração do `GeoRural.gpkg` |
| `exemplos/matriz-conformidade.md` | Matriz final + conclusão técnica + veredicto |

---

## 8. Princípios fundamentais

A skill opera sob 5 princípios não negociáveis (extraídos da `SKILL.md`):

### P1 — Independência das fontes

```
MEMORIAL ≠ PLANTA ≠ BANCO
```

A correspondência entre as três fontes **deve ser demonstrada**, nunca presumida. Cada fonte é tratada como independente, extraída separadamente e comparada matematicamente.

### P2 — Não corrigir silenciosamente

A skill **nunca**:

- Corrige o memorial usando a planta como referência
- Corrige a planta usando o memorial como referência
- Move blocos, renomeia vértices, altera coordenadas
- Inventa coordenadas, confrontantes, layers, matrículas ou tolerâncias

Ela apenas: **detecta, mede, compara, classifica e sugere revisão**.

### P3 — Rastreabilidade total

Todo dado apresentado tem origem explícita:

- Memorial → arquivo, página, trecho
- CAD → arquivo, layer, entidade (handle)
- GeoPackage → arquivo, camada, feição, campo

### P4 — Separação fato / cálculo / critério / limitação

A conclusão técnica sempre separa:

| Componente | Conteúdo |
|---|---|
| **Fato** | O que os dados mostram |
| **Cálculo** | O que foi calculado |
| **Critério** | Qual norma foi aplicada |
| **Limitação** | O que não pôde ser verificado |

### P5 — Resposta com valor + tolerância + norma

A skill **nunca** responde apenas "CONFORME" ou "NÃO CONFORME". Sempre apresenta:

- Valor observado
- Valor de referência
- Diferença
- Tolerância
- Norma/critério
- Fonte

Quando o critério não está definido: *"diferença calculada; avaliação normativa não determinada"*.

---

## 9. Modo de trabalho e normas aplicáveis

A skill identifica automaticamente o modo de trabalho com base nos arquivos e indícios textuais:

| Modo | Indícios | Norma principal |
|---|---|---|
| `TOPOGRÁFICO` | `TopoGeo.gpkg`, sem indícios rurais | NBR 13133:2021 |
| `CADASTRAL` | `TopoGeo.gpkg`, contexto urbano | NBR 17047:2022 |
| `GEORREFERENCIAMENTO_RURAL` | `GeoRural.gpkg` + palavras INCRA, SIGEF, imóvel rural | INCRA NTG 3ª ed. |
| `CERTIFICACAO_SIGEF` | Sistema de certificação, sentido horário obrigatório | Manual SIGEF 2ª ed. |
| `HÍBRIDO` | Mistura de indícios ou ambos os arquivos | Combinação de critérios |
| `INDETERMINADO` | Sem indícios suficientes | Diferença calculada, sem juízo |

### Tolerâncias aplicáveis

#### NBR 13133:2021 (topográfico)

- Precisão linear mínima: **1:12.000**
- Tolerância angular: `Tα = 3 × p × √n + 10"`

#### NBR 17047:2022 (cadastral)

| Classe | Precisão posicional |
|---|---:|
| 1 | ≤ 0,10 m |
| 2 | ≤ 0,25 m |
| 3 | ≤ 0,50 m |
| 4 | ≤ 1,00 m |

#### INCRA NTG 3ª ed. (rural)

| Tipo de limite | Precisão absoluta | Tolerância comparativa (3×) |
|---|---:|---:|
| Artificial | 0,50 m | 1,50 m |
| Natural | 3,00 m | 9,00 m |
| Inacessível | 7,50 m | 22,50 m |

#### SIGEF (certificação)

Pré-validação verifica 14 itens (códigos únicos, sequência, sentido horário, fechamento, etc.) — ver [`docs/skill/referencias/normas.md`](./docs/skill/referencias/normas.md).

---

## 10. Saída padrão (relatório)

O relatório sempre contém **11 blocos numerados**:

1. Resumo da auditoria
2. Roteiro 01 — Memorial
3. Roteiro 02 — Planta (com módulo CAD quando DXF/DWG)
4. Roteiro 03 — Banco (quando houver)
5. Comparação Memorial × Planta × Banco
6. Confrontantes por trecho
7. Fechamento
8. Matriz normativa
9. Divergências (classificadas D1–D7 com severidade)
10. Conclusão técnica (responde 11 perguntas padrão)
11. Anexos (JSON interno)

Veja estrutura completa em [`docs/skill/FORMATO_SAIDA.md`](./docs/skill/FORMATO_SAIDA.md).

---

## 11. Classificação de divergências

Toda divergência recebe **um código D1–D7** (mutuamente exclusivo) e **uma severidade**:

| Código | Tipo | Severidade típica |
|---|---|---|
| `D1` | Arredondamento | BAIXA |
| `D2` | Diferença geométrica pequena (≤ tolerância) | MÉDIA |
| `D3` | Diferença geométrica relevante (≤ 3× tolerância) | ALTA |
| `D4` | Divergência documental | BAIXA ou MÉDIA |
| `D5` | Divergência cadastral (matrícula, CNS) | MÉDIA ou ALTA |
| `D6` | Divergência topológica (fechamento, sequência) | ALTA ou CRÍTICA |
| `D7` | Divergência normativa (sentido, CRS, método) | ALTA ou CRÍTICA |

Critérios objetivos e exemplos em [`docs/skill/referencias/classificacao-divergencias.md`](./docs/skill/referencias/classificacao-divergencias.md).

---

## 12. Módulo CAD específico (DXF/DWG)

Quando a planta é DXF/DWG, a skill aplica detecções específicas (seções 42–58 da `SKILL.md`):

| ID | Tipo | Descrição |
|---|---|---|
| `CAD-001` | Bloco órfão | Bloco de vértice sem par na polilinha |
| `CAD-002` | Código duplicado | Mesmo código em coordenadas distintas |
| `CAD-003` | Atributo incompatível | Código do bloco ≠ atributo ≠ posição |
| `CAD-004` | Inserção incompatível | Posição geométrica ≠ atributo declarado |
| `CAD-005` | Roteiro divergente | Tabela do CAD ≠ geometria |
| `CAD-006` | Anotação fora do limite | Bloco com offset acima da tolerância |
| `CAD-007` | Segmento divergente | Segmento CAD ≠ memorial |
| `CAD-008` | Geometria aberta | Polígono não fecha |
| `CAD-009` | Entidade duplicada | Geometria sobreposta |
| `CAD-010` | Inconsistência de unidade/CRS | CAD em mm, memorial em m, etc. |

A skill distingue **três fontes** dentro do próprio CAD:

1. **Geometria efetiva** (polilinha do limite)
2. **Roteiro textual** (tabelas, TEXT, MTEXT)
3. **Blocos e anotações** (INSERT com atributos)

Essas três fontes são comparadas entre si **antes** de confrontar com memorial ou banco. Detalhes em [`docs/skill/referencias/modulos-cad.md`](./docs/skill/referencias/modulos-cad.md).

---

## 13. LGPD e tratamento de dados pessoais

**Política estrita deste repositório**: nenhum dado pessoal identificável (PII) de proprietários, ocupantes ou terceiros é publicado.

### Dados sanitizados antes da publicação

| Tipo | Substituição |
|---|---|
| CPF | `[CPF REDACTED]` |
| RG | `[RG REDACTED]` |
| Nome completo do proprietário | `[PROPRIETARIO REDACTED]` |
| Telefone | `[TELEFONE REDACTED]` |
| Email pessoal | `[EMAIL REDACTED]` |
| Endereço (logradouro + número) | `[ENDERECO REDACTED]` |
| Bairro | `[BAIRRO REDACTED]` |
| CEP | `[CEP REDACTED]` |
| EXIF GPS em imagens | removido |

### Dados preservados (públicos)

| Tipo | Mantido? |
|---|:---:|
| Nome do imóvel (ex.: "Sítio Felicidade") | ✓ |
| Município / UF | ✓ |
| Área, perímetro, coordenadas | ✓ |
| Limites perimétricos (confrontantes) | ✓ |
| Responsável técnico + RNP | ✓ (profissional habilitado, dado público) |
| Data do levantamento | ✓ |
| ART/RNP | ✓ |

O tratamento é aplicado em **todos os formatos** (DOCX, PDF, DWG). Para o DWG, a sanitização foi feita editando o DXF intermediário (gerado via libredwg), preservando a geometria mas substituindo atributos textuais do carimbo.

A skill **nunca** publica dados que violem a LGPD. Quando o material original contém PII, a skill aplica sanitização antes de qualquer commit.

---

## 14. Limitações

A skill **não pode** ser usada ou tem precisão reduzida quando:

- ❌ A planta está em formato proprietário sem conversão (DWG criptografado sem libredwg/AutoCAD/ODA)
- ❌ O memorial está em imagem escaneada sem OCR
- ❌ O CRS é desconhecido e os dados estão em coordenadas geográficas em graus
- ❌ A base geoespacial está corrompida ou inacessível
- ❌ Faltam coordenadas de pelo menos um vértice
- ❌ A poligonal não tem fechamento declarado e não é possível inferi-lo

Nesses casos, a skill registra a limitação explicitamente e prossegue com o que está disponível.

A skill **não substitui**:

- ❌ Certificação oficial do INCRA/SIGEF
- ❌ Análise jurídica de matrícula
- ❌ Vistoria in loco
- ❌ Responsabilidade técnica do engenheiro credenciado

A skill é uma **PRÉ-VALIDAÇÃO** que identifica inconsistências antes da submissão oficial.

---

## 15. Contribuição e revisão

### Como contribuir

1. **Issues**: relatar comportamentos inesperados ou casos-limite não cobertos
2. **Pull requests**: adicionar novos casos de teste em pastas nomeadas conforme o imóvel
3. **Discussões**: propor novos módulos (ex.: módulo hidrográfico, módulo batimetria)

### Padrão para novos casos publicados

Cada novo imóvel publicado deve seguir a estrutura:

```
<Nome_Imobiliario>/
├── README.md                                   ← metadados
├── MEMORIAL_DESCRITIVO_<...>_SANITIZADO.docx
├── <IMOVEL>_Folha_A1_SANITIZADO.pdf
└── <IMOVEL>_SANITIZADO.dwg
```

E ter LGPD aplicada **antes** do commit.

### Revisão da documentação

A documentação é **viva** e deve ser revisada sempre que:

- Uma nova norma técnica é publicada ou revisada
- Um novo tipo de arquivo é adicionado ao escopo
- Uma divergência real evidencia um caso-limite não coberto
- Um usuário reporta comportamento inesperado

Mudanças devem ser registradas no commit com referência à seção da `SKILL.md` afetada.

---

## 16. Licença

Este repositório é distribuído sob a **Licença MIT** — ver [`LICENSE`](./LICENSE).

A skill `auditoria-limites-territoriais` é uma especificação aberta que pode ser usada, modificada e redistribuída livremente, com atribuição ao mantenedor.

---

## 17. Mantenedor e contato

| Campo | Valor |
|---|---|
| **Mantenedor** | Prof. Erison Rosa de Oliveira Barros |
| **Formação** | Engenheiro Cartógrafo, MSc em Ciências Geodésicas, Dr em Engenharia Civil |
| **Cargo** | Professor Adjunto IV — DECART/UFPE |
| **Vice-coordenação** | Engenharia Cartográfica — DECART/UFPE |
| **Coordenações** | Estágio DECART/UFPE; LATOP/LABAT |
| **Presidência** | ABECA-PE |
| **RNP** | 1805172549 38236-PE |
| **Email institucional** | erison.barros@ufpe.br |
| **Site** | <https://erobeng.tech> |

---

## Anexo — Histórico de versões

| Versão | Data | Descrição |
|---|---|---|
| 1.0 | 2026-09-17 | Publicação inicial: skill completa + caso Sítio Felicidade sanitizado |

---

**Para começar**: leia [`docs/skill/README.md`](./docs/skill/README.md) e [`docs/skill/GUIA_RAPIDO.md`](./docs/skill/GUIA_RAPIDO.md).
