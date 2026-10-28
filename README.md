# Análise do Mercado Publicitário Audiovisual

## Contexto

Este projeto foi desenvolvido durante a formação em **Análise de Dados da EBAC**, utilizando dados públicos da **Agência Nacional do Cinema (ANCINE)** sobre o mercado publicitário audiovisual brasileiro.

Foram utilizados três conjuntos de dados: registros de obras publicitárias por meio do **Certificado de Registro de Título (CRT)**, agentes econômicos regulares e atividades econômicas dos agentes regulares.

Como a base de obras publicitárias estava distribuída em múltiplos arquivos, foi necessário consolidar **164 arquivos CSV** antes de realizar as validações, o tratamento e as análises.

O desenvolvimento reuniu **Python e Pandas** para preparação e análise dos dados, **Google BigQuery** para disponibilização da base preparada e **Looker Studio** para construção do dashboard.

## Objetivo

Analisar os registros de obras publicitárias disponibilizados pela ANCINE, identificando padrões de evolução temporal, segmentos de mercado, tipos de publicidade, duração das obras e distribuição geográfica dos requerentes.

Relacionar os registros de CRTs aos agentes econômicos e às suas atividades, complementando a análise com informações das outras duas bases.

Apresentar os principais indicadores e resultados em um dashboard interativo.

## Desenvolvimento

### Fontes e Estrutura dos Dados

Os dados foram obtidos no portal de [Dados Abertos da ANCINE](https://www.gov.br/ancine/pt-br/oca/dados-abertos).

Foram utilizados os seguintes conjuntos:

- Obras Publicitárias — Certificado de Registro de Título (CRT);
- Agentes Econômicos Regulares Registrados na ANCINE;
- Atividades Econômicas dos Agentes Regulares Registrados na ANCINE.

Após o carregamento, as bases apresentaram:

| Base | Registros | Colunas |
|---|---|---|
| CRTs de obras publicitárias, consolidada a partir de 164 arquivos | 560.295 | 25 |
| Agentes econômicos | 18.884 | 11 |
| Atividades econômicas | 147.288 | 5 |

A base consolidada de obras publicitárias continha **535.494 CRTs únicos**.

### Consolidação e Validação dos Arquivos

Os 164 arquivos CSV foram carregados e consolidados com Python e Pandas.

A validação inicial verificou:

- Quantidade de arquivos e registros;
- Quantidade de CRTs únicos;
- Existência de CRTs repetidos;
- Estrutura e quantidade de colunas;
- Arquivos vazios;
- Erros de leitura.

Todos os arquivos apresentaram a mesma estrutura de **25 colunas**, sem arquivos vazios ou erros de leitura.

Também foi investigada a repetição dos registros:

- **14.886 CRTs** estavam presentes em mais de um registro;
- **39.687 linhas** pertenciam a CRTs repetidos;
- Não foram encontradas linhas totalmente duplicadas.

A investigação identificou diferenças em campos como diretor, produtor, anunciante e agência para registros de um mesmo CRT. Por esse motivo, as linhas não foram removidas apenas pela repetição do código.

Nas análises de quantidade de obras, foi utilizada a **contagem distinta de CRTs**.

### Tratamento e Preparação dos Dados

Foram realizados procedimentos de:

- Verificação de valores ausentes;
- Investigação de inconsistências e valores textuais especiais;
- Padronização e conversão de datas para `datetime`;
- Verificação de valores extremos nas variáveis numéricas;
- Padronização dos identificadores utilizados nos relacionamentos;
- Criação de variáveis derivadas.

O valor **“NÃO INFORMADO”**, encontrado em registros relacionados à agência, foi interpretado como ausência de informação.

O valor **“PESSOA FÍSICA”**, presente em campos de CNPJ, foi preservado por representar uma informação válida sobre o tipo de requerente ou anunciante.

Na análise de duração:

- Registros com **24 horas** foram mantidos após a identificação de sua associação com obras de televenda de período de 24 horas;
- **27 registros com duração zero** foram preservados na base geral, mas desconsiderados nas análises estatísticas de duração.

Também foram criadas as colunas:

- `ANO_REQUERIMENTO`: ano extraído da data de requerimento do CRT;
- `DURACAO_MINUTOS`: duração convertida de segundos para minutos.

### Relacionamento entre as Bases

Foram avaliados o **CNPJ do requerente** e o **Registro ANCINE** como possíveis chaves de relacionamento entre CRTs e agentes econômicos.

Após a padronização, o CNPJ apresentou cobertura de aproximadamente **72,54%** entre os registros com CNPJ válido da base de CRTs e a base de agentes.

O relacionamento entre agentes e atividades econômicas apresentou **100% de correspondência** entre os agentes com CNPJ válido.

Com base nessa validação, o CNPJ foi utilizado como principal chave para relacionar:

**CRTs → Agentes Econômicos → Atividades Econômicas**

O cruzamento apresentou:

| Etapa | Linhas | CRTs únicos |
|---|---|---|
| CRTs relacionados aos agentes econômicos | 398.210 | 377.871 |
| Inclusão das atividades econômicas | 2.341.538 | 377.871 |

O aumento de linhas ocorreu porque um agente pode possuir múltiplas atividades econômicas. A contagem distinta de CRTs foi mantida nas análises de obras para evitar contabilizações repetidas.

### Análise Exploratória

A análise foi desenvolvida em Python e contemplou:

- Evolução anual dos CRTs únicos;
- Distribuição por segmento de mercado;
- Distribuição por tipo de publicidade;
- Duração das obras;
- Atividades econômicas dos agentes associados aos CRTs;
- Distribuição por UF do requerente;
- Relação entre segmento e tipo de publicidade.

Na análise das atividades econômicas, foram consideradas principalmente as atividades classificadas como **principais**, utilizando CRTs distintos.

A análise geográfica utilizou a **UF do requerente do CRT**, que não representa necessariamente o local de produção ou exibição da obra.

### Desenvolvimento do Dashboard

A base preparada foi armazenada no **Google BigQuery** e utilizada como fonte de dados para o dashboard no **Looker Studio**.

O relatório foi organizado em duas páginas.

#### Visão Geral

Apresenta os indicadores:

- Total de obras publicitárias;
- Duração mediana;
- Quantidade de UFs representadas.

Também reúne a evolução anual dos registros e a distribuição por segmento de mercado.

#### Análise Detalhada

Apresenta:

- Distribuição geográfica por UF do requerente;
- Distribuição por tipo de publicidade;
- Principais conclusões;
- Recomendações baseadas nos resultados.

## Resultados

### Panorama dos Registros

A consolidação dos **164 arquivos CSV** resultou em uma base com **560.295 registros e 535.494 CRTs únicos**.

Os dados analisados abrangem o período de **2013 a 2026**, com **27 UFs representadas**.

A evolução anual apresentou oscilações, sem uma tendência contínua de crescimento. Os dados de **2026 são parciais** e devem ser interpretados considerando essa limitação.

### Segmentos de Mercado

Foi identificada uma concentração dos registros em **TV Aberta**, correspondente a aproximadamente **65,6% dos CRTs analisados**.

| Segmento | CRTs únicos |
|---|---|
| TV Aberta | 351.161 |
| Todos os Segmentos | 141.515 |
| TV Paga | 24.657 |
| Outros Mercados | 14.218 |
| Salas de Exibição | 3.547 |
| Vídeo Doméstico | 396 |

Cada CRT estava associado a apenas um segmento na base utilizada.

### Tipos de Publicidade

A publicidade classificada como **Comum** apresentou predominância:

| Tipo de publicidade | CRTs únicos |
|---|---|
| Comum | 515.703 |
| Televenda / Infomercial | 11.960 |
| Beneficente / Filantrópico | 7.752 |
| Período de 24h / Televenda | 79 |

A categoria Comum predominou nos diferentes segmentos. Televenda / Infomercial apresentou maior presença relativa principalmente nos registros associados à TV Aberta.

### Duração das Obras

A duração mediana foi de **30 segundos**, com concentração em formatos curtos.

Nas análises de duração, aproximadamente:

- **86,27%** dos registros tinham até 30 segundos;
- **96,76%** tinham até 1 minuto;
- **99,15%** tinham até 5 minutos.

As durações mais frequentes foram **30, 15 e 60 segundos**.

### Distribuição Geográfica

São Paulo apresentou a maior quantidade de CRTs por UF do requerente:

| UF do requerente | CRTs |
|---|---|
| São Paulo | 169.198 |
| Paraná | 57.838 |
| Santa Catarina | 42.824 |
| Rio Grande do Sul | 41.302 |
| Minas Gerais | 38.105 |
| Rio de Janeiro | 33.765 |

Esses resultados representam a localização dos requerentes, não necessariamente os locais de produção ou exibição das obras.

### Atividades Econômicas

O cruzamento das bases permitiu identificar atividades com maior quantidade de CRTs relacionados, incluindo:

- Produção audiovisual;
- Produção de filmes para publicidade;
- Pós-produção audiovisual;
- Agências de publicidade;
- Televisão aberta.

### Entregas do Projeto

O projeto resultou na consolidação e validação dos arquivos de CRTs, no tratamento e relacionamento das três bases e no desenvolvimento de análises exploratórias em Python.

Os dados preparados foram disponibilizados no BigQuery e apresentados em um dashboard de duas páginas no Looker Studio.

### Recomendações Derivadas da Análise

Os resultados reforçaram a importância de considerar as diferenças entre segmentos, formatos e regiões na análise dos registros.

Foi recomendado acompanhar a evolução dos CRTs ao longo do tempo e as diferenças entre UFs para identificar mudanças na distribuição das obras publicitárias registradas.

## Habilidades Demonstradas

- Análise de dados;
- Python;
- Pandas;
- Google BigQuery;
- Looker Studio;
- DataFrames;
- Consolidação de arquivos CSV;
- Validação de dados;
- Tratamento de valores ausentes;
- Investigação de duplicidades;
- Padronização de dados;
- Manipulação de datas;
- Criação de variáveis derivadas;
- Relacionamento entre tabelas;
- Validação de chaves de relacionamento;
- Análise exploratória de dados;
- Análise temporal;
- Análise por categoria;
- Análise geográfica;
- Análise de distribuição;
- Visualização de dados;
- Construção de dashboards;
- Indicadores e KPIs;
- Análise e interpretação de dados;
- Recomendações baseadas em dados;
- Documentação técnica.

## Arquivos do Projeto

Os arquivos estão organizados entre as bases utilizadas, o notebook de preparação e análise e o dashboard.

### [Notebook — Análise Exploratória](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/blob/main/notebooks/analise_exploratoria.ipynb)

Notebook Jupyter com carregamento e consolidação dos arquivos, validações, tratamento e preparação dos dados, relacionamento entre as três bases, análises exploratórias e visualizações em Python.

### [Dados Utilizados no Projeto](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/tree/main/data)

A pasta contém as bases de agentes econômicos e atividades econômicas.

A base de CRTs utilizada na análise é composta por **164 arquivos CSV**. Devido ao volume dos arquivos, a base completa deve ser obtida na fonte oficial: [Dados Abertos da ANCINE](https://www.gov.br/ancine/pt-br/oca/dados-abertos)

### [Dashboard — Versão em PDF](https://github.com/Jucastilla/analise-mercado-publicitario-ancine/blob/main/dashboard/An%C3%A1lise_do_Mercado_Publicit%C3%A1rio_Audiovisual.pdf)

Versão em PDF do dashboard para consulta das principais visualizações e resultados do projeto.
