# DIO - Azure AI Search: Utilizando AI Search para Indexação e Consulta de Dados

Este repositório documenta o projeto prático desenvolvido durante o curso "Azure Cognitive Search: Utilizando AI Search para Indexação e Consulta de Dados" da Digital Innovation One (DIO). O objetivo principal foi explorar os recursos do Azure AI Search (anteriormente conhecido como Azure Cognitive Search) para criar um índice de pesquisa inteligente, aplicar enriquecimento com IA e realizar consultas eficientes sobre os dados.

## Visão Geral do Projeto

O Azure AI Search é um serviço de busca na nuvem totalmente gerenciado que oferece aos desenvolvedores APIs e ferramentas para adicionar uma experiência de pesquisa rica sobre conteúdo heterogêneo em aplicações web, mobile e empresariais. Este projeto focou em compreender e aplicar os conceitos fundamentais do serviço, utilizando a interface do Portal do Azure para configurar e testar uma solução de busca.

## Objetivo

Replicar e documentar o processo de configuração de uma solução de ponta a ponta com Azure AI Search, incluindo:
1.  Criação do serviço Azure AI Search.
2.  Definição de uma Fonte de Dados (Data Source).
3.  Criação de um Índice (Index) para definir a estrutura pesquisável.
4.  (Opcional, mas recomendado) Configuração de um Conjunto de Habilidades (Skillset) para enriquecimento com IA (ex: OCR, extração de frases-chave).
5.  Criação de um Indexador (Indexer) para automatizar o fluxo de dados e enriquecimento.
6.  Realização de consultas no índice através do Search Explorer no Portal do Azure.

## Passo a Passo para Configurar o Azure AI Search (Via Portal do Azure)

A seguir, descrevemos os passos gerais realizados para configurar a solução, baseados na experiência do curso e na exploração da ferramenta:

**Pré-requisitos:**
*   Uma conta ativa do Azure (pode ser a gratuita).
*   Dados de exemplo para indexar (ex: arquivos PDF, JSON, documentos do Office em um Azure Blob Storage, ou dados em um Azure SQL Database).

**Passos:**

1.  **Criar o Serviço Azure AI Search:**
    *   Acesse o [Portal do Azure](https://portal.azure.com/).
    *   Clique em "+ Criar um recurso".
    *   Procure por "Azure AI Search".
    *   Clique em "Criar".
    *   Preencha os detalhes: Grupo de Recursos, Nome do Serviço (único globalmente), Localização e Nível de Preço (o nível 'Free' é suficiente para este projeto).
    *   Revise e crie o serviço. Aguarde o deploy.

2.  **Definir a Fonte de Dados (Data Source):**
    *   No painel do serviço AI Search recém-criado, vá para a seção "Importar dados".
    *   Como alternativa, na visão geral do serviço, clique em "Fontes de Dados" e depois em "+ Adicionar fonte de dados".
    *   Selecione o tipo de fonte de dados (ex: Azure Blob Storage, Azure SQL).
    *   Configure a conexão: forneça um nome para a fonte de dados, insira a string de conexão ou use a identidade gerenciada, e especifique o contêiner/tabela/etc.

3.  **(Opcional) Definir o Conjunto de Habilidades (Skillset) - O Poder da IA:**
    *   *Este passo é crucial para o enriquecimento com IA.*
    *   Se estiver usando o assistente "Importar dados", haverá uma seção para "Adicionar Enriquecimentos (Opcional)".
    *   Expanda esta seção e anexe um serviço de IA do Azure (Azure AI Services - antigo Cognitive Services). Pode ser necessário criar um se não houver. O nível gratuito também funciona para testes.
    *   Selecione as habilidades desejadas (ex: OCR para extrair texto de imagens/PDFs, Extração de Frases-Chave, Reconhecimento de Entidades, Detecção de Idioma).
    *   Configure cada habilidade, definindo as entradas (inputs) e saídas (outputs). Por exemplo, a entrada do OCR geralmente é o conteúdo do arquivo (`/document/content`), e a saída pode ser mapeada para um campo no índice (ex: `merged_content`).

4.  **Definir o Índice (Index):**
    *   *O índice define a estrutura dos dados que serão pesquisáveis.*
    *   Se estiver usando o assistente "Importar dados", a próxima etapa é "Personalizar índice de destino".
    *   Defina os campos do seu índice. O Azure tenta inferir campos da fonte de dados, mas você pode personalizá-los.
    *   Para cada campo, defina:
        *   `Nome`: Nome do campo.
        *   `Tipo`: Tipo de dado (String, Int32, Double, Boolean, DateTimeOffset, Collection, GeographyPoint).
        *   `Atributos`:
            *   `Retornável (Retrievable)`: Pode ser retornado nos resultados da busca.
            *   `Filtrável (Filterable)`: Pode ser usado em expressões de filtro.
            *   `Ordenável (Sortable)`: Pode ser usado para ordenar resultados.
            *   `Facetável (Facetable)`: Pode ser usado para navegação facetada.
            *   `Pesquisável (Searchable)`: Habilita a pesquisa de texto completo no campo.
            *   `Sugestionador (Suggester)`: Usado para sugestões de preenchimento automático (typeahead).
    *   **Importante:** Defina um campo como `Chave (Key)`. Este deve ser único para cada documento no índice e geralmente é uma string.

5.  **Criar o Indexador (Indexer):**
    *   *O indexador conecta a fonte de dados, o índice e o skillset, e automatiza a ingestão e o enriquecimento.*
    *   Se estiver usando o assistente "Importar dados", a última etapa é "Criar um Indexador".
    *   Dê um nome ao indexador.
    *   Configure o `Agendamento (Schedule)`: "Uma vez", "Por Hora", "Diariamente" ou "Personalizado". Para testes, "Uma vez" ou um intervalo curto pode ser útil.
    *   Configure `Opções Avançadas` (ex: tratamento de erros, processamento em lote) se necessário.
    *   Mapeamentos de campo (Field Mappings) podem ser necessários se os nomes dos campos na fonte de dados não corresponderem exatamente aos nomes no índice ou às saídas do skillset.
    *   Clique em "Enviar" ou "Criar". O indexador será executado conforme o agendamento (ou imediatamente, se for "Uma vez").

6.  **Monitorar a Indexação:**
    *   No painel do serviço AI Search, vá para "Indexadores".
    *   Selecione o indexador criado para ver o status da execução, documentos processados, erros, etc.

7.  **Consultar o Índice:**
    *   Após a conclusão bem-sucedida da indexação, vá para o painel do serviço AI Search.
    *   Clique em "Explorador de Pesquisas".
    *   Selecione o índice criado na lista suspensa.
    *   Use a barra de pesquisa para inserir termos de consulta.
    *   Explore opções como:
        *   `Pesquisa Simples`: `search=termo`
        *   `Filtros OData`: `$filter=Campo eq 'valor'`
        *   `Seleção de Campos`: `$select=Campo1,Campo2`
        *   `Ordenação`: `$orderby=Campo asc`
        *   `Facetas`: `facet=Campo`
    *   Observe os resultados retornados em formato JSON.

## Insights e Observações

*   **Facilidade de Uso:** O assistente "Importar dados" no Portal do Azure simplifica enormemente a configuração inicial, especialmente para fontes de dados comuns como Blob Storage e SQL.
*   **Poder do Enriquecimento:** A integração com Azure AI Services (Skillsets) é o grande diferencial. A capacidade de extrair texto de imagens (OCR), identificar entidades ou frases-chave automaticamente agrega um valor imenso aos dados não estruturados.
*   **Importância do Schema do Índice:** Definir corretamente os atributos dos campos (pesquisável, filtrável, facetável, etc.) é crucial para a performance e a funcionalidade da busca. Um bom planejamento inicial evita retrabalho.
*   **Flexibilidade de Consultas:** A sintaxe de consulta, embora possa parecer complexa inicialmente (especialmente com filtros OData), é muito poderosa e permite construir experiências de busca ricas e precisas.
*   **Monitoramento:** Acompanhar o status dos indexadores é fundamental para identificar e corrigir problemas durante a ingestão de dados.

## Ferramentas e Aplicações que se Beneficiam do Azure AI Search

*   **Plataformas de E-commerce:** Para busca de produtos rápida e relevante, com filtros, facetas e sugestões.
*   **Sistemas de Gerenciamento de Documentos (DMS):** Para pesquisar dentro de grandes volumes de documentos (PDFs, Word, etc.), extraindo conteúdo e metadados.
*   **Bases de Conhecimento e Suporte ao Cliente:** Para permitir que usuários e agentes encontrem rapidamente respostas e artigos relevantes.
*   **Aplicações Web e Mobile:** Qualquer aplicação que precise oferecer uma funcionalidade de busca sobre seus próprios dados.
*   **Análise de Logs e Telemetria:** Para indexar e pesquisar grandes volumes de dados de log.
*   **Portais Corporativos e Intranets:** Para busca unificada em diversos repositórios de informação internos.

## Aprendizados Adquiridos

*   Compreensão prática da arquitetura do Azure AI Search (Data Source, Index, Skillset, Indexer).
*   Habilidade em configurar e gerenciar o serviço através do Portal do Azure.
*   Noções de como aplicar enriquecimento de IA (OCR, extração de frases-chave) em um pipeline de indexação.
*   Experiência na definição de schemas de índice e na escolha dos atributos corretos para cada campo.
*   Fundamentos da sintaxe de consulta do Azure AI Search (pesquisa simples, filtros básicos).
*   Valor da integração entre serviços PaaS do Azure para construir soluções complexas de forma eficiente.

## Próximos Passos (Sugestões)

*   Explorar a utilização do Azure AI Search via SDKs (ex: .NET, Python) para integração programática em aplicações.
*   Aprofundar em Skillsets personalizados e mais complexos.
*   Integrar com outros serviços Azure, como Azure Functions (para gatilhos ou skills personalizadas) ou Power BI (para visualização dos dados indexados).
*   Investigar técnicas de otimização de performance e relevância de busca.

## Recursos

*   [Documentação Oficial do Azure AI Search](https://learn.microsoft.com/pt-br/azure/search/)
*   [Explore an Azure AI Search index (UI) - Tutorial Microsoft Learn](https://learn.microsoft.com/pt-br/azure/search/search-explorer) (Link fornecido no desafio)
*   [Curso DIO - Azure Cognitive Search](https://web.dio.me/) (Link genérico para a plataforma)
