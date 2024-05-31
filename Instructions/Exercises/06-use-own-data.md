---
lab:
  title: Implementar a RAG (Geração Aumentada de Recuperação) com o Serviço Azure OpenAI
---

# Implementar a RAG (Geração Aumentada de Recuperação) com o Serviço Azure OpenAI

O Serviço OpenAI do Azure permite que você use dados próprios com a inteligência do LLM subjacente. Você pode limitar o modelo para usar apenas seus dados para tópicos pertinentes ou misturá-los com os resultados do modelo pré-treinado.

No cenário deste exercício, você executará a função de um desenvolvedor de software que trabalha para a Margie's Travel Agency. Você explorará como usar a IA gerativa para tornar as tarefas de codificação mais fáceis e eficientes. As técnicas usadas no exercício podem ser aplicadas a outros arquivos de código, linguagens de programação e casos de uso.

Este exercício levará aproximadamente **20** minutos.

## Provisionar um recurso de OpenAI do Azure

Se ainda não tiver um, provisione um recurso OpenAI do Azure na sua assinatura do Azure.

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

3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

O OpenAI do Azure fornece um portal baseado na Web chamado **Azure OpenAI Studio**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará sua exploração do OpenAI do Azure usando o Azure OpenAI Studio para implantar um modelo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Vá para o OpenAI do Azure**  para abrir o Est do OpenAI do Azure em uma nova guia do navegador.
2. No Azure OpenAI Studio, na página **Implantações**, exiba suas implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Modelo**: gpt-35-turbo-16k *(deve ser este modelo para usar os recursos neste exercício)*
    - **Versão do Modelo**: atualização automática para padrão
    - **Nome da implantação**: *Um nome exclusivo de sua escolha. Você usará esse nome posteriormente no laboratório.*
    - **Opções avançadas**
        - **Filtro de conteúdo**: Padrão
        - **Tipo de implantação**: Padrão
        - **Limite de taxa de tokens por minuto**: 5K\*
        - **Habilitar cota dinâmica**: Habilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Observe o comportamento normal do chat sem adicionar seus dados

Antes de conectar o OpenAI do Azure aos seus dados, primeiro observe como o modelo base responde a consultas sem nenhum dado para fundamentação.

1. No **Estúdio do OpenAI  do Azure** em `https://oai.azure.com`, na seção **Playground**, selecione a página **Chat**. A página do playground **Chat** consiste em três seções principais:
    - **Configuração** - usada para definir o contexto para as respostas do modelo.
    - **Sessão de chat** - usada para enviar mensagens de bate-papo e exibir respostas.
    - **Configuração** - usada para definir configurações para a implantação do modelo.
2. Na seção **Configuração**, certifique-se de que a implantação do seu modelo esteja selecionada.
3. Na área **Configuração**, selecione o modelo de mensagem do sistema padrão para definir o contexto da sessão de chat. A mensagem padrão do sistema é *Você é um assistente de IA que ajuda as pessoas a encontrar informações*.
4. Na **sessão de Chat**, envie as seguintes consultas e examine as respostas:

    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    Tente fazer perguntas semelhantes sobre turismo e lugares para ficar para outros locais que serão incluídos em nossos dados de fundamentação, como Londres ou São Francisco. Você provavelmente receberá respostas completas sobre áreas ou bairros, e alguns fatos gerais sobre a cidade.

## Conectar seus dados no playground de chat

Agora você adicionará alguns dados para uma empresa fictícia de agente de viagens chamada *Margie's Travel*. Em seguida, você verá como o modelo do Azure OpenAI responde ao usar os folhetos do Margie's Travel como dados de aterramento.

1. Em uma nova guia do navegador, baixe um arquivo de dados de folheto de `https://aka.ms/own-data-brochures`. Extraia os folhetos para uma pasta no computador.
1. No Estúdio de OpenAI do Azure, no playground de **Chat**,  na seção **Configuração**, selecione **Adicionar seus dados**.
1. Selecione **Adicionar uma fonte de dados** e escolha **Carregar arquivos**.
1. Você precisará criar uma conta de armazenamento e um recurso de pesquisa da IA do Azure. Na lista suspensa do recurso de armazenamento, selecione **Criar um recurso de Armazenamento de Blobs do Azure** e crie uma conta de armazenamento com as configurações a seguir. Deixe qualquer coisa não especificada com o valor padrão.

    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Selecione o mesmo grupo de recursos que o do recurso do  OpenAI do Azure*
    - **Nome da conta de armazenamento**: *insira um nome exclusivo*.
    - **Região**: *Selecione a mesma região que o recurso do  OpenAI do Azure*
    - **Redundância**: LRS (armazenamento com redundância local)

1. Enquanto o recurso da conta de armazenamento estiver sendo criado, retorne ao Azure OpenAI Studio e selecione **Criar um recurso da Pesquisa de IA do Azure** com as seguintes configurações. Deixe qualquer coisa não especificada com o valor padrão.

    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Selecione o mesmo grupo de recursos que o do recurso do  OpenAI do Azure*
    - **Nome do serviço**: *insira um nome exclusivo*
    - **Localização**: *Selecione o mesmo local que o do recurso do OpenAI do Azure*
    - **Tipo de preço**: Básico

1. Aguarde até que o recurso de pesquisa tenha sido implantado e, em seguida, volte para o Estúdio de IA do Azure e atualize a página.
1. Em **Adicionar dados**, insira os seguintes valores para a fonte de dados e selecione **Avançar**.

    - **Selecionar fonte de dados**: carregar arquivos
    - **Assinatura:** sua assinatura do Azure
    - **Selecione o recurso Armazenamento de Blobs do Azure Blob**. *Use o botão **Atualizar** para preencher novamente a lista e escolha o recurso de armazenamento que você criou*
        - Ativar o CORS quando solicitado
    - **Selecione recurso da Pesquisa de IA do Azure** *Use o botão **Atualizar** para preencher novamente a lista e escolha o recurso de pesquisa que você criou*
    - **Insira o nomedo índice**: `margiestravel`
    - **Adicionar busca em vetores a este recurso de pesquisa**: desmarcado
    - **Reconheço que a conexão a uma conta de Pesquisa de IA do Azure incorrerá no uso da minha conta**: verificado

1. Na página **Upload de arquivos**, carregue os PDFs que você baixou e selecione **Avançar**.
1. Na página **Gerenciamento de dados**, selecione o tipo de pesquisa **Palavra-chave** no menu suspenso e, em seguida, **Avançar**.
1. Na página **Revisar e concluir** selecione **Salvar e fechar**, que adicionará seus dados. Isso pode levar alguns minutos, durante os quais você precisa deixar a janela aberta. Depois de concluído, você verá a fonte de dados, o recurso de pesquisa e o índice especificados na seção **Configuração**.

    > **Dica**: Ocasionalmente, a conexão entre o novo índice de pesquisa e o Estúdio de OpenAI do Azure leva muito tempo. Se você esperou alguns minutos e ainda não se conectou, verifique os recursos da Pesquisa de IA no portal do Azure. Se você vir o índice concluído, poderá desconectar a conexão de dados no Estúdio da OpenAI do Azure e adicioná-la novamente especificando uma fonte de dados da Pesquisa de IA do Azure e selecionando seu novo índice.

## Conversar com um modelo fundamentado em seus dados

Agora que você adicionou seus dados, faça as mesmas perguntas que fez anteriormente e veja como a resposta difere.

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
```

Você observará uma resposta muito diferente desta vez, com detalhes sobre determinados hotéis e uma menção da Margie's Travel, bem como referências à origem das informações fornecidas. Se você abrir a referência de PDF listada na resposta, verá os mesmos hotéis que o modelo fornecido.

Tente perguntar sobre outras cidades incluídas nos dados de fundamentação, que são Dubai, Las Vegas, Londres e São Francisco.

> **Observação**: **Adicionar seus dados** ainda está em versão prévia e pode nem sempre se comportar conforme o esperado para esse recurso, por exemplo, pode fornecer a referência incorreta para uma cidade não incluída nos dados de fundamentação.

## Conectar seu aplicativo aos seus próprios dados

Em seguida, vamos explorar como conectar seu aplicativo para usar seus próprios dados.

### Preparar-se para desenvolver um aplicativo no Visual Studio Code

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
    pip install openai==1.13.3
    ```

3. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de configuração do seu idioma preferido

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Atualize os valores da configuração para incluir:
    - O **ponto de extremidade** e uma **chave** do recurso Azure OpenAI que você criou (disponível na página **Chaves e Ponto de Extremidade** para seu recurso Azure OpenAI no portal do Azure)
    - O **nome de implantação** que você especificou para a implantação do modelo (disponível na página **Implantações** no Azure OpenAI Studio).
    - O ponto de extremidade do seu serviço de pesquisa (o valor de **Url** na página de visão geral do seu recurso de pesquisa no portal do Azure).
    - Uma **chave** para seu recurso de pesquisa (disponível na página **Chaves** para seu recurso de pesquisa no portal do Azure - você pode usar qualquer uma das chaves de administrador)
    - O nome do índice de pesquisa (que deve ser `margiestravel`).
1. Salve o arquivo de configuração.

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

Quando terminar de usar o recurso do OpenAI do Azure, lembre-se de excluir o recurso no **portal do Azure** em `https://portal.azure.com`. Inclua também a conta de armazenamento e o recurso de pesquisa, pois eles podem incorrer em um custo relativamente grande.
