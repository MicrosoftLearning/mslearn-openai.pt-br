---
lab:
  title: Usar SDKs do Azure OpenAI em seu aplicativo
---

# Usar APIs do Azure OpenAI em seu aplicativo

Com o Serviço OpenAI do Azure, os desenvolvedores podem criar chatbots, modelos de linguagem e outros aplicativos que se destacam na compreensão da linguagem humana natural. O OpenAI do Azure fornece acesso a modelos de IA pré-treinados, bem como um conjunto de APIs e ferramentas para personalizar e ajustar esses modelos para atender aos requisitos específicos do seu aplicativo. Neste exercício, você aprenderá a implantar um modelo no OpenAI do Azure e usá-lo em seu aplicativo para resumir texto.

No cenário deste exercício, você executará a função de um desenvolvedor de software que foi encarregado de implementar um aplicativo que pode usar a IA generativa para ajudar a fornecer recomendações de caminhada. As técnicas usadas no exercício podem ser aplicadas a qualquer aplicativo que queira usar APIs do OpenAI do Azure.

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

O OpenAI do Azure fornece um portal baseado na Web chamado **Azure OpenAI Studio**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará sua exploração do OpenAI do Azure usando o Azure OpenAI Studio para implantar um modelo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Vá para o OpenAI do Azure**  para abrir o Est do OpenAI do Azure em uma nova guia do navegador.
2. No Azure OpenAI Studio, na página **Implantações**, exiba suas implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Modelo**: gpt-35-turbo-16k *(se o modelo 16k não estiver disponível, escolha gpt-35-turbo)*
    - **Versão do Modelo**: atualização automática para padrão
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Habilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Preparar-se para desenvolver um aplicativo no Visual Studio Code

Você desenvolverá seu aplicativo do OpenAI do Azure usando o Visual Studio Code. Os arquivos de código do seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: Se você já clonou o repositório **mslearn-openai**, abra-o no código do Visual Studio. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
2. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

## Configurar seu aplicativo

Os aplicativos para C# e Python foram fornecidos. Ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes-chave do aplicativo para habilitar o uso do recurso do OpenAI do Azure.

1. No Visual Studio Code, no painel do **Explorer**, navegue até a pasta **Labfiles/02-code-generation** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de idioma. Cada pasta contém os arquivos específicos do idioma de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
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
5. Salve o arquivo de configuração.

## Adicione código para usar o serviço Azure OpenAI

Agora você está pronto para usar o SDK do Azure OpenAI para consumir seu modelo implantado.

1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código para seu idioma preferido e substitua o comentário ***Adicionar pacote Azure OpenAI*** pelo código para adicionar a biblioteca Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```
    
    **Python**: test-openai-model.py
    
    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. No código do aplicativo para seu idioma, substitua o comentário ***Inicializar o cliente do Azure OpenAI...*** pelo código a seguir para inicializar o cliente e definir nossa mensagem do sistema.

    **C#**: Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. Substitua o comentário ***Adicionar código para enviar solicitação...*** pelo código necessário para compilar a solicitação; especificando os vários parâmetros para seu modelo, como `messages` e `temperature`.

    **C#**: Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(inputText),
        },
        MaxTokens = 400,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Print the response
    string completion = response.Choices[0].Message.Content;
    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. Salve as alterações no arquivo de código.

## Teste seu aplicativo

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta.

1. No painel do terminal interativo, verifique se o contexto da pasta é a pasta da sua linguagem de preferência. Em seguida, insira o comando a seguir para executar o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python test-openai-model.py`

    > **Dica**: você pode usar o ícone **Maximizar tamanho do painel** (**^**) na barra de ferramentas do terminal para ver mais do texto do console.

1. Quando solicitado, insira o texto `What hike should I do near Rainier?`.
1. Observe a saída, observando que a resposta segue as diretrizes fornecidas na mensagem do sistema que você adicionou à matriz de *mensagens*.
1. Forneça o prompt `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` e observe a saída.
1. No arquivo de código do idioma preferido, altere o valor do *parâmetro de temperatura* em sua solicitação para **1,0** e salve o arquivo.
1. Execute o aplicativo novamente usando os prompts acima e observe a saída.

Aumentar a temperatura geralmente faz com que a resposta varie, mesmo quando fornecido o mesmo prompt, devido ao aumento da aleatoriedade. Você pode executá-lo várias vezes para ver como a saída pode ser alterada. Tente usar valores diferentes para sua temperatura com a mesma entrada.

## Manter o histórico de conversas

Na maioria dos aplicativos do mundo real, a capacidade de referenciar partes anteriores da conversa permite uma interação mais realista com um agente de IA. A API OpenAI do Azure é sem estado por design, mas ao fornecer um histórico da conversa em seu prompt, você permite que o modelo de IA faça referência a mensagens passadas.

1. Execute o aplicativo novamente e forneça o prompt `Where is a good hike near Boise?`.
1. Observe a saída e, em seguida, solicite `How difficult is the second hike you suggested?`.
1. A resposta do modelo provavelmente indicará que não é possível entender o aumento ao qual você está se referindo. Para corrigir isso, podemos permitir que o modelo tenha as mensagens de conversa anteriores para referência.
1. Em seu aplicativo, precisamos adicionar o prompt e a resposta anteriores ao prompt futuro que estamos enviando. Abaixo da definição da **mensagem** do sistema, adicione o código a seguir.

    **C#**: Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatRequestMessage>()
    {
        new ChatRequestSystemMessage(systemMessage),
    };
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. Sob o comentário ***Adicionar código para enviar solicitação...***, substitua todo o código do comentário até o final do loop **while** pelo código a seguir e salve o arquivo. O código é basicamente o mesmo, mas agora está usando a matriz de mensagens para armazenar o histórico de conversas.

    **C#**: Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new ChatRequestUserMessage(inputText));

    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        MaxTokens = 1200,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Add messages to the completion options
    foreach (ChatRequestMessage chatMessage in messagesList)
    {
        chatCompletionsOptions.Messages.Add(chatMessage);
    }

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Return the response
    string completion = response.Choices[0].Message.Content;

    // Add generated text to messages list
    messagesList.Add(new ChatRequestAssistantMessage(completion));

    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. Salve o arquivo. No código que você adicionou, observe que agora acrescentamos a entrada e a resposta anteriores à matriz de prompts que permite que o modelo entenda o histórico de nossa conversa.
1. No painel do terminal, insira o comando a seguir para executar o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Execute o aplicativo novamente e forneça o prompt `Where is a good hike near Boise?`.
1. Observe a saída e, em seguida, solicite `How difficult is the second hike you suggested?`.
1. Você provavelmente receberá uma resposta sobre a segunda caminhada sugerida pelo modelo, o que fornece uma conversa muito mais realista. Você pode fazer perguntas adicionais de acompanhamento que fazem referência às respostas anteriores e sempre que o histórico fornece contexto para o modelo responder.

    > **Dica**: a contagem de tokens é definida apenas como 1200, portanto, se a conversa continuar por muito tempo, o aplicativo ficará sem tokens disponíveis, resultando em um prompt incompleto. Em usos de produção, limitar o comprimento do histórico às entradas e respostas mais recentes ajudará a controlar o número de tokens necessários.

## Limpar

Quando terminar o recurso do OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no **portal do Azure** em `https://portal.azure.com`.
