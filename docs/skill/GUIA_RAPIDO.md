# Guia Rápido — Skill `auditoria-limites-territoriais`

> Quickstart operacional. Para a especificação completa, veja [`SKILL.md`](./SKILL.md).

---

## Instalação

A skill é distribuída como um arquivo `SKILL.md` standalone, sem dependências de runtime obrigatórias além de:

- Python 3.10+ (para parsing de GeoPackage via `geopandas`/`fiona`)
- `ezdxf` 1.4+ (para leitura DXF)
- `libredwg` 0.14+ ou AutoCAD/ODA (para leitura DWG)
- `pdfplumber` ou `pdfminer.six` (para extração de texto em PDF)

A skill em si é **declarativa** — o arquivo `SKILL.md` é a especificação que o agente lê e executa. Não há código a instalar.

Para carregar a skill em um agente compatível:

```bash
# Em sistemas que usam o padrão agentskills (https://agentskills.io)
cp SKILL.md ~/.agentskills/engineering/auditoria-limites-territoriais/SKILL.md
```

---

## Uso mínimo

Forneça à skill os três arquivos básicos:

1. **Memorial descritivo** (DOCX/PDF/TXT)
2. **Planta** (DXF/DWG/GeoPackage)
3. **Base geoespacial** (`TopoGeo.gpkg` ou `GeoRural.gpkg`) — opcional mas recomendado

A skill identificará automaticamente o modo de trabalho e produzirá um relatório conforme [`FORMATO_SAIDA.md`](./FORMATO_SAIDA.md).

### Exemplo de prompt para o agente

```
Atue como especialista em auditoria de limites territoriais usando a skill
`auditoria-limites-territoriais`. Analise os seguintes arquivos:

- Memorial: Sítio_Felicidade/MEMORIAL_DESCRITIVO_SITIO_FELICIDADE_SANITIZADO.docx
- Planta: Sítio_Felicidade/SITIO_FELICIDADE_Folha_A1_SANITIZADO.pdf
- Vetor: Sítio_Felicidade/SITIO_FELICIDADE_SANITIZADO.dwg

Não há base geoespacial (TopoGeo/GeoRural) disponível. Aplique o modo HÍBRIDO
sem banco e gere o relatório completo conforme a seção 37 da skill.
```

---

## Saída em 11 blocos

O relatório sempre contém:

1. **Resumo da auditoria** — finalidade, arquivos, CRS, área, perímetro, vértices, segmentos, divergências.
2. **Roteiro 01 — Memorial** — tabela completa com coordenadas, azimutes, distâncias, confrontantes.
3. **Roteiro 02 — Planta** — tabela extraída da geometria CAD/GPKG.
4. **Roteiro 03 — Banco** — quando houver `TopoGeo`/`GeoRural`.
5. **Comparação** — Memorial × Planta × Banco, com Δ por trecho.
6. **Confrontantes** — comparação por trecho.
7. **Fechamento** — erros e precisão relativa.
8. **Matriz normativa** — valores, tolerâncias, referências.
9. **Divergências** — classificadas D1–D7 com severidade.
10. **Conclusão técnica** — separada em fato, cálculo, critério, limitação.
11. **Anexos** — JSON interno padrão (seção 36 da SKILL).

---

## 3 decisões de projeto que você precisa entender

### 1. A skill nunca corrige

Ela detecta, mede, compara, classifica e sugere revisão. **Nunca move blocos, renomeia vértices, altera coordenadas ou apaga entidades.**

Se a planta tem um bloco órfão, a skill registra `CAD-001 — Bloco órfão` e indica a posição. O humano decide o que fazer.

### 2. A skill nunca declara certificação SIGEF

A análise é uma **pré-validação**. A certificação é ato oficial do INCRA/SIGEF, realizado por engenheiro credenciado com submission de parcela no sistema. A skill pode verificar se os pré-requisitos formais estão atendidos, mas não substitui a certificação oficial.

### 3. A skill separa fato, cálculo, critério e limitação

Na conclusão técnica (seção 38), a skill sempre responde a 11 perguntas separadas:

```
1. Memorial e planta representam a mesma geometria?
2. Os vértices correspondem?
3. As coordenadas correspondem dentro do critério adotado?
4. Os azimutes correspondem?
5. As distâncias correspondem?
6. Área e perímetro correspondem?
7. Os confrontantes correspondem por trecho?
8. O fechamento é adequado ao critério aplicável?
9. Existem divergências cadastrais?
10. Existem divergências normativas?
11. Quais limitações impedem conclusão mais ampla?
```

A resposta a cada pergunta é sustentada pelos dados efetivamente encontrados nos arquivos.

---

## Quando a skill **não pode** ser usada

- Quando a planta está em formato proprietário sem conversão (ex.: DWG criptografado sem acesso ao AutoCAD/ODA/libredwg)
- Quando o memorial está em imagem (foto escaneada sem OCR) sem texto extraível
- Quando falta a especificação do CRS e os dados estão em coordenadas geográficas em graus
- Quando a base geoespacial está corrompida ou inacessível

Nesses casos, a skill registra a limitação explicitamente e prossegue com o que está disponível.

---

## Próximos passos

- Para entender o fluxo completo: [`COMO_FUNCIONA.md`](./COMO_FUNCIONA.md)
- Para ver o relatório modelo: [`FORMATO_SAIDA.md`](./FORMATO_SAIDA.md)
- Para estudar casos reais: [`EXEMPLOS.md`](./EXEMPLOS.md)
- Para o referencial normativo: [`referencias/normas.md`](./referencias/normas.md)
- Para o módulo CAD: [`referencias/modulos-cad.md`](./referencias/modulos-cad.md)
