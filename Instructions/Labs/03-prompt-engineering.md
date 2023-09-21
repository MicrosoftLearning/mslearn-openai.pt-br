---
lab:
  title: Utilizar engenharia de prompt em seu aplicativo
---

# Utilizar engenharia de prompt em seu aplicativo

Com o Serviço OpenAI do Azure, os desenvolvedores podem criar chatbots, modelos de linguagem e outros aplicativos que se destacam na compreensão da linguagem humana natural. O OpenAI do Azure fornece acesso a modelos de IA pré-treinados, bem como um conjunto de APIs e ferramentas para personalizar e ajustar esses modelos para atender aos requisitos específicos do seu aplicativo. Neste exercício, você aprenderá a implantar um modelo no OpenAI do Azure e usá-lo em seu aplicativo para resumir texto.

Ao trabalhar com o Serviço OpenAI do Azure, a forma como os desenvolvedores moldam o prompt deles afeta muito como o modelo de IA gerador responderá. Os modelos OpenAI do Azure são capazes de adaptar e formatar conteúdo, se solicitado de maneira clara e concisa. Neste exercício, você aprenderá como diferentes prompts de conteúdo semelhante ajudam a moldar a resposta do modelo de IA para atender melhor às suas necessidades.

Imagine que você está tentando enviar informações para um novo resgate de vida selvagem, e quer obter assistência de um modelo de IA generativo.

Este exercício levará aproximadamente **25** minutos.

## Antes de começar

Você precisará de uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.

- Para se inscrever em uma assinatura gratuita do Azure, visite [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acesso ao serviço OpenAI do Azure, visite [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionar um recurso de OpenAI do Azure

Antes de usar os modelos do OpenAI do Azure, você precisa provisionar um recurso do OpenAI do Azure em sua assinatura do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.
    - **Grupo de recursos**: crie um grupo de recursos com um nome de sua escolha.
    - **Região**: escolha qualquer região disponível.
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.
4. Navegue até a página **Chaves e Ponto de Extremidade** e salve-os em um arquivo de texto a ser usado posteriormente.

## Implantar um modelo

Para usar a API de OpenAI do Azure, primeiro você precisa implantar um modelo a ser usado por meio do **Azure OpenAI Studio**. Depois de implantado, referenciaremos esse modelo em nosso aplicativo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Explorar** para abrir o Azure OpenAI Studio em uma nova guia do navegador. Como alternativa, navegue diretamente até o [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
2. No Azure OpenAI Studio, crie uma implantação com as seguintes configurações:
    - **Modelo**: gpt-35-turbo
    - **Versão do modelo**: *usar a versão padrão*
    - **Nome da implantação**: text-turbo

> **Observação**: cada modelo do OpenAI do Azure é otimizado para um equilíbrio diferente de funcionalidades e desempenho. Usaremos a série de modelos **3.5 Turbo** na família de modelos **GPT-3** neste exercício, que é altamente capaz de entender a linguagem. Este exercício usa apenas um modelo, no entanto, a implantação e o uso de outros modelos implantados funcionarão da mesma maneira.

## Aplicar engenharia de prompt no playground de chat

Antes de usar seu aplicativo, examine como a engenharia de prompt aprimora a resposta do modelo no playground. Neste primeiro exemplo, imagine que você está tentando escrever um aplicativo Python de animais com nomes divertidos.

1. No [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), navegue até o playground **Chat** no painel esquerdo.
1. Na seção **Configuração do Assistente** na parte superior, insira `You are a helpful AI assistant` como a mensagem do sistema.
1. Na seção **Sessão de chat**, insira o prompt a seguir e pressione *Enter*.

    ```code
   1. Create a list of animals
   2. Create a list of whimsical names for those animals
   3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. O modelo provavelmente responderá com uma resposta para satisfazer o prompt, dividido em uma lista numerada. Esta é uma boa resposta, mas não o que estamos procurando.
1. Em seguida, atualize a mensagem do sistema para incluir instruções `You are an AI assistant helping write python code. Complete the app based on provided comments`. Clique em **Salvar alterações**
1. Formate as instruções como comentários do Python. Envie o prompt a seguir para o modelo.

    ```code
   # 1. Create a list of animals
   # 2. Create a list of whimsical names for those animals
   # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. O modelo deve responder corretamente com código Python completo, fazendo o que os comentários solicitaram.
1. Em seguida, veremos o impacto de uso de prompt com poucos disparos ao tentar classificar artigos. Retorne à mensagem do sistema, insira `You are a helpful AI assistant` novamente e salve as alterações. Isso criará uma sessão de chat.
1. Envie o prompt a seguir para o modelo.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. A resposta provavelmente será algumas informações sobre a seca na Califórnia. Embora não seja uma resposta ruim, não é a classificação que estamos procurando.
1. Na seção **Configuração do Assistente**, próximo à mensagem do sistema, selecione o botão **Adicionar um exemplo** . Adicione o exemplo a seguir.

    **Usuário:**

    ```code
   New York Baseballers Wins Big Against Chicago
   
   New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
   
   Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
   
   The Chicago Cyclones' two hits came in the 2nd and the 5th innings, but were unable to get the runner home to score.
    ```

    **Assistente:**

    ```code
   Sports
    ```

1. Adicione outro exemplo com o texto a seguir.

    **Usuário:**

    ```code
   Joyous moments at the Oscars

   The Oscars this past week where quite something!
   
   Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
   These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
   
   From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```

    **Assistente:**

    ```code
   Entertainment
    ```

1. Salve-os alterados para a configuração do assistente, e envie o mesmo prompt sobre a seca na Califórnia, fornecido aqui novamente para conveniência.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. Desta vez, o modelo deve responder com uma classificação apropriada, mesmo sem instruções.

## Configurar um aplicativo no Cloud Shell

Para mostrar como integrar com um modelo do OpenAI do Azure, usaremos um aplicativo de linha de comando curto que é executado no Cloud Shell no Azure. Abra uma nova guia do navegador para trabalhar com o Cloud Shell.

1. No [portal do Azure](https://portal.azure.com?azure-portal=true), selecione o botão **[>_]** (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Um painel do Cloud Shell é aberto na parte inferior do portal.

    ![Captura de tela da inicialização do Cloud Shell com um clique no ícone à direita da caixa de pesquisa superior.](../media/cloudshell-launch-portal.png#lightbox)

2. Na primeira vez que você abrir o Cloud Shell, talvez precise escolher o tipo de shell que deseja usar (*Bash* ou *PowerShell).* Selecione **Bash**. Se não vir essa opção, ignore a etapa.  

3. Se você precisar criar o armazenamento para o Cloud Shell, verifique se sua assinatura está especificada e selecione **Criar armazenamento**. Aguarde um minuto para a criação do armazenamento.

4. Verifique se o tipo de shell indicado na parte superior esquerda do painel do Cloud Shell está marcado como *Bash*. Se for o *PowerShell*, altere para *Bash* usando o menu suspenso.

5. Após o terminal iniciar, insira o comando a seguir para baixar o aplicativo de exemplo e salvá-lo em uma pasta chamada `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. Os arquivos são baixados para uma pasta chamada **azure-openai**. Navegue até os arquivos de laboratório deste exercício usando o comando a seguir.

    ```bash
   cd azure-openai/Labfiles/03-prompt-engineering
    ```

    Aplicativos para C# e Python foram fornecidos, bem como arquivos de texto que fornecem os prompts. Ambos os aplicativos apresentam a mesma funcionalidade.

    Abra o editor de código interno e você pode observar os arquivos de prompt que você usará no `prompts`. Use o comando a seguir para abrir os arquivos de laboratório no editor de código.

    ```bash
   code .
    ```

## Configurar seu aplicativo

Para este exercício, você concluirá algumas partes importantes do aplicativo para habilitar o uso do recurso OpenAI do Azure.

1. No editor de código, expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de linguagem de programação.

2. Abra o arquivo de configuração da linguagem de programação escolhida.

    - C#: `appsettings.json`
    - Python: `.env`
    
3. Atualize os valores de configuração para incluir o **ponto de extremidade** e a **chave** do recurso OpenAI do Azure que você criou, bem como o nome do modelo que você implantou, `text-turbo`. Salve o arquivo.

4. Navegue até a pasta da linguagem de programação de sua preferência e instale os pacotes necessários.

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.5
    ```

    **Python**

    ```bash
   cd Python
   pip install python-dotenv
   pip install openai
    ```

5. Navegue até sua pasta da linguagem de programação preferida, selecione o arquivo de código e adicione as bibliotecas necessárias.

    **C#**

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    ```python
   # Add OpenAI import
   import openai
    ```

5. Abra o código do aplicativo para sua linguagem de programação e adicione o código necessário para configurar o cliente.

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key
    ```

6. Na função que chama o modelo OpenAI do Azure, adicione o código para formatar e enviar a solicitação para o modelo.

    **C#**

    ```csharp
   // Create chat completion options
   var chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, systemPrompt),
          new ChatMessage(ChatRole.User, userPrompt)
       },
       Temperature = 0.7f,
       MaxTokens = 800,
   };

   // Get response from Azure OpenAI
   Response<ChatCompletions> response = await client.GetChatCompletionsAsync(
       oaiModelName,
       chatCompletionsOptions
   );

   ChatCompletions completions = response.Value;
   string completion = completions.Choices[0].Message.Content;
    ```

    **Python**

    ```python
   # Build the messages array
   messages =[
       {"role": "system", "content": system_message},
       {"role": "user", "content": user_message},
   ]

   # Call the Azure OpenAI model
   response = openai.ChatCompletion.create(
       engine=model,
       messages=messages,
       temperature=0.7,
       max_tokens=800
   )
    ```

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta. Você observará que a única diferença entre as várias opções é o conteúdo do prompt, todos os outros parâmetros (como contagem de tokens e temperatura) permanecem os mesmos para cada solicitação.

Cada prompt é exibido no console conforme ele envia para você ver como as diferenças nos prompts produzem respostas diferentes.

1. No terminal bash Cloud Shell, navegue até a pasta da linguagem de programação preferida.
1. Execute o aplicativo e expanda o terminal para ocupar a maior parte da janela do navegador.

    - **C#** : `dotnet run`
    - **Python**: `python prompt-engineering.py`

1. Escolha a opção **1** para o prompt mais básico.
1. Observe a entrada do prompt e a saída gerada. O modelo de IA provavelmente produzirá uma boa introdução genérica a um resgate de animais selvagens.
1. Em seguida, escolha a opção **2** para fornecer um prompt solicitando um email de introdução, juntamente com alguns detalhes sobre o resgate da vida selvagem.
1. Observe a entrada do prompt e a saída gerada. Desta vez, você provavelmente verá o formato de um email com os animais específicos incluídos, bem como a chamada para doações.
1. Em seguida, escolha a opção **3** para solicitar um email semelhante ao acima, mas com uma tabela formatada com animais adicionais incluídos.
1. Observe a entrada do prompt e a saída gerada. Desta vez, você provavelmente verá um email semelhante com texto formatado de uma maneira específica (nesse caso, uma tabela perto do final) demonstrando como os modelos de IA generativos podem formatar a saída quando solicitados.
1. Em seguida, escolha a opção **4** para solicitar um email semelhante, mas desta vez especificando um tom diferente na mensagem do sistema.
1. Observe a entrada do prompt e a saída gerada. Desta vez, você provavelmente verá o email em um formato semelhante, mas com um tom muito mais informal. Você provavelmente até verá piadas incluídas.

Aumentar a temperatura geralmente faz com que a resposta varie, mesmo quando fornecido o mesmo prompt, devido ao aumento da aleatoriedade. Você pode executá-lo várias vezes com diferentes valores de temperatura ou top_p para ver como isso afeta a resposta para o mesmo prompt.

Se você quiser ver a resposta completa do OpenAI do Azure, poderá definir a variável `printFullResponse` como `True` e executar novamente o aplicativo.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true).
