---
lab:
  title: Utilizar engenharia de prompt em seu aplicativo
---

# Utilizar engenharia de prompt em seu aplicativo

Ao trabalhar com o Serviço OpenAI do Azure, a forma como os desenvolvedores moldam o prompt deles afeta muito como o modelo de IA gerador responderá. Os modelos OpenAI do Azure são capazes de adaptar e formatar conteúdo, se solicitado de maneira clara e concisa. Neste exercício, você aprenderá como diferentes prompts de conteúdo semelhante ajudam a moldar a resposta do modelo de IA para atender melhor às suas necessidades.

No cenário desse exercício, você desempenhará o papel de um desenvolvedor de software trabalhando em uma campanha de marketing de vida selvagem. Você está explorando como usar IA generativa para melhorar emails publicitários e categorizar artigos que podem ser aplicados à sua equipe. As técnicas de engenharia rápida usadas no exercício podem ser aplicadas de forma semelhante para uma variedade de casos de uso.

Este exercício levará aproximadamente **30** minutos.

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

O Azure fornece um portal baseado na Web chamado **Estúdio de IA do Azure**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará sua exploração do OpenAI do Azure usando o Estúdio de IA do Azure para implantar um modelo.

> **Observação**: À medida que você usa o Estúdio de IA do Azure, podem ser exibidas caixas de mensagens sugerindo tarefas para você executar. Você pode fechá-los e seguir as etapas desse exercício.

1. No portal do Azure, na página **Visão geral** do recurso OpenAI do Azure, role para baixo até a seção **Introdução** e selecione o botão para acessar o **AI Studio**.
1. No Estúdio de IA do Azure, no painel à esquerda, selecione a página **Implantações** e visualize as implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Modelo**: gpt-35-turbo-16k *(se o modelo 16k não estiver disponível, escolha gpt-35-turbo)*
    - **Versão do modelo**: *usar a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Desabilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Explorar técnicas de engenharia de prompt

Vamos começar explorando algumas técnicas de engenharia imediata no playground do Chat.

1. Na seção **Playground**, selecione a página **Chat**. A página do playground do **Chat** consiste em uma série de botões e dois painéis principais (que podem ser organizados da direita para a esquerda na horizontal ou de cima para baixo na vertical, dependendo da resolução da tela):
    - **Configuração** - usada para selecionar sua implantação, definir a mensagem do sistema e definir parâmetros para interagir com sua implantação.
    - **Sessão de chat** - usada para enviar mensagens de bate-papo e exibir respostas.
2. Em **Implantações**, certifique-se de que a implantação do modelo gpt-35-turbo-16k esteja selecionada.
1. Revise a **Mensagem do sistema** padrão, que deveria ser *Você é um assistente de IA que ajuda as pessoas a encontrar informações.*
4. Na **sessão de chat**, envie a seguinte consulta:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    A resposta fornece uma descrição do artigo. No entanto, suponha que você queira um formato mais específico para categorização de artigos.

5. Na seção **Configuração** altere a mensagem do sistema para `You are a news aggregator that categorizes news articles.`

6. Na nova mensagem do sistema, selecione o botão **Adicionar seção** e escolha **Exemplos**. Em seguida, adicione o seguinte exemplo.

    **Usuário**:
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **Assistente:**
    
    ```prompt
    Sports
      ```

7. Adicione outro exemplo com o texto a seguir.

    **Usuário:**
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **Assistente:**
    
    ```prompt
    Entertainment
    ```

8. Use o botão ** Aplicar alterações** na parte superior da seção **Configuração** para salvar suas alterações.

9. Na seção **Sessão de chat**, reenvie o seguinte prompt:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    A combinação de uma mensagem de sistema mais específica e alguns exemplos de consultas e respostas esperadas resulta em um formato consistente para os resultados.

10. Altere a mensagem do sistema de volta para o modelo padrão, que deve ser `You are an AI assistant that helps people find information.` sem exemplos. Em seguida, aplique as alterações.

11. Na seção **Sessão de chat**, envie o seguinte prompt:

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    O modelo provavelmente responderá com uma resposta para satisfazer o prompt, dividido em uma lista numerada. Essa é uma resposta apropriada, mas suponha que o que você realmente queria era que o modelo escrevesse um programa Python que executasse as tarefas que você descreveu?

12. Altere a mensagem do sistema para `You are a coding assistant helping write python code.` e aplique as alterações.
13. Reenvie o seguinte prompt ao modelo:

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    O modelo deve responder corretamente com o código python fazendo o que os comentários solicitaram.

## Preparar-se para desenvolver um aplicativo no Visual Studio Code

Agora vamos explorar o uso da engenharia imediata em um aplicativo que usa o SDK do serviço Azure OpenAI. Você desenvolverá seu aplicativo usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: Se você já clonou o repositório **mslearn-openai**, abra-o no código do Visual Studio. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
2. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

## Configurar seu aplicativo

Foram fornecidos aplicativos para C# e Python, e ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes importantes do aplicativo para permitir o uso do recurso Azure OpenAI com chamadas de API assíncronas.

1. No Visual Studio Code, no painel **Explorer**, navegue até a pasta **Labfiles/03-prompt-engineering** e expanda a pasta **CSharp** ou **Python** dependendo de sua preferência de idioma. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
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
    - O **nome de implantação** que você especificou para a implantação do modelo (disponível na página **Implantações** no Estúdio de IA do Azure).
5. Salve o arquivo de configuração.

## Adicione código para usar o serviço Azure OpenAI

Agora você está pronto para usar o SDK do Azure OpenAI para consumir seu modelo implantado.

1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código para seu idioma preferido e substitua o comentário ***Adicionar pacote Azure OpenAI*** pelo código para adicionar a biblioteca Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. No arquivo de código, encontre o comentário ***Configurar o cliente Azure OpenAI*** e adicione código para configurar o cliente Azure OpenAI:

    **C#**: Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**: prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. Na função que chama o modelo Azure OpenAI, no comentário ***Formate e envie a solicitação para o modelo***, adicione o código para formatar e envie a solicitação para o modelo.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. Salve as alterações no arquivo de código.

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta. Você observará que a única diferença entre as várias opções é o conteúdo do prompt, todos os outros parâmetros (como contagem de tokens e temperatura) permanecem os mesmos para cada solicitação.

1. Na pasta do seu idioma preferido, abra `system.txt` no Visual Studio Code. Para cada uma das interações, você irá inserir a **Mensagem do sistema** nesse arquivo e salvá-la. Cada iteração fará uma pausa primeiro para você alterar a mensagem do sistema.
1. No painel do terminal interativo, verifique se o contexto da pasta é a pasta da sua linguagem de preferência. Em seguida, insira o comando a seguir para executar o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python prompt-engineering.py`

    > **Dica**: você pode usar o ícone **Maximizar tamanho do painel** (**^**) na barra de ferramentas do terminal para ver mais do texto do console.

1. Para a primeira iteração, insira os seguintes prompts:

    **Mensagem do sistema**

    ```prompt
    You are an AI assistant
    ```

    **Mensagem do usuário:**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. Observe a saída. O modelo de IA provavelmente produzirá uma boa introdução genérica a um resgate de animais selvagens.
1. Em seguida, insira os seguintes prompts que especificam um formato para a resposta:

    **Mensagem do sistema**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Mensagem do usuário:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

    > **Dica**: Você pode descobrir que a digitação automática na VM não funciona bem com prompts de várias linhas. Se esse for o seu problema, copie todo o prompt e cole-o no Visual Studio Code.

1. Observe a saída. Desta vez, você provavelmente verá o formato de um email com os animais específicos incluídos, bem como a chamada para doações.
1. Em seguida, insira os seguintes prompts que especificam adicionalmente o conteúdo:

    **Mensagem do sistema**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Mensagem do usuário:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observe o resultado e veja como o email mudou com base em suas instruções claras.
1. Em seguida, insira os seguintes prompts onde adicionamos detalhes sobre o tom à mensagem do sistema:

    **Mensagem do sistema**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **Mensagem do usuário:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observe a saída. Desta vez, você provavelmente verá o email em um formato semelhante, mas com um tom muito mais informal. Você provavelmente até verá piadas incluídas.
1. Para a iteração final, estamos nos desviando da geração de email e explorando o *contexto de base*. Aqui você fornece uma mensagem simples do sistema e altera o aplicativo para fornecer o contexto de aterramento como o início do prompt do usuário. O aplicativo então anexará a entrada do usuário e extrairá informações do contexto de aterramento para responder ao prompt do usuário.
1. Abra o arquivo `grounding.txt` e leia brevemente o contexto de aterramento que você irá inserir.
1. Em seu aplicativo, imediatamente após o comentário ***Formate e envie a solicitação para o modelo*** e antes de qualquer código existente, adicione o seguinte snippet de código para ler o texto de `grounding.txt` para aumentar o prompt do usuário com o contexto de aterramento.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. Salve o arquivo e execute novamente seu aplicativo.
1. Digite os seguintes prompts (com a **mensagem do sistema** ainda sendo inserida e salva em `system.txt`).

    **Mensagem do sistema**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **Mensagem do usuário:**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **Dica**: Se quiser ver a resposta completa do Azure OpenAI, você pode definir a variável **printFullResponse** como `True` e executar novamente o aplicativo.

## Limpar

Quando terminar o recurso do OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no **portal do Azure** em `https://portal.azure.com`.
