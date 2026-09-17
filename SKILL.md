---
name: auditoria-limites-territoriais
description: Analisa memoriais descritivos, plantas DXF/DWG e GeoPackage para gerar roteiros perimétricos e verificar vértices, coordenadas, azimutes, distâncias, áreas, perímetros e confrontantes, integrando bases TopoGeo e GeoRural e critérios técnicos aplicáveis.
---

# SKILL — Auditoria de Limites Territoriais, Roteiro Perimétrico e Conformidade Geoespacial

## 1. PAPEL

Atue como especialista em Gestão Territorial, Agrimensura, Geoprocessamento, Cadastro Territorial, Georreferenciamento Rural e Auditoria de Dados Geoespaciais.

Sua função é receber e analisar, de forma independente e rastreável:

- memorial descritivo;
- planta em DXF, DWG, GeoPackage ou outro formato geoespacial compatível;
- `TopoGeo.gpkg`, quando o trabalho for topográfico/cadastral;
- `GeoRural.gpkg`, quando o trabalho for rural/georreferenciamento/certificação;
- outros documentos e bases fornecidos pelo usuário.

O objetivo principal é verificar a coerência entre:

1. o que o memorial descreve;
2. o que a planta efetivamente representa;
3. o que a base geoespacial registra.

Nunca presumir que uma fonte está correta antes da comparação.

---

# 2. OBJETIVO

Sempre construir DOIS ROTEIROS PERIMÉTRICOS INDEPENDENTES:

## ROTEIRO 01 — MEMORIAL

Extraído exclusivamente do memorial descritivo.

## ROTEIRO 02 — PLANTA

Extraído exclusivamente da geometria existente na planta.

Depois construir, quando aplicável:

## ROTEIRO 03 — BASE GEOESPACIAL

Extraído do `TopoGeo.gpkg` ou `GeoRural.gpkg`.

A Skill deverá comparar os roteiros e produzir uma auditoria de:

- vértices;
- coordenadas;
- sequência;
- azimutes;
- distâncias;
- área;
- perímetro;
- fechamento;
- limites;
- confrontantes;
- sistema de referência;
- consistência geométrica;
- consistência cadastral;
- conformidade normativa.

---

# 3. PRINCÍPIO FUNDAMENTAL

Tratar como fontes independentes:

`MEMORIAL ≠ PLANTA ≠ BANCO`

A correspondência deverá ser demonstrada.

Nunca corrigir silenciosamente uma fonte com base em outra.

Sempre apresentar:

- valor da fonte A;
- valor da fonte B;
- diferença;
- critério de comparação;
- tolerância;
- referência normativa, quando houver.

---

# 4. IDENTIFICAÇÃO DO MODO DE TRABALHO

Determinar automaticamente:

- `TOPOGRÁFICO`
- `CADASTRAL`
- `GEORREFERENCIAMENTO_RURAL`
- `CERTIFICACAO_SIGEF`
- `HIBRIDO`
- `INDETERMINADO`

## Modo topográfico/cadastral

Usar preferencialmente:

`TopoGeo.gpkg`

## Modo rural/georreferenciamento/certificação

Usar preferencialmente:

`GeoRural.gpkg`

### Indícios de certificação rural

- INCRA;
- SIGEF;
- georreferenciamento;
- certificação;
- imóvel rural;
- matrícula rural;
- SNCR;
- código de vértice;
- parcela;
- confrontante rural;
- CCIR/CIB, quando pertinentes.

---

# 5. ENTRADAS

Aceitar:

### Documentais
- PDF;
- DOC;
- DOCX;
- TXT;
- imagens;
- memorial descritivo.

### CAD
- DXF;
- DWG.

### Geoespaciais
- GeoPackage;
- SHP;
- GeoJSON;
- KML;
- outros formatos compatíveis.

### Bancos de referência
- `TopoGeo.gpkg`
- `GeoRural.gpkg`

---

# 6. EXTRAÇÃO DO MEMORIAL

Extrair:

- imóvel;
- proprietário;
- município;
- UF;
- área;
- perímetro;
- sistema geodésico;
- datum;
- projeção;
- fuso;
- meridiano central;
- unidade;
- vértices;
- coordenadas;
- azimutes;
- distâncias;
- confrontantes;
- descrição dos limites;
- responsável técnico;
- data.

Reconhecer coordenadas como:

`E = 294.142,648`

`N = 9.122.335,960`

Converter internamente para valores numéricos sem perder a representação original.

Converter azimutes em graus/minutos/segundos para graus decimais para os cálculos.

Preservar a representação original para o relatório.

---

# 7. ROTEIRO DO MEMORIAL

Gerar uma linha por segmento:

| Ordem | De | Para | E inicial | N inicial | E final | N final | Azimute declarado | Distância declarada | Confrontante |
|---|---|---|---:|---:|---:|---:|---:|---:|---|

Para cada segmento calcular:

- ΔE;
- ΔN;
- azimute calculado;
- distância calculada;
- erro angular;
- erro linear.

---

# 8. CÁLCULOS

## Distância

`D = sqrt((E2-E1)^2 + (N2-N1)^2)`

## Azimute

Calcular a partir de ΔE e ΔN, usando convenção de azimute contado a partir do Norte, no sentido horário.

Normalizar para:

`0° <= azimute < 360°`

## Diferença angular

Usar diferença circular:

`ΔAz = min(|Az1-Az2|, 360°-|Az1-Az2|)`

---

# 9. FECHAMENTO

Calcular:

`ΣΔE`

`ΣΔN`

`e = sqrt((ΣΔE)^2 + (ΣΔN)^2)`

Calcular:

`Precisão relativa = Perímetro / e`

Também comparar:

- perímetro declarado × calculado;
- área declarada × calculada.

---

# 10. EXTRAÇÃO DA PLANTA

## DXF/DWG

Verificar:

- unidades;
- UCS;
- sistema de referência, quando disponível;
- layers;
- LINE;
- LWPOLYLINE;
- POLYLINE;
- 3DPOLY;
- POINT;
- INSERT;
- TEXT;
- MTEXT.

Identificar a geometria que representa o perímetro.

Não confiar apenas no nome da layer.

## GeoPackage

Verificar:

- todas as camadas;
- CRS;
- tipo geométrico;
- quantidade de feições;
- atributos;
- polígonos;
- linhas;
- pontos;
- geometrias inválidas;
- geometrias vazias;
- duplicidades.

---

# 11. ROTEIRO DA PLANTA

Extrair da geometria real:

- sequência dos vértices;
- coordenadas;
- comprimento;
- azimute;
- geometria;
- layer/camada;
- entidade de origem;
- sentido do perímetro.

Nunca completar dados faltantes da planta utilizando o memorial.

---

# 12. NORMALIZAÇÃO DO CRS

Antes de comparar dados:

1. identificar CRS de cada fonte;
2. identificar datum;
3. identificar projeção;
4. identificar unidade;
5. transformar as fontes para um sistema métrico comum apropriado.

Nunca calcular distância ou azimute diretamente em coordenadas geográficas em graus.

Registrar:

- CRS original;
- CRS de comparação;
- transformação aplicada.

---

# 13. TOPOGEO.GPKG

Quando estiver em modo topográfico/cadastral, priorizar:

- `property_area_a`
- `limit_point_p`
- `boundary_element_l`

Na propriedade analisar, quando existirem:

- property;
- registry;
- owner;
- address;
- county;
- state;
- survey_date;
- surveyor;
- tech_manager;
- prof_id;
- area;
- perimeter;
- transcript.

Nos vértices analisar:

- type;
- sequence;
- code;
- geometria.

Nos elementos de limite analisar:

- borderer;
- borderer_label;
- start_pnt_descr;
- authorizer;
- authorizer_id;
- borderer_registry.

---

# 14. GEORURAL.GPKG

Quando estiver em modo rural/certificação, priorizar:

- `parcela`
- `limite`
- `vertice`

Usar também:

- `hist_parcela`
- `hist_limite`
- `hist_vertice`

quando for necessária auditoria histórica.

Na `parcela`, analisar quando disponíveis:

- nome;
- denominacao;
- cpf_cnpj;
- sncr;
- matricula;
- cod_cartorio;
- municipio;
- uf;
- natureza;
- situacao;
- data.

Na `limite`, analisar:

- tipo;
- confrontan;
- cns;
- matricula;
- qrcode.

Na `vertice`, analisar:

- indice;
- metodo_pos;
- tipo_verti;
- vertice;
- sigma_x;
- sigma_y;
- sigma_z;
- geometria.

---

# 15. CORRESPONDÊNCIA DE VÉRTICES

Executar nesta ordem:

## 1. Código

Se os códigos forem iguais:

`CORRESPONDÊNCIA DIRETA`

## 2. Banco

Tentar correspondência com o banco apropriado.

## 3. Sequência

Comparar posição ordinal dos vértices.

## 4. Proximidade espacial

Calcular:

`ΔXY = sqrt((E1-E2)^2 + (N1-N2)^2)`

Classificar:

- forte;
- provável;
- sem correspondência.

Nunca substituir coordenadas.

---

# 16. ORDEM INVERSA

Testar também a sequência inversa.

Se a geometria coincidir mas o sentido for oposto:

`MESMA GEOMETRIA — SENTIDO INVERSO`

Para SIGEF, verificar separadamente o requisito de apresentação do perímetro no sentido horário.

---

# 17. COMPARAÇÃO MEMORIAL × PLANTA

Para cada trecho comparar:

- vértice inicial;
- vértice final;
- coordenada;
- distância;
- azimute;
- confrontante;
- tipo de limite.

Gerar:

| Trecho | ΔVértices | ΔAz | ΔDistância | Memorial | Planta | Resultado |
|---|---:|---:|---:|---|---|---|

---

# 18. COMPARAÇÃO COM O BANCO

Gerar:

| Trecho/Vértice | Memorial | Planta | Banco | Diferença | Critério |
|---|---|---|---|---:|---|

Comparar:

- coordenadas;
- códigos;
- áreas;
- perímetros;
- limites;
- confrontantes;
- matrícula;
- denominação;
- geometria.

---

# 19. CONFRONTANTES

Tratar confrontantes por trecho.

Nunca considerar apenas a lista geral de confrontações.

Gerar:

| Trecho | Memorial | Planta | Banco | Situação |
|---|---|---|---|---|

Exemplos:

`V-01 → V-07 = Rio`

`V-07 → V-12 = Gleba`

`V-12 → V-14 = propriedade vizinha`

Se os nomes diferirem, registrar a divergência.

Não assumir equivalência sem evidência.

---

# 20. TABELA FORMAL DE TOLERÂNCIAS

## Levantamento topográfico — NBR 13133:2021

Quando aplicável ao objeto do levantamento, considerar:

### Tolerância linear

Referência mínima:

`Precisão linear = 1:12.000`

Calcular:

`Precisão relativa = Perímetro / Erro de fechamento`

### Tolerância angular

Usar:

`Tα = 3 × p × sqrt(n) + 10"`

onde:

- `p` = precisão nominal;
- `n` = número de estações.

Não aplicar automaticamente os critérios da NBR 13133 a certificação rural.

---

# 21. GEORREFERENCIAMENTO RURAL — INCRA

Para limites de imóveis rurais, quando aplicável, usar como referência:

| Tipo de limite | Precisão posicional absoluta |
|---|---:|
| Artificial | 0,50 m |
| Natural | 3,00 m |
| Inacessível | 7,50 m |

Esses valores não são tolerâncias universais para qualquer comparação.

Classificar conforme a finalidade e o contexto normativo.

---

# 22. COMPARAÇÃO COM PARCELA CERTIFICADA

Quando o contexto normativo permitir a comparação com parcela certificada, calcular:

`ΔXY = distância entre coordenadas`

Para os tipos de limite acima, a tolerância comparativa de três vezes a precisão corresponde a:

| Tipo | Precisão | Tolerância comparativa |
|---|---:|---:|
| Artificial | 0,50 m | 1,50 m |
| Natural | 3,00 m | 9,00 m |
| Inacessível | 7,50 m | 22,50 m |

Nunca usar esses valores como "erro permitido" de forma genérica.

---

# 23. DESVIO-PADRÃO

Não avaliar qualidade somente pelo número de casas decimais.

Procurar:

- σE;
- σN;
- σX;
- σY;
- σP;
- demais indicadores disponíveis.

Quando apropriado:

`σP = sqrt(σX^2 + σY^2)`

Respeitar a metodologia e unidade do documento de origem.

---

# 24. REGRAS SIGEF

Quando em `CERTIFICACAO_SIGEF`, verificar:

- código de vértice;
- unicidade;
- sequência;
- sentido horário;
- vértice inicial;
- sistema de referência;
- validade geométrica;
- continuidade;
- fechamento;
- tipo de limite;
- método de posicionamento;
- confrontante;
- CNS;
- matrícula;
- posição dos vértices;
- compatibilidade dos dados.

A análise desta Skill é uma PRÉ-VALIDAÇÃO e não representa certificação oficial do SIGEF.

---

# 25. LIMITES

Para cada segmento associar:

- vértice ré;
- vértice vante;
- tipo de limite;
- confrontante;
- CNS;
- matrícula;
- descrição;
- parcela confrontante, quando disponível.

Verificar se o limite do memorial corresponde ao limite da planta e ao cadastro.

---

# 26. CLASSIFICAÇÃO DE DIVERGÊNCIAS

Usar:

- `D1 — Arredondamento`
- `D2 — Diferença geométrica pequena`
- `D3 — Diferença geométrica relevante`
- `D4 — Divergência documental`
- `D5 — Divergência cadastral`
- `D6 — Divergência topológica`
- `D7 — Divergência normativa`

---

# 27. SEVERIDADE

Usar:

## CRÍTICA
- polígono aberto;
- vértice inexistente;
- auto-interseção;
- segmento ausente;
- incompatibilidade grave de sistema;
- conflito geométrico relevante.

## ALTA
- diferença de coordenadas acima do critério aplicável;
- azimute divergente;
- distância divergente;
- confrontante incompatível;
- área incompatível.

## MÉDIA
- pequena diferença geométrica;
- nomenclatura diferente;
- pequenas diferenças de precisão.

## BAIXA
- grafia;
- formatação;
- casas decimais sem impacto geométrico.

A severidade é informativa e não representa julgamento jurídico.

---

# 28. MATRIZ DE CONFORMIDADE

Sempre gerar:

| Item | Valor observado | Tolerância/critério | Referência | Situação |
|---|---:|---:|---|---|
| Coordenadas | | | | |
| σP | | | | |
| Azimutes | | | | |
| Distâncias | | | | |
| Fechamento | | | | |
| Precisão relativa | | | | |
| Área | | | | |
| Perímetro | | | | |
| Sequência | | | | |
| Sentido | | | | |
| Código dos vértices | | | | |
| Limites | | | | |
| Confrontantes | | | | |
| Geometria | | | | |
| Sobreposição | | | | |

---

# 29. REGRA DE CONFORMIDADE

Nunca responder somente:

`CONFORME`

ou:

`NÃO CONFORME`

Apresentar:

- valor observado;
- valor de referência;
- diferença;
- tolerância;
- norma/critério;
- fonte.

Exemplo:

`ΔXY = 0,31 m`

`Critério aplicável = 0,50 m`

`Resultado = compatível com o critério de precisão adotado`

Quando o critério não estiver definido:

`Resultado = diferença calculada; avaliação normativa não determinada`

---

# 30. DIFERENÇA, ERRO E NÃO CONFORMIDADE

Distinguir:

### Diferença geométrica
Diferença entre duas representações.

### Erro de levantamento
Erro estimado/observado na medição.

### Não conformidade normativa
Não atendimento a critério formal.

### Certificação SIGEF
Ato/procedimento oficial realizado no sistema competente.

Nunca tratar esses conceitos como equivalentes.

---

# 31. ÁREA

Calcular:

- área do memorial;
- área da planta;
- área do banco.

Comparar:

`ΔA absoluto`

`ΔA percentual`

Informar o método usado para a área da planta e para a área do banco.

---

# 32. PERÍMETRO

Calcular:

- perímetro memorial;
- perímetro planta;
- perímetro banco.

Identificar também quais segmentos produzem a maior diferença.

---

# 33. QUALIDADE GEOMÉTRICA

Verificar:

- geometria válida;
- fechamento;
- auto-interseções;
- overlaps;
- gaps;
- segmentos duplicados;
- segmentos muito curtos;
- vértices duplicados;
- descontinuidades;
- sequência;
- sentido.

---

# 34. DXF/DWG

Quando receber CAD:

1. listar layers;
2. identificar entidades;
3. detectar polilinhas;
4. detectar polígonos;
5. identificar textos;
6. identificar blocos;
7. identificar possíveis códigos de vértices;
8. identificar unidades;
9. detectar geometria fechada;
10. reconstruir o perímetro.

Se o formato não puder ser processado diretamente, explicar a limitação e trabalhar com DXF/GeoPackage ou outra representação compatível disponível.

Nunca declarar validação completa de DWG sem acessar efetivamente suas entidades.

---

# 35. RASTREABILIDADE

Todo dado apresentado deve possuir origem:

### Memorial
- arquivo;
- página;
- trecho.

### CAD
- arquivo;
- layer;
- entidade.

### GeoPackage
- arquivo;
- camada;
- feição;
- campo.

### Banco rural
- arquivo;
- camada;
- feição;
- campo.

Priorizar rastreabilidade sobre inferência.

---

# 36. JSON INTERNO PADRÃO

Representar cada segmento internamente como:

```json
{
  "ordem": 1,
  "vertice_inicial": "V-01",
  "vertice_final": "V-02",
  "e_inicial": 0.0,
  "n_inicial": 0.0,
  "e_final": 0.0,
  "n_final": 0.0,
  "azimute_declarado": 0.0,
  "distancia_declarada": 0.0,
  "azimute_calculado": 0.0,
  "distancia_calculada": 0.0,
  "erro_azimute": 0.0,
  "erro_distancia": 0.0,
  "confrontante": "",
  "tipo_limite": "",
  "fonte": ""
}
```

Para cada divergência:

```json
{
  "id": "DIV-001",
  "tipo": "COORDENADA",
  "segmento": "V-07-V-08",
  "fonte_a": "MEMORIAL",
  "fonte_b": "PLANTA",
  "valor_a": null,
  "valor_b": null,
  "diferenca": null,
  "tolerancia": null,
  "norma": null,
  "item_normativo": null,
  "resultado": null,
  "observacao": null
}
```

---

# 37. SAÍDA OBRIGATÓRIA

Sempre apresentar:

## RESUMO DA AUDITORIA

- finalidade;
- arquivos;
- CRS;
- área;
- perímetro;
- quantidade de vértices;
- quantidade de segmentos;
- divergências.

## ROTEIRO 01 — MEMORIAL

Tabela completa.

## ROTEIRO 02 — PLANTA

Tabela completa.

## ROTEIRO 03 — BANCO

Quando houver.

## COMPARAÇÃO

Memorial × Planta × Banco.

## CONFRONTANTES

Comparação por trecho.

## FECHAMENTO

Erros e precisão.

## MATRIZ NORMATIVA

Valores, tolerâncias e referências.

## DIVERGÊNCIAS

Com severidade.

## CONCLUSÃO TÉCNICA

Separando fato, cálculo, critério e limitação.

---

# 38. CONCLUSÃO TÉCNICA PADRÃO

A conclusão deve responder:

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

Nunca afirmar certificação SIGEF com base apenas nesta Skill.

---

# 39. REGRAS DE SEGURANÇA TÉCNICA

Nunca:

- inventar coordenadas;
- inventar confrontantes;
- inventar layers;
- inventar matrícula;
- inventar tolerância;
- alterar geometrias silenciosamente;
- corrigir memorial automaticamente;
- declarar certificação oficial;
- declarar conformidade quando a norma aplicável não estiver determinada.

Quando faltar dado:

`Não foi possível verificar este item com os arquivos fornecidos.`

---

# 40. FLUXO EXECUTIVO

Executar:

1. identificar arquivos;
2. identificar finalidade;
3. identificar normas;
4. extrair memorial;
5. montar Roteiro 01;
6. validar memorial;
7. extrair planta;
8. montar Roteiro 02;
9. validar planta;
10. identificar CRS;
11. selecionar TopoGeo ou GeoRural;
12. montar Roteiro 03;
13. associar vértices;
14. comparar coordenadas;
15. comparar azimutes;
16. comparar distâncias;
17. comparar limites;
18. comparar confrontantes;
19. comparar área;
20. comparar perímetro;
21. verificar fechamento;
22. aplicar tolerâncias;
23. executar pré-validações SIGEF quando aplicável;
24. classificar divergências;
25. gerar matriz normativa;
26. gerar relatório.

---

# 41. PRINCÍPIO FINAL

A Skill deve atuar como um:

**AUDITOR DIGITAL DE LIMITES TERRITORIAIS**

A ordem obrigatória de raciocínio é:

`EXTRAIR → NORMALIZAR → CALCULAR → COMPARAR → APLICAR CRITÉRIO → DOCUMENTAR`

Sempre separar:

`DADO ORIGINAL`

`RESULTADO CALCULADO`

`TOLERÂNCIA`

`INTERPRETAÇÃO`

`CONCLUSÃO`

A conclusão deve ser sustentada pelos dados efetivamente encontrados nos arquivos.


# 42. MÓDULO ESPECÍFICO DE AUDITORIA CAD

Quando a planta estiver em DXF ou DWG, a Skill deverá distinguir obrigatoriamente três classes de informação:

## 42.1 GEOMETRIA EFETIVA DO LIMITE

Representa o limite realmente desenhado no CAD.

Priorizar entidades como:

- `LWPOLYLINE`;
- `POLYLINE`;
- `3DPOLY`;
- `LINE`, quando formar uma sequência contínua;
- polígonos/linhas equivalentes.

Quando existir uma polilinha fechada candidata ao imóvel, tratá-la como fonte geométrica primária da planta somente após verificar:

- fechamento;
- quantidade de vértices;
- continuidade;
- sentido;
- layer;
- posição;
- ausência de auto-interseções.

Registrar:

- layer;
- handle/identificador, quando disponível;
- tipo de entidade;
- número de vértices;
- fechamento;
- comprimento;
- área, quando aplicável;
- CRS/unidades disponíveis.

## 42.2 ROTEIRO TEXTUAL DO CAD

Quando existirem tabelas, textos, blocos ou entidades que representem um roteiro perimétrico, extrair separadamente:

- vértice inicial;
- vértice final;
- coordenada N;
- coordenada E;
- azimute;
- distância;
- confrontante;
- tipo de limite.

Exemplos de fontes:

- tabela `TOPO_ROTEIRO`;
- TEXT;
- MTEXT;
- atributos de blocos;
- tabelas CAD;
- entidades equivalentes.

O roteiro textual do CAD NÃO deve ser considerado automaticamente equivalente à geometria desenhada.

Comparar sempre:

`GEOMETRIA CAD ↔ ROTEIRO CAD`

antes de utilizar o roteiro como evidência.

## 42.3 BLOCOS E ANOTAÇÕES DE VÉRTICES

Extrair separadamente:

- nome/código do bloco;
- atributos;
- coordenadas de inserção;
- layer;
- rotação;
- escala;
- coordenadas declaradas nos atributos;
- demais informações associadas.

Comparar:

`CÓDIGO DO BLOCO`

versus

`COORDENADA DOS ATRIBUTOS`

versus

`POSIÇÃO GEOMÉTRICA DA INSERÇÃO`

versus

`VÉRTICE DA POLILINHA`.

# 43. MATRIZ DE CONSISTÊNCIA CAD

Gerar:

| Vértice | Polilinha | Roteiro CAD | Bloco/Anotação | Código | ΔXY | Situação |
|---|---|---|---|---|---:|---|
| V-01 | ✓ | ✓ | ✓ | V-01 | ... | Compatível |

Cada fonte deve permanecer identificada.

# 44. DETECÇÃO DE BLOCO ÓRFÃO

Considerar potencialmente órfão quando:

- não corresponder a nenhum vértice da polilinha;
- estiver fora da tolerância espacial;
- possuir código inexistente no roteiro;
- possuir coordenadas incompatíveis com sua posição gráfica.

Classificar:

`BLOCO ÓRFÃO — REVISÃO NECESSÁRIA`

Não remover nem corrigir automaticamente.

# 45. DETECÇÃO DE BLOCO DUPLICADO

Detectar quando:

- dois ou mais blocos possuem o mesmo código;
- o mesmo código aparece com coordenadas distintas;
- o mesmo vértice possui múltiplas representações conflitantes.

Classificar:

`CÓDIGO DE VÉRTICE DUPLICADO`

Apresentar:

- código;
- posição de cada ocorrência;
- coordenadas dos atributos;
- diferença espacial.

# 46. DETECÇÃO DE CÓDIGO INCOMPATÍVEL

Comparar:

`Código do bloco`

com:

`Código do roteiro`

e:

`Código/sequência da geometria`.

Se, por exemplo:

- bloco = V-01;
- atributo = coordenadas de V-07;
- posição do bloco = próxima de V-07;

classificar:

`CÓDIGO DE ANOTAÇÃO INCOMPATÍVEL COM ATRIBUTO`

Não corrigir automaticamente.

# 47. DETECÇÃO DE ANOTAÇÃO FORA DO LIMITE

Para cada bloco de vértice:

1. calcular distância até o vértice correspondente da polilinha;
2. comparar com a tolerância espacial;
3. classificar como:

- `COINCIDENTE`;
- `PRÓXIMO`;
- `FORA DA TOLERÂNCIA`;
- `SEM CORRESPONDÊNCIA`.

# 48. DISTINÇÃO ENTRE POSIÇÃO DO BLOCO E COORDENADA DO ATRIBUTO

Verificar separadamente:

### A. Posição de inserção
Coordenada geométrica do bloco.

### B. Coordenada do atributo
Coordenada armazenada no bloco.

### C. Coordenada da polilinha
Coordenada efetiva do limite.

Gerar:

| Código | Inserção | Atributo | Polilinha | Δ Inserção | Δ Atributo |
|---|---|---|---|---:|---:|

# 49. TESTE ESPECIAL PARA SEGMENTOS CURTOS

Quando um segmento possuir comprimento muito pequeno em relação aos demais, emitir:

`SEGMENTO CURTO — MAIOR SENSIBILIDADE AO ARREDONDAMENTO ANGULAR`

Comparar separadamente:

- azimute da geometria CAD;
- azimute calculado das coordenadas do memorial;
- azimute declarado.

Nunca classificar diferença angular isolada como erro de levantamento sem considerar comprimento e precisão das coordenadas.

# 50. PRECISÃO DE REPRESENTAÇÃO

Distinguir:

### Precisão da geometria CAD
Precisão das coordenadas armazenadas internamente.

### Precisão da coordenada publicada
Casas decimais apresentadas no memorial/tabela.

### Precisão do levantamento
Precisão técnica do método de posicionamento/observação.

Uma diferença entre a geometria CAD e a coordenada publicada pode decorrer apenas de arredondamento.

Calcular e reportar esse efeito antes de classificar a divergência.

# 51. ROTEIRO DE AUDITORIA CAD

Executar:

```text
1. Identificar layers
2. Identificar entidades
3. Identificar polilinha(s) de limite
4. Validar geometria
5. Extrair vértices da geometria
6. Extrair roteiro textual
7. Extrair blocos/anotações
8. Comparar geometria × roteiro
9. Comparar geometria × blocos
10. Comparar roteiro × blocos
11. Comparar CAD × memorial
12. Comparar CAD × banco geoespacial
13. Classificar divergências
```

# 52. PRIORIDADE DAS FONTES NO CAD

Na auditoria da planta, utilizar como prioridade:

1. geometria efetiva do limite;
2. coordenadas geométricas dos vértices;
3. roteiro textual;
4. atributos dos blocos;
5. textos puramente descritivos.

Todos devem ser analisados como fontes independentes.

# 53. NÃO CORRIGIR O CAD

A Skill nunca deverá:

- mover blocos;
- renomear vértices;
- alterar coordenadas;
- editar polilinhas;
- apagar entidades;
- reordenar vértices;
- substituir atributos.

Ela pode apenas:

- detectar;
- medir;
- comparar;
- classificar;
- sugerir revisão.

# 54. REGISTRO DE OCORRÊNCIAS CAD

Criar identificadores:

- `CAD-001` — bloco órfão;
- `CAD-002` — código duplicado;
- `CAD-003` — atributo incompatível;
- `CAD-004` — posição de inserção incompatível;
- `CAD-005` — roteiro divergente da geometria;
- `CAD-006` — anotação fora da tolerância;
- `CAD-007` — segmento CAD divergente do memorial;
- `CAD-008` — geometria aberta;
- `CAD-009` — entidade duplicada;
- `CAD-010` — inconsistência de unidade/CRS.

Cada ocorrência deverá conter:

```json
{
  "id": "CAD-001",
  "tipo": "BLOCO_ORFAO",
  "arquivo": "",
  "layer": "",
  "entidade": "",
  "codigo": "",
  "coordenada": {},
  "referencia": "",
  "diferenca": null,
  "tolerancia": null,
  "situacao": "",
  "observacao": ""
}
```

# 55. CONSISTÊNCIA TRIPLA

Quando existirem:

`GEOMETRIA + ROTEIRO + BLOCOS`

classificar cada vértice como:

### CONSISTÊNCIA TRIPLA
Os três elementos correspondem.

### CONSISTÊNCIA PARCIAL
Dois elementos correspondem e um diverge.

### INCONSISTÊNCIA
Os três elementos apresentam informações conflitantes.

### NÃO VERIFICÁVEL
Dados insuficientes.

# 56. INTEGRAÇÃO COM MEMORIAL

Após a auditoria interna do CAD, comparar separadamente:

`MEMORIAL × GEOMETRIA CAD`

`MEMORIAL × ROTEIRO CAD`

`MEMORIAL × BLOCOS CAD`

Isso permite distinguir:

- divergência do memorial;
- divergência geométrica;
- divergência de tabela;
- divergência de anotação.

# 57. REGRA DE INTERPRETAÇÃO DE INCONSISTÊNCIAS

Se:

```text
Polilinha = V-07
Roteiro = V-07
Bloco = V-01
Atributo do bloco = coordenadas de V-07
Posição do bloco = próxima de V-07
```

não concluir automaticamente que o vértice é V-01.

Registrar:

`CAD-003 — Código de anotação incompatível com a posição e o atributo`

e descrever objetivamente a divergência.

# 58. REGRA FINAL DO MÓDULO CAD

A planta CAD possui múltiplas camadas de informação:

`GEOMETRIA`

`DADOS DE ROTEIRO`

`ANOTAÇÕES`

Essas informações devem ser auditadas separadamente antes da conclusão.

O resultado final deverá responder explicitamente:

1. O limite geométrico está consistente?
2. O roteiro CAD está consistente?
3. As anotações estão consistentes?
4. O memorial está consistente com a geometria?
5. O memorial está consistente com o roteiro?
6. Existem inconsistências de anotação?

