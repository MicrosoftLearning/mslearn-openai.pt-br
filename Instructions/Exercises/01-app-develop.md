---
lab:
  title: Desenvolvimento de aplicativo com o Serviço OpenAI do Azure
---

# Desenvolvimento de aplicativo com o Serviço OpenAI do Azure

Com o Serviço OpenAI do Azure, os desenvolvedores podem criar chatbots e outros aplicativos que se destacam na compreensão da linguagem humana natural ao utilizar APIs REST ou SDKs específicos de linguagem. Ao trabalhar com esses modelos de linguagem, a forma como os desenvolvedores moldam o prompt deles afeta muito como o modelo de IA generativa responderá. Os modelos OpenAI do Azure são capazes de adaptar e formatar conteúdo, se solicitado de maneira clara e concisa. Neste exercício, você aprenderá a conectar seu aplicativo ao OpenAI do Azure e verá como diferentes prompts de conteúdo semelhante ajudam a moldar a resposta do modelo de IA para atender melhor às suas necessidades.

No cenário desse exercício, você desempenhará o papel de um desenvolvedor de software trabalhando em uma campanha de marketing de vida selvagem. Você está explorando como usar IA generativa para melhorar emails publicitários e categorizar artigos que podem ser aplicados à sua equipe. As técnicas de engenharia rápida usadas no exercício podem ser aplicadas de forma semelhante para uma variedade de casos de uso.

Este exercício levará aproximadamente **30** minutos.

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

3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

Em seguida, você implantará um recurso de modelo do OpenAI do Azure na CLI. Consulte este exemplo e substitua as seguintes variáveis por seus próprios valores acima:

```dotnetcli
az cognitiveservices account deployment create \
   -g *Your resource group* \
   -n *Name of your OpenAI service* \
   --deployment-name gpt-35-turbo \
   --model-name gpt-35-turbo \
   --model-version 0125  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 5,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.

> [!NOTE]
> Se você vir um aviso sobre a estrutura net7.0 não ter suporte, poderá desconsiderá-lo para este exercício.

## Configurar seu aplicativo

Foram fornecidos aplicativos para C# e Python, e ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes importantes do aplicativo para permitir o uso do recurso Azure OpenAI com chamadas de API assíncronas.

1. No Visual Studio Code, no painel do **Explorer**, navegue até a pasta **Labfiles/01-app-develop** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de linguagem. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
2. Clique com o botão direito na pasta **CSharp** ou **Python** que contém seus arquivos de código e abra um terminal integrado. Em seguida, instale o pacote do SDK do OpenAI do Azure executando o comando apropriado para sua preferência de idioma:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 2.0.0
    ```

    **Python**:

    ```
    pip install openai==1.54.3
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
        ChatCompletion completion = chatClient.CompleteChat(
        [
        new SystemChatMessage(systemMessage),
        new UserChatMessage(userMessage),
        ]);
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

    **Python**: application.py

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
