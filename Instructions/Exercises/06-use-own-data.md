---
lab:
  title: Implementar a RAG (Geração Aumentada de Recuperação) com o Serviço Azure OpenAI
---

# Implementar a RAG (Geração Aumentada de Recuperação) com o Serviço Azure OpenAI

O Serviço OpenAI do Azure permite que você use dados próprios com a inteligência do LLM subjacente. Você pode limitar o modelo para usar apenas seus dados para tópicos pertinentes ou misturá-los com os resultados do modelo pré-treinado.

No cenário deste exercício, você executará a função de um desenvolvedor de software que trabalha para a Margie's Travel Agency. Você explorará como usar a Pesquisa de IA do Azure para indexar seus próprios dados e usá-los com o OpenAI do Azure para aumentar os prompts.

Este exercício levará aproximadamente **30** minutos.

## Provisionar recursos do Azure

Para concluir este exercício, você precisa de:

- Um recurso do Azure OpenAI.
- Um recurso da Pesquisa de IA do Azure.
- Um recurso da conta de armazenamento do Azure.

1. Entre no **portal do Azure** em `https://portal.azure.com`.
2. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: *Selecione uma assinatura do Azure que tenha sido aprovada para acesso ao serviço Azure OpenAI*
    - **Grupo de recursos**: *escolher ou criar um grupo de recursos*
    - **Região**: *faça uma escolha **aleatória** de uma das regiões a seguir*\*
        - Leste da Austrália
        - Leste do Canadá
        - Leste dos EUA
        - Leste dos EUA 2
        - França Central
        - Leste do Japão
        - Centro-Norte dos EUA
        - Suécia Central
        - Norte da Suíça
        - Sul do Reino Unido
    - **Nome**: *um nome exclusivo de sua preferência*
    - **Tipo de preço**: Standard S0

    > \* Os recursos do OpenAI do Azure são restritos por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. A escolha aleatória de uma região reduz o risco de uma só região atingir o limite de cota em cenários nos quais você compartilha uma assinatura com outros usuários. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

3. Enquanto o recurso OpenAI do Azure está sendo provisionado, crie um recurso da **Pesquisa de IA do Azure** com as seguintes configurações:
    - **Assinatura**: *a assinatura na qual você provisionou o recurso do OpenAI do Azure*
    - **Grupo de recursos**: *O grupo de recursos no qual você provisionou seu recurso Azure OpenAI*
    - **Nome do serviço**: *um nome exclusivo de sua preferência*
    - **Localização**: *a região onde você provisionou seu recurso do OpenAI do Azure*
    - **Tipo de preço**: Básico
4. Enquanto o recurso da Pesquisa de IA do Azure estiver sendo provisionado, crie um recurso de **conta de armazenamento** com as seguintes configurações:
    - **Assinatura**: *a assinatura na qual você provisionou o recurso do OpenAI do Azure*
    - **Grupo de recursos**: *O grupo de recursos no qual você provisionou seu recurso Azure OpenAI*
    - **Nome da conta de armazenamento**: *um nome exclusivo de sua escolha*
    - **Região**: *a região onde você provisionou seu recurso do OpenAI do Azure*
    - **Desempenho**: padrão
    - **Redundância**: LRS (armazenamento com redundância local)
5. Depois que os três recursos tiverem sido implantados em sua assinatura do Azure, revise-os no portal do Azure e reúna as seguintes informações (que você precisará mais adiante no exercício):
    - O **ponto de extremidade** e uma **chave** do recurso Azure OpenAI que você criou (disponível na página **Chaves e Ponto de Extremidade** para seu recurso Azure OpenAI no portal do Azure)
    - O ponto de extremidade do serviço Pesquisa de IA do Azure (o valor de **Url** na página de visão geral do seu recurso de pesquisa no portal do Azure).
    - Uma **chave de administrador primária** para o recurso Pesquisa de IA do Azure (disponível na página **Chaves** do recurso Pesquisa de IA do Azure no portal do Azure).

## Upload de dados

Você vai fundamentar os prompts que usa com um modelo de IA generativa usando seus próprios dados. Neste exercício, os dados consistem em uma coleção de folhetos de viagem da empresa fictícia *Margies Travel*.

1. Em uma nova guia do navegador, baixe um arquivo de dados de folheto de `https://aka.ms/own-data-brochures`. Extraia os folhetos para uma pasta no computador.
1. No portal do Azure no navegador, acesse a sua conta de armazenamento e visualize a página **Navegador de armazenamento**.
1. Escolha **Contêineres de blob** e adicione um novo contêiner chamado `margies-travel`.
1. Escolha o contêiner **margies-travel** e carregue os folhetos .pdf extraídos anteriormente para a pasta raiz do contêiner de blob.

## Implantar modelos de IA

Você usará dois modelos de IA neste exercício:

- Um modelo de inserção de texto para *vetorizar* o texto nos folhetos para que ele possa ser indexado de forma eficiente para uso em prompts de fundamentação.
- Um modelo GPT que seu aplicativo pode usar para gerar respostas a prompts que são fundamentados em seus dados.

Para implantar esses modelos, você usará a Fábrica de IA.

1. No portal do Azure, navegue até o recurso OpenAI do Azure. Em seguida, use o link para abrir seu recurso no **Portal da Fábrica de IA do Azure**.
1. No Portal da Fábrica de IA do Azure, na página **Implantações**, exiba as implantações de modelo existentes. Depois, crie uma nova implantação do modelo base **text-embedding-ada-002** com as seguintes configurações:
    - **Nome da implantação**: text-embedding-ada-002
    - **Modelo**: text-embedding-ada-002
    - **Versão do modelo**: *a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Habilitado
1. Depois que o modelo de inserção de texto tiver sido implantado, volte para a página **Implantações** e crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Nome da implantação**: gpt-35-turbo-16k
    - **Modelo**: gpt-35-turbo-16k *(se o modelo 16k não estiver disponível, escolha gpt-35-turbo)*
    - **Versão do modelo**: *a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Habilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Crie um índice

Para facilitar o uso de seus próprios dados em um prompt, você os indexará usando a Pesquisa de IA do Azure. Você usará o modelo de inserção de texto implantado anteriormente durante o processo de indexação para *vetorizar* os dados de texto (o que resulta em cada token de texto no índice sendo representado por vetores numéricos, tornando-o compatível com a maneira como um modelo de IA generativa representa o texto)

1. No portal do Azure, navegue até o recurso Pesquisa de IA do Azure.
1. Na página **Visão geral**, selecione **Importar e vetorizar dados**.
1. Na página **Configurar conexão de dados**, escolha **Armazenamento de Blobs do Azure** e configure a fonte de dados com o seguinte:
    - **Assinatura**: a assinatura do Azure na qual você provisionou sua conta de armazenamento.
    - **Conta de armazenamento de Blobs**: a conta de armazenamento que você criou anteriormente.
    - **Contêiner de blob**: margies-travel
    - **Pasta de blob**: *deixe em branco*
    - **Ativar rastreamento de exclusão**: desmarcado
    - **Autenticar usando a identidade gerenciada**: desmarcado
1. Na página **Vetorizar texto**, selecione as seguintes configurações:
    - **Tipo**: OpenAI do Azure
    - **Assinatura**: a assinatura do Azure na qual você provisionou o serviço OpenAI do Azure.
    - **Serviço OpenAI do Azure**: o recurso do Serviço OpenAI do Azure
    - **Implantação de modelo**: text-embedding-ada-002
    - **Tipo de autenticação**: chave de API
    - **Reconheço que a conexão com um serviço OpenAI do Azure incorrerá em custos adicionais para minha conta**: selecionado
1. Na próxima página, <u>não</u> selecione a opção para vetorizar imagens ou extrair dados com habilidades de IA.
1. Na próxima página, habilite a classificação semântica e agende o indexador para ser executado uma vez.
1. Na página final, defina o **prefixo do nome Objetos** como `margies-index` e crie o índice.

## Preparar-se para desenvolver um aplicativo no Visual Studio Code

Agora vamos explorar o uso da engenharia imediata em um aplicativo que usa o SDK do serviço OpenAI do Azure. Você desenvolverá seu aplicativo usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: Se você já clonou o repositório **mslearn-openai**, abra-o no código do Visual Studio. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
2. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

## Configurar seu aplicativo

Foram fornecidos aplicativos para C# e Python, e ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes-chave do aplicativo para habilitar o uso do recurso do OpenAI do Azure.

1. No Visual Studio Code, no painel do **Explorer**, navegue até a pasta **Labfiles/06-code-generation** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de idioma. Cada pasta contém os arquivos específicos do idioma de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
2. Clique com o botão direito na pasta **CSharp** ou **Python** que contém seus arquivos de código e abra um terminal integrado. Em seguida, instale o pacote do SDK do OpenAI do Azure executando o comando apropriado para sua preferência de idioma:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**:

    ```
    pip install openai==1.55.3
    ```

3. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de configuração do seu idioma preferido

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Atualize os valores da configuração para incluir:
    - O **ponto de extremidade** e uma **chave** do recurso Azure OpenAI que você criou (disponível na página **Chaves e Ponto de Extremidade** para seu recurso Azure OpenAI no portal do Azure)
    - O **nome da implantação** que você especificou para a implantação do modelo gpt-35-turbo (disponível na página **Implantações** no Portal da Fábrica de IA do Azure).
    - O ponto de extremidade do seu serviço de pesquisa (o valor de **Url** na página de visão geral do seu recurso de pesquisa no portal do Azure).
    - Uma **chave** para seu recurso de pesquisa (disponível na página **Chaves** para seu recurso de pesquisa no portal do Azure - você pode usar qualquer uma das chaves de administrador)
    - O nome do índice de pesquisa (que deve ser `margies-index`).
5. Salve o arquivo de configuração.

### Adicione código para usar o serviço Azure OpenAI

Agora você está pronto para usar o SDK do Azure OpenAI para consumir seu modelo implantado.

1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código para seu idioma preferido e substitua o comentário ***Adicionar pacote Azure OpenAI*** pelo código para adicionar a biblioteca do SDK do OpenAI do Azure:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. Examine o restante do código, observando o uso das *extensões* no corpo da solicitação que é usado para fornecer informações sobre as configurações da fonte de dados.

3. Salve as alterações no arquivo de código.

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta. Você observará que a única diferença entre as várias opções é o conteúdo do prompt, todos os outros parâmetros (como contagem de tokens e temperatura) permanecem os mesmos para cada solicitação.

1. No painel do terminal interativo, verifique se o contexto da pasta é a pasta da sua linguagem de preferência. Em seguida, insira o comando a seguir para executar o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python ownData.py`

    > **Dica**: você pode usar o ícone **Maximizar tamanho do painel** (**^**) na barra de ferramentas do terminal para ver mais do texto do console.

2. Examine a resposta para o prompt `Tell me about London`, que deve incluir uma resposta, bem como alguns detalhes dos dados usados para aterrar o prompt, que foi obtido do serviço de pesquisa.

    > **Dica**: Se você quiser ver as citações do seu índice de pesquisa, defina a variável ***show citations*** próxima ao topo do arquivo de código como **true**.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir os recursos no **portal do Azure** em `https://portal.azure.com`. Inclua também a conta de armazenamento e o recurso de pesquisa, pois eles podem incorrer em um custo relativamente grande.
