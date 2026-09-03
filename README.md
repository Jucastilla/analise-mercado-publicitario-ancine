# Análise do Mercado Publicitário Audiovisual — ANCINE

## Sobre o Projeto

Este projeto foi desenvolvido durante a formação em **Análise de Dados da EBAC**, com o objetivo de analisar os registros de obras publicitárias do mercado audiovisual brasileiro disponibilizados pela **Agência Nacional do Cinema (ANCINE)**.

A análise foi desenvolvida a partir de três conjuntos de dados públicos da ANCINE: registros de obras audiovisuais publicitárias por meio do **Certificado de Registro de Título (CRT)**, agentes econômicos regulares e atividades econômicas dos agentes regulares.

A base de obras publicitárias é disponibilizada de forma fragmentada. Para o desenvolvimento do projeto, foram utilizados e consolidados **164 arquivos CSV**, posteriormente submetidos a processos de validação, tratamento, preparação e análise exploratória utilizando **Python e Pandas**.

O projeto também inclui o cruzamento entre as três bases, permitindo relacionar os registros das obras publicitárias aos agentes econômicos e às suas respectivas atividades econômicas.

Para a etapa de visualização, os dados preparados foram disponibilizados no **Google BigQuery** e utilizados como fonte para a construção de um dashboard interativo no **Looker Studio**, contendo indicadores, análises temporais, distribuição por segmento, tipo de publicidade e distribuição geográfica dos registros.

O projeto reúne **Python, Pandas, Google BigQuery e Looker Studio**, contemplando desde a consolidação e validação dos dados públicos até a análise exploratória e comunicação dos resultados.

## Objetivo

Analisar os registros de obras publicitárias disponibilizados pela ANCINE, buscando identificar padrões relacionados à evolução temporal dos registros, segmentos de mercado, tipos de publicidade, duração das obras, distribuição geográfica e atividades econômicas dos agentes relacionados aos CRTs.

O projeto também busca demonstrar a aplicação de diferentes etapas de um processo de análise de dados, incluindo **seleção, consolidação, validação, tratamento, preparação, cruzamento de tabelas, análise exploratória e visualização dos dados**.

## Etapas Desenvolvidas

### 1. Fonte e Estrutura dos Dados

Os dados utilizados no projeto foram obtidos no portal de **Dados Abertos da ANCINE**:

**Fonte oficial:**  
https://www.gov.br/ancine/pt-br/oca/dados-abertos

Foram utilizados três conjuntos de dados:

- **Obras Publicitárias — Certificado de Registro de Título (CRT)**
- **Agentes Econômicos Regulares Registrados na ANCINE**
- **Atividades Econômicas dos Agentes Regulares Registrados na ANCINE**

A base de CRTs de obras publicitárias é disponibilizada pela ANCINE em múltiplos arquivos. Para esta análise, foram utilizados **164 arquivos CSV**, que foram carregados e consolidados utilizando Python.

Após a consolidação dos arquivos de CRT, foram obtidos:

- **164 arquivos CSV analisados**
- **560.295 registros**
- **535.494 CRTs únicos**
- **25 colunas**
- **14.886 CRTs presentes em mais de um registro**

As outras duas bases utilizadas apresentam:

- **18.884 registros e 11 colunas** na base de agentes econômicos;
- **147.288 registros e 5 colunas** na base de atividades econômicas.

### 2. Consolidação e Validação dos Dados

Como os registros de obras publicitárias estavam distribuídos entre **164 arquivos CSV**, foi realizada inicialmente uma validação estrutural dos arquivos antes da análise.

A validação verificou:

- Quantidade de arquivos encontrados;
- Quantidade total de registros;
- Quantidade de CRTs únicos;
- Existência de CRTs repetidos;
- Estrutura e quantidade de colunas;
- Existência de arquivos vazios;
- Possíveis erros durante a leitura dos arquivos.

Os **164 arquivos apresentaram a mesma estrutura de 25 colunas**, sem identificação de arquivos vazios ou erros de leitura.

Após a consolidação, também foi verificada a existência de registros duplicados.

Embora tenham sido identificados **14.886 CRTs presentes em mais de um registro**, não foram encontradas linhas totalmente duplicadas. Foram identificadas **39.687 linhas pertencentes a CRTs repetidos**.

A investigação mostrou que um mesmo CRT poderia estar associado a diferenças em campos como diretor, produtor, anunciante e agência. Por esse motivo, os registros não foram removidos apenas com base na repetição do código de CRT.

Para análises relacionadas à quantidade de obras publicitárias, foi utilizada a **contagem distinta de CRTs**, evitando que registros associados ao mesmo CRT fossem contabilizados como obras diferentes.

### 3. Tratamento e Preparação dos Dados

Após a validação inicial, foram realizados procedimentos de tratamento e preparação dos dados.

Entre os procedimentos desenvolvidos estão:

- Verificação de valores ausentes;
- Investigação de inconsistências e valores textuais especiais;
- Padronização dos campos de data;
- Conversão das colunas de data para formato `datetime`;
- Verificação de valores extremos nas variáveis numéricas;
- Padronização de identificadores utilizados nos relacionamentos entre as tabelas;
- Criação de variáveis derivadas para apoiar as análises.

Durante a análise dos valores ausentes, foi identificada a ocorrência de valores textuais com significados diferentes.

O valor **"NÃO INFORMADO"**, presente em registros relacionados à agência, foi interpretado como ausência de informação. Já o valor **"PESSOA FÍSICA"**, encontrado em campos de CNPJ, foi preservado por representar uma informação válida sobre o tipo de requerente ou anunciante.

Também foram investigados valores extremos na duração das obras. Registros com duração de **24 horas** foram mantidos após a verificação de que estavam associados a obras de televenda com período de 24 horas.

Foram identificados **27 registros com duração igual a zero**. Esses registros foram preservados na base geral, mas desconsiderados especificamente nas análises estatísticas de duração.

Duas novas colunas foram criadas durante a preparação:

- `ANO_REQUERIMENTO` — ano extraído da data de requerimento do CRT;
- `DURACAO_MINUTOS` — conversão da duração da obra de segundos para minutos.

### 4. Relacionamento entre as Bases

Antes do cruzamento das três tabelas, foram avaliadas possíveis chaves de relacionamento.

Para relacionar a base de CRTs aos agentes econômicos, foram analisados o **CNPJ do requerente** e o **Registro ANCINE**.

Após a padronização dos identificadores, o CNPJ apresentou cobertura de aproximadamente **72,54%** entre os registros com CNPJ válido da base de CRT e a base de agentes econômicos.

O relacionamento entre agentes econômicos e atividades econômicas apresentou **100% de correspondência** entre os agentes com CNPJ válido.

Com base nessa validação, o CNPJ foi utilizado como principal chave de relacionamento:

`CRT → Agentes Econômicos → Atividades Econômicas`

O cruzamento entre CRTs e agentes econômicos resultou em:

- **398.210 registros**
- **377.871 CRTs únicos**

Após a inclusão das atividades econômicas, o conjunto resultante apresentou **2.341.538 linhas**, mantendo os mesmos **377.871 CRTs únicos**.

O aumento da quantidade de linhas ocorre porque um mesmo agente econômico pode possuir múltiplas atividades econômicas. Por esse motivo, as análises de obras continuaram utilizando a contagem de **CRTs únicos**.

### 5. Análise Exploratória dos Dados

A análise exploratória foi desenvolvida em Python e contemplou diferentes perspectivas sobre os registros de obras publicitárias.

#### Evolução Temporal

Foi analisada a quantidade anual de CRTs únicos registrados ao longo do período disponível.

Os registros analisados abrangem o período entre **2013 e 2026**.

A evolução anual apresenta oscilações ao longo dos anos, sem uma tendência contínua de crescimento. Os dados de **2026 são parciais**, devendo ser interpretados considerando que o ano ainda não está completo na base utilizada.

#### Distribuição por Segmento de Mercado

A análise por segmento identificou uma forte concentração dos registros em **TV Aberta**.

Entre os CRTs únicos analisados:

- **TV Aberta:** 351.161
- **Todos os Segmentos:** 141.515
- **TV Paga:** 24.657
- **Outros Mercados:** 14.218
- **Salas de Exibição:** 3.547
- **Vídeo Doméstico:** 396

A categoria **TV Aberta representa aproximadamente 65,6% dos CRTs analisados**.

Também foi verificado que cada CRT está associado a apenas um segmento na base utilizada, permitindo analisar a composição proporcional dos registros por segmento.

#### Tipo de Publicidade

A categoria **Comum** apresentou ampla predominância entre os tipos de publicidade registrados.

Foram identificados:

- **515.703 CRTs** classificados como Comum;
- **11.960 CRTs** de Televenda / Infomercial;
- **7.752 CRTs** de caráter Beneficente / Filantrópico;
- **79 CRTs** de Período de 24h / Televenda.

#### Duração das Obras

A análise das durações mostrou forte concentração em formatos de curta duração.

A **mediana é de 30 segundos**, e aproximadamente:

- **86,27%** dos registros possuem até 30 segundos;
- **96,76%** possuem até 1 minuto;
- **99,15%** possuem até 5 minutos.

Entre as durações mais frequentes, destacam-se obras de **30 segundos, 15 segundos e 60 segundos**.

#### Atividades Econômicas

O cruzamento das três bases permitiu analisar as principais atividades econômicas dos agentes associados aos CRTs.

Como um agente pode possuir diferentes atividades econômicas, a análise foi direcionada principalmente às atividades classificadas como **principais**, utilizando CRTs distintos para evitar duplicidade na contabilização das obras.

Entre as atividades com maior quantidade de CRTs relacionados aparecem atividades ligadas à **produção audiovisual, produção de filmes para publicidade, pós-produção audiovisual, agências de publicidade e televisão aberta**.

#### Distribuição Geográfica

A distribuição geográfica foi analisada utilizando a **UF do requerente do CRT**.

Os estados com maior quantidade de CRTs foram:

- **São Paulo:** 169.198
- **Paraná:** 57.838
- **Santa Catarina:** 42.824
- **Rio Grande do Sul:** 41.302
- **Minas Gerais:** 38.105
- **Rio de Janeiro:** 33.765

A variável representa a localização do **requerente do CRT**, não necessariamente o local de produção ou exibição da obra publicitária.

#### Relação entre Segmento e Tipo de Publicidade

Também foi analisada a relação entre os segmentos de mercado e os tipos de publicidade.

A análise mostrou que a categoria **Comum** apresenta predominância nos diferentes segmentos, enquanto Televenda / Infomercial possui maior presença relativa principalmente nos registros associados à TV Aberta.

### 6. Desenvolvimento do Dashboard no Looker Studio

Foi desenvolvido um dashboard interativo no **Looker Studio** para apresentar os principais resultados da análise.

Para disponibilização dos dados ao Looker Studio, a base preparada foi armazenada no **Google BigQuery**, utilizado como fonte de dados do dashboard.

O relatório foi organizado em **duas páginas**.

#### Visão Geral

A primeira página apresenta os principais indicadores e uma visão geral dos registros de obras publicitárias.

Entre os indicadores apresentados estão:

- **535.494 obras publicitárias**
- **30 segundos de duração mediana**
- **27 UFs representadas**

A página também apresenta:

- Evolução anual das obras publicitárias registradas;
- Distribuição das obras por segmento de mercado.

#### Análise Detalhada

A segunda página apresenta análises complementares sobre os registros.

São apresentados:

- Distribuição geográfica dos registros por UF do requerente;
- Distribuição das obras por tipo de publicidade;
- Principais conclusões da análise;
- Recomendações baseadas nos resultados encontrados.

A utilização do Looker Studio permite apresentar os resultados de forma interativa e complementar às análises desenvolvidas no notebook Python.

### 7. Análise dos Resultados

A análise evidencia uma forte concentração das obras publicitárias registradas no segmento de **TV Aberta**, responsável por aproximadamente **65,6% dos CRTs analisados**.

Também foi identificado amplo predomínio da categoria de publicidade **Comum**, com **515.703 obras registradas**.

Em relação à duração, os formatos curtos representam a maior parte dos registros. A duração mediana é de **30 segundos**, e mais de 96% dos registros analisados possuem duração de até um minuto.

Geograficamente, **São Paulo** apresenta a maior concentração de registros por UF do requerente, com **169.198 CRTs**, seguido por Paraná e Santa Catarina.

A evolução anual apresenta oscilações ao longo do período analisado, sem uma tendência contínua de crescimento. Os resultados de 2026 devem ser interpretados como parciais.

O cruzamento com as bases de agentes e atividades econômicas também permitiu observar a presença relevante de atividades relacionadas à produção audiovisual, produção de filmes para publicidade, pós-produção e agências de publicidade entre os agentes associados aos CRTs.

### 8. Conclusões

O projeto permitiu analisar os registros do mercado publicitário audiovisual disponibilizados pela ANCINE utilizando diferentes etapas e ferramentas de análise de dados.

Com **Python e Pandas**, foram consolidados **164 arquivos CSV**, realizadas verificações de qualidade, investigados registros repetidos, tratados formatos de dados e desenvolvidas análises exploratórias.

O relacionamento entre as três bases permitiu complementar a análise dos CRTs com informações sobre os agentes econômicos e suas atividades.

O **Google BigQuery** foi utilizado para disponibilizar os dados preparados para o **Looker Studio**, onde foi desenvolvido um dashboard interativo com os principais indicadores e resultados.

Os resultados mostram uma forte concentração dos registros em **TV Aberta**, predominância da publicidade classificada como **Comum**, forte presença de formatos de curta duração e concentração geográfica dos requerentes em São Paulo.

Os resultados reforçam a importância de considerar as diferenças entre segmentos, formatos e regiões ao analisar os registros do mercado publicitário audiovisual. Recomenda-se acompanhar a evolução dos registros ao longo do tempo e as diferenças entre UFs, buscando identificar mudanças na distribuição das obras publicitárias registradas.

## Habilidades Demonstradas

- Análise de Dados
- Python
- Pandas
- Google BigQuery
- Looker Studio
- DataFrames
- Consolidação de Arquivos CSV
- Validação de Dados
- Tratamento de Valores Ausentes
- Investigação de Duplicidades
- Padronização de Dados
- Manipulação de Datas
- Criação de Variáveis Derivadas
- Relacionamento entre Tabelas
- Validação de Chaves de Relacionamento
- Análise Exploratória de Dados
- Análise Temporal
- Análise por Categoria
- Análise Geográfica
- Análise de Distribuição
- Visualização de Dados
- Construção de Dashboards
- Indicadores e KPIs
- Análise e Interpretação de Dados
- Recomendações Baseadas em Dados
- Documentação Técnica

## Arquivos do Projeto

Os principais arquivos do projeto estão organizados entre as bases públicas utilizadas, o notebook responsável pela preparação e análise dos dados e os arquivos relacionados ao dashboard.

### Notebook

📓 [Notebook - Análise Exploratória](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/blob/main/notebooks/analise_exploratoria.ipynb)

Notebook Jupyter contendo o carregamento e a consolidação dos dados, validações de qualidade, tratamento e preparação das bases, relacionamento entre as três tabelas, análises exploratórias e visualizações desenvolvidas em Python.

### Dados

📁 [Dados utilizados no projeto](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/tree/main/data)

A pasta contém as bases de agentes econômicos e atividades econômicas utilizadas no projeto.

A base de CRTs de obras publicitárias utilizada na análise é composta por **164 arquivos CSV** disponibilizados pela ANCINE. Devido ao volume dos arquivos, a base completa deve ser obtida diretamente na fonte oficial.

**Fonte:** [Dados Abertos da ANCINE](https://www.gov.br/ancine/pt-br/oca/dados-abertos)

### Dashboard

📄 [Dashboard - Versão em PDF](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/blob/main/dashboard/An%C3%A1lise_do_Mercado_Publicit%C3%A1rio_Audiovisual.pdf)

Versão em PDF do dashboard para consulta das principais visualizações e resultados apresentados no projeto.
