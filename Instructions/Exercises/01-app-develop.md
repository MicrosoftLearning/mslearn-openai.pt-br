---
lab:
  title: Desenvolvimento de aplicativo com o Serviço OpenAI do Azure
  status: new
---

# Desenvolvimento de aplicativo com o Serviço OpenAI do Azure

Com o Serviço OpenAI do Azure, os desenvolvedores podem criar chatbots e outros aplicativos que se destacam na compreensão da linguagem humana natural ao utilizar APIs REST ou SDKs específicos de linguagem. Ao trabalhar com esses modelos de linguagem, a forma como os desenvolvedores moldam o prompt deles afeta muito como o modelo de IA generativa responderá. Os modelos OpenAI do Azure são capazes de adaptar e formatar conteúdo, se solicitado de maneira clara e concisa. Neste exercício, você aprenderá a conectar seu aplicativo ao OpenAI do Azure e verá como diferentes prompts de conteúdo semelhante ajudam a moldar a resposta do modelo de IA para atender melhor às suas necessidades.

No cenário desse exercício, você desempenhará o papel de um desenvolvedor de software trabalhando em uma campanha de marketing de vida selvagem. Você está explorando como usar IA generativa para melhorar emails publicitários e categorizar artigos que podem ser aplicados à sua equipe. As técnicas de engenharia rápida usadas no exercício podem ser aplicadas de forma semelhante para uma variedade de casos de uso.

Este exercício levará aproximadamente **30** minutos.

## Clone o repositório para este curso

Se você ainda não tiver feito isso, deverá clonar o repositório de código para este curso:

1. Inicie o Visual Studio Code.
2. Abra a paleta de comandos (SHIFT+CTRL+P ou **Exibir** > **Paleta de comandos...**) e execute um comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.
4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar ativos necessários para compilar e depurar, selecione **Agora não**.

## Provisionar um recurso de OpenAI do Azure

Se ainda não tiver um, provisione um recurso OpenAI do Azure na sua assinatura do Azure.

1. Entre no **portal do Azure** em `https://portal.azure.com`.

1. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: *Selecione uma assinatura do Azure que tenha sido aprovada para acesso ao serviço Azure OpenAI*
    - **Grupo de recursos**: *escolher ou criar um grupo de recursos*
    - **Região**: *faça uma escolha **aleatória** de uma das regiões a seguir*\*
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

1. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

Em seguida, você implantará um recurso de modelo do OpenAI do Azure na CLI. Consulte este exemplo e substitua as seguintes variáveis por seus próprios valores acima:

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_service> \
   --deployment-name gpt-4o \
   --model-name gpt-4o \
   --model-version 2024-05-13 \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

> **Observação**: a capacidade de SKU é medida em milhares de tokens por minuto. Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Configurar seu aplicativo

Foram fornecidos aplicativos para C# e Python, e ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes importantes do aplicativo para permitir o uso do recurso Azure OpenAI com chamadas de API assíncronas.

1. No Visual Studio Code, no painel do **Explorer**, navegue até a pasta **Labfiles/01-app-develop** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de linguagem. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
2. Clique com o botão direito na pasta **CSharp** ou **Python** que contém seus arquivos de código e abra um terminal integrado. Em seguida, instale o pacote do SDK do OpenAI do Azure executando o comando apropriado para sua preferência de idioma:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    ```

    **Python**:

    ```powershell
    pip install openai==1.65.2
    ```

3. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de configuração do seu idioma preferido

    - **C#**: appsettings.json
    - **Python**: .env

4. Atualize os valores da configuração para incluir:
    - O **ponto de extremidade** e uma **chave** do recurso Azure OpenAI que você criou (disponível na página **Chaves e Ponto de Extremidade** para seu recurso Azure OpenAI no portal do Azure)
    - O **nome da implantação** que você especificou para a implantação do modelo.
5. Salve o arquivo de configuração.

## Adicione código para usar o serviço Azure OpenAI

Agora você está pronto para usar o SDK do Azure OpenAI para consumir seu modelo implantado.

1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código para seu idioma preferido e substitua o comentário ***Adicionar pacote Azure OpenAI*** pelo código para adicionar a biblioteca Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**: application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. No arquivo de código, encontre o comentário ***Configurar o cliente Azure OpenAI*** e adicione código para configurar o cliente Azure OpenAI:

    **C#**: Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    ```

    **Python**: application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
    )
    ```

3. Na função que chama o modelo OpenAI do Azure, no comentário ***Receber resposta do OpenAI do Azure***, adicione o código para formatar e enviar a solicitação para o modelo.

    **C#**: Program.cs

    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(userMessage)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**: application.py

    ```python
    # Get response from Azure OpenAI
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
    - **Python**: `python application.py`

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

## Use o contexto de fundamento e mantenha o histórico de chats

1. Para a iteração final, estamos nos desviando da geração de email e explorando o *contexto de fundamento* e mantendo o histórico de chats. Aqui você fornece uma mensagem simples do sistema e altera o aplicativo para fornecer o contexto de fundamento como o início do histórico de chats. O aplicativo então anexará a entrada do usuário e extrairá informações do contexto de aterramento para responder ao prompt do usuário.
1. Abra o arquivo `grounding.txt` e leia brevemente o contexto de aterramento que você irá inserir.
1. Em seu aplicativo, imediatamente após o comentário***Inicializar lista de mensagens*** e antes de qualquer código existente, adicione o seguinte trecho de código para ler o texto de `grounding.txt` e inicializar o histórico de chats com o contexto de fundamento.

    **C#**: Program.cs

    ```csharp
    // Initialize messages list
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    var messagesList = new List<ChatMessage>()
    {
        new UserChatMessage(groundingText),
    };
    ```

    **Python**: application.py

    ```python
    # Initialize messages array
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    messages_array = [{"role": "user", "content": grounding_text}]
    ```

1. No comentário ***Format and send the request to the model***, substitua o código do comentário até o final do loop **while** pelo código a seguir. O código é basicamente o mesmo, mas agora está usando a matriz de mensagens para enviar a solicitação ao modelo.

    **C#**: Program.cs
   
    ```csharp
    // Format and send the request to the model
    messagesList.Add(new SystemChatMessage(systemMessage));
    messagesList.Add(new UserChatMessage(userMessage));
    GetResponseFromOpenAI(messagesList);
    ```

    **Python**: application.py

    ```python
    # Format and send the request to the model
    messages_array.append({"role": "system", "content": system_text})
    messages_array.append({"role": "user", "content": user_text})
    await call_openai_model(messages=messages_array, 
        model=azure_oai_deployment, 
        client=client
    )
    ```

1. No comentário ***Define the function that will get the response from Azure OpenAI endpoint***, substitua a declaração da função pelo código a seguir para usar a lista de histórico de chats ao chamar a função `GetResponseFromOpenAI` para C# ou `call_openai_model` Python.

    **C#**: Program.cs
   
    ```csharp
    // Define the function that gets the response from Azure OpenAI endpoint
    private static void GetResponseFromOpenAI(List<ChatMessage> messagesList)
    ```

    **Python**: application.py

    ```python
    # Define the function that will get the response from Azure OpenAI endpoint
    async def call_openai_model(messages, model, client):
    ```
    
1. Por fim, substitua todo o código em ***Get response from Azure OpenAI***. O código é basicamente o mesmo, mas agora está usando a matriz de mensagens para armazenar o histórico de conversas.

    **C#**: Program.cs
   
    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        messagesList,
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    messagesList.Add(new AssistantChatMessage(completion.Content[0].Text));
    ```

    **Python**: application.py

    ```python
    # Get response from Azure OpenAI
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )   

    print("Response:\n" + response.choices[0].message.content + "\n")
    messages.append({"role": "assistant", "content": response.choices[0].message.content})
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

   Observe que o modelo usa as informações de texto de fundamento para responder à sua pergunta.

1. Sem alterar a mensagem do sistema, insira o seguinte prompt para a mensagem do usuário:

    **Mensagem do usuário:**

    ```prompt
    How can they interact with it at Contoso?
    ```

    Observe que o modelo reconhece "eles" como as crianças e "ele" como o animal favorito delas, pois agora ele tem acesso à sua pergunta anterior no histórico de chats.
   
## Limpar

Quando terminar o recurso do OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no **portal do Azure** em `https://portal.azure.com`.
