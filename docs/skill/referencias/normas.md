# Referencial Normativo — Skill `auditoria-limites-territoriais`

> Recortes das normas técnicas aplicáveis à auditoria de limites territoriais. Esta é uma referência operacional, não substitui a consulta aos textos integrais das normas.

---

## 1. NBR 13133:2021 — Execução de levantamento topográfico

**Título**: ABNT NBR 13133:2021 — Execução de levantamento topográfico
**Status**: Vigente
**Aplicabilidade**: Levantamentos topográficos em modo `TOPOGRÁFICO`

### Precisão linear

```
Precisão relativa = Perímetro / Erro de fechamento
```

**Mínimo aceitável**: 1:12.000 para levantamentos topográficos planimétricos de precisão.

### Tolerância angular

```
Tα = 3 × p × √n + 10"
```

onde:

- `p` = precisão nominal do instrumento (em segundos de arco)
- `n` = número de estações (vértices da poligonal)

**Exemplo**: para `p = 5"` e `n = 14` vértices:

```
Tα = 3 × 5 × √14 + 10 = 3 × 5 × 3,742 + 10 = 66,13" ≈ 1'06"
```

### Quando **não** aplicar

A NBR 13133 **não se aplica automaticamente** a:

- Certificação rural (INCRA/SIGEF) — usa critérios próprios
- Levantamentos hidrográficos
- Batimetria
- Geodésia de alta precisão (rede geodésica)

---

## 2. NBR 17047:2022 — Levantamento cadastral

**Título**: ABNT NBR 17047:2022 — Levantamento cadastral territorial — Procedimento
**Status**: Vigente
**Aplicabilidade**: Cadastro territorial urbano e rural em modo `CADASTRAL`

### Conteúdo principal

A NBR 17047 estabelece:

- Princípios do cadastro territorial multifinalitário
- Métodos de levantamento cadastral
- Precisão posicional por classe (1, 2, 3, 4)
- Elementos do cadastro (parcela, edifício, eixo, ponto limite)
- Atributos obrigatórios e opcionais

### Precisão posicional por classe

| Classe | Precisão posicional | Aplicação |
|---|---:|---|
| 1 | ≤ 0,10 m | Cadastro fiscal de alta precisão |
| 2 | ≤ 0,25 m | Cadastro multifinalitário detalhado |
| 3 | ≤ 0,50 m | Cadastro de referência |
| 4 | ≤ 1,00 m | Cadastro simplificado |

A skill usa essa tabela quando o contexto é cadastro territorial sem ser rural/SIGEF.

---

## 3. Norma Técnica INCRA para Georreferenciamento (3ª ed.)

**Título**: Norma Técnica para Georreferenciamento de Imóveis Rurais (NTG 3ª ed.)
**Órgão**: INCRA — Instituto Nacional de Colonização e Reforma Agrária
**Aplicabilidade**: `GEORREFERENCIAMENTO_RURAL` e `CERTIFICACAO_SIGEF`

### 3.1 Precisão posicional por tipo de limite

| Tipo de limite | Precisão posicional absoluta |
|---|---:|
| Artificial | 0,50 m |
| Natural | 3,00 m |
| Inacessível | 7,50 m |

**Definições**:

- **Artificial**: limite definido por obra humana (cerca, muro, estrada, vala)
- **Natural**: limite definido por feição natural (rio, crista, lago)
- **Inacessível**: limite que não pode ser medido diretamente (interior de mata, brejo intransitável)

### 3.2 Métodos de posicionamento aceitos

- GNSS/GPS (L1, L1+L2, RTK, PPP)
- Estação total (poligonal)
- Levantamento aerofotogramétrico
- Sensoriamento remoto orbital

### 3.3 Limite inacessível — vértice tipo M (Marco)

Para limites inacessíveis, o vértice tipo `M` (Marco) é posicionado o mais próximo possível do limite verdadeiro, dentro da precisão exigida. O tipo `P` (Presumido) é usado quando o limite é estimado sem observação direta.

### 3.4 Tolerância comparativa (3× a precisão)

Quando se compara a planta/memorial com uma parcela certificada, aplica-se:

| Tipo | Precisão | Tolerância comparativa |
|---|---:|---:|
| Artificial | 0,50 m | 1,50 m |
| Natural | 3,00 m | 9,00 m |
| Inacessível | 7,50 m | 22,50 m |

**Atenção**: esses valores **não são tolerâncias universais** para qualquer comparação. Aplica-se especificamente quando se confronta a representação local (memorial/planta) com a parcela certificada no SIGEF.

---

## 4. SIGEF — Manual de Certificação (2ª ed.)

**Título**: Manual de Certificação de Imóveis Rurais no SIGEF (2ª ed.)
**Órgão**: INCRA / Secretaria Especial de Agricultura Familiar e do Desenvolvimento Agrário
**Aplicabilidade**: `CERTIFICACAO_SIGEF`

### 4.1 Pré-requisitos formais

A skill verifica (mas não certifica) os seguintes pré-requisitos:

- [x] Código de vértice único
- [x] Sequência contínua de vértices (sem gaps)
- [x] Sentido horário do perímetro
- [x] Vértice inicial declarado
- [x] Sistema de referência SIRGAS2000
- [x] Geometria válida (sem auto-interseções)
- [x] Continuidade (sem segmentos ausentes)
- [x] Fechamento (primeiro vértice = último vértice)
- [x] Tipo de limite declarado para cada segmento
- [x] Método de posicionamento registrado
- [x] Confrontante declarado (com CNS quando aplicável)
- [x] Matrícula declarada
- [x] Posição dos vértices (sem inversões suspeitas)
- [x] Compatibilidade dos dados entre memorial e parcela

### 4.2 Limitação da skill

A análise desta skill é uma **PRÉ-VALIDAÇÃO** e **não representa certificação oficial do SIGEF**. A certificação é ato administrativo do INCRA, realizado após:

1. Submissão da parcela pelo engenheiro credenciado
2. Análise técnica pelo INCRA
3. Aprovação ou rejeição com parecer
4. Emissão da certificação (em caso de aprovação)

A skill pode ser usada para verificar se a parcela **tem chance** de ser aprovada, identificando inconsistências antes da submissão oficial.

---

## 5. Decreto 5.334/2005 — Sistema Geodésico Brasileiro

**Título**: Decreto 5.334, de 6 de janeiro de 2005
**Aplicabilidade**: Todos os modos

### Conteúdo

Estabelece o **Sistema Geodésico Brasileiro (SGB)** como referência oficial para o país. Define:

- **SIRGAS2000** como sistema de referência geodésico
- **RBMC** (Rede Brasileira de Monitoramento Contínuo) como infraestrutura de apoio
- Padrões de precisão para levantamentos

### Implicação para a skill

Todas as coordenadas devem estar em SIRGAS2000 (ou ter transformação documentada para SIRGAS2000) para fins de certificação. A skill verifica o CRS de cada fonte e aplica transformação quando necessário.

---

## 6. Lei 10.267/2001 — CNIR e Georreferenciamento

**Título**: Lei 10.267, de 28 de agosto de 2001
**Aplicabilidade**: Imóveis rurais

### Conteúdo principal

- Cria o **CNIR** (Cadastro Nacional de Imóveis Rurais)
- Exige **georreferenciamento** para fins de registro, transferência, desmembramento e remembramento de imóveis rurais
- Define prazo para adequação
- Estabelece o INCRA como órgão gestor

### Implicação para a skill

A skill aplica esta lei quando o contexto é imóvel rural. Em outros contextos, é informativa.

---

## 7. Decreto 4.449/2002 — Regulamentação da Lei 10.267

**Título**: Decreto 4.449, de 30 de outubro de 2002 (com alterações posteriores)
**Aplicabilidade**: Imóveis rurais — procedimentos de certificação

### Conteúdo

Regulamenta a Lei 10.267/2001, estabelecendo:

- Prazos para certificação
- Credenciamento de profissionais
- Prazos para registro de imóveis
- Procedimentos de retificação administrativa

---

## 8. Res. CONFEA 1.073/2016 — Atribuições profissionais

**Título**: Resolução CONFEA 1.073, de 19 de abril de 2016
**Aplicabilidade**: Responsável técnico

### Conteúdo

Regulamenta as atribuições profissionais de engenheiros cartógrafos, agrimensores e correlatos. A skill registra o RT (responsável técnico) e verifica se está habilitado para o tipo de serviço.

---

## 9. Outras referências aplicáveis conforme contexto

| Norma | Aplicação |
|---|---|
| **NBR 14166:1998** | Rede de Referência Cadastral Municipal |
| **NBR 14699:2001** | Desenho técnico — Representação gráfica |
| **NBR 10647:1989** | Desenho técnico — Norma geral |
| **NBR 8196:1999** | Desenho técnico — Formato e apresentação |
| **Manual de Limites e Confrontações (IBGE)** | Limites políticos e geográficos |
| **Manual Técnico de Limites (IBGE 3ª ed.)** | Procedimentos oficiais |
| **Manual Técnico de Posicionamento (IBGE)** | Métodos GNSS |
| **Manual do Usuário SIGEF** | Operação do sistema |

---

## 10. Hierarquia de aplicação

Quando múltiplas normas se aplicam, a skill segue esta hierarquia:

1. **Legislação federal específica** (Lei 10.267/2001, Decreto 5.334/2005) — prevalência sobre todas
2. **Norma técnica do INCRA** (NTG 3ª ed.) — para imóveis rurais
3. **Manual SIGEF** — para certificação
4. **Norma ABNT específica** (NBR 13133, NBR 17047) — quando não houver norma federal
5. **Manual IBGE** — para referências geodésicas e cartográficas
6. **Boas práticas técnicas** — quando nenhuma norma for aplicável

Quando duas normas conflitarem:

- A de hierarquia superior prevalece
- A divergência é registrada como `D7 — Divergência normativa`
- A skill reporta ambas as interpretações possíveis

---

## 11. Glossário de termos técnicos

| Termo | Definição |
|---|---|
| **Azimute** | Ângulo horizontal contado a partir do Norte, no sentido horário, até a direção do alinhamento. Varia de 0° a 360°. |
| **Azimute geodésico** | Azimute contado sobre a superfície de referência (elipsoide), usado em geodésia. |
| **Azimute topográfico** | Azimute contado no plano topográfico (projetado), usado em levantamentos de pequena extensão. |
| **Datum** | Modelo matemático da Terra usado como referência para coordenadas. |
| **SIRGAS2000** | Sistema de Referência Geocêntrico para as Américas (realização 2000), adotado oficialmente pelo Brasil. |
| **RBMC** | Rede Brasileira de Monitoramento Contínuo dos sistemas GNSS. |
| **Precisão relativa** | Razão entre a distância percorrida e o erro de fechamento. Ex.: 1:10.000 significa 1 cm de erro para cada 100 m. |
| **Erro de fechamento** | Distância entre o último vértice de uma poligonal e o ponto de partida. |
| **Poligonal** | Sucessão de alinhamentos que formam um contorno fechado ou aberto. |
| **Limite artificial** | Linha divisória definida por obra humana. |
| **Limite natural** | Linha divisória definida por feição natural. |
| **CNS** | Código Nacional de Serventia (identificador único de cartório no Brasil). |
| **SNCR** | Sistema Nacional de Cadastro Rural. |
| **CCIR** | Certificado de Cadastro de Imóvel Rural. |
| **CIB** | Cadastro de Imóvel Brasileiro. |
| **σP** | Desvio-padrão da posição planimétrica (σP = √(σX² + σY²)). |

---

**Próximo passo**: [`classificacao-divergencias.md`](./classificacao-divergencias.md) — códigos D1–D7 e severidades.
