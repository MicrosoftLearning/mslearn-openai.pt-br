---
lab:
  title: Integrar o OpenAI do Azure ao seu aplicativo
---

# Integrar o OpenAI do Azure ao seu aplicativo

Com o Serviço OpenAI do Azure, os desenvolvedores podem criar chatbots, modelos de linguagem e outros aplicativos que se destacam na compreensão da linguagem humana natural. O OpenAI do Azure fornece acesso a modelos de IA pré-treinados, bem como um conjunto de APIs e ferramentas para personalizar e ajustar esses modelos para atender aos requisitos específicos do seu aplicativo. Neste exercício, você aprenderá a implantar um modelo no OpenAI do Azure e usá-lo em seu aplicativo para resumir texto.

Este exercício levará aproximadamente **30** minutos.

## Antes de começar

Você precisará de uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.

- Para se inscrever em uma assinatura gratuita do Azure, visite [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acesso ao serviço OpenAI do Azure, visite [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionar um recurso de OpenAI do Azure

Antes de usar os modelos do OpenAI do Azure, você precisa provisionar um recurso do OpenAI do Azure em sua assinatura do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.
    - **Grupo de recursos**: escolha um grupo de recursos existente ou crie um com um nome de sua escolha.
    - **Região**: escolha qualquer região disponível.
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.
4. Navegue até a página **Chaves e Ponto de Extremidade** e salve-os em um arquivo de texto a ser usado posteriormente.

## Implantar um modelo

Para usar a API de OpenAI do Azure, primeiro você precisa implantar um modelo a ser usado por meio do **Azure OpenAI Studio**. Depois de implantado, referenciaremos esse modelo em nosso aplicativo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Explorar** para abrir o Azure OpenAI Studio em uma nova guia do navegador.
2. No Azure OpenAI Studio, crie uma implantação com as seguintes configurações:
    - **Modelo**: gpt-35-turbo
    - **Versão do modelo**: *usar a versão padrão*
    - **Nome da implantação**: text-turbo

> **Observação**: cada modelo do OpenAI do Azure é otimizado para um equilíbrio diferente de funcionalidades e desempenho. Usaremos a série de modelos **3.5 Turbo** na família de modelos **GPT-3** neste exercício, que é altamente capaz de entender a linguagem. Este exercício usa apenas um modelo, no entanto, a implantação e o uso de outros modelos implantados funcionarão da mesma maneira.

## Configurar um aplicativo no Cloud Shell

Para mostrar como integrar com um modelo do OpenAI do Azure, usaremos um aplicativo de linha de comando curto que é executado no Cloud Shell no Azure. Abra uma nova guia do navegador para trabalhar com o Cloud Shell.

1. No [portal do Azure](https://portal.azure.com?azure-portal=true), selecione o botão **[>_]** (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Um painel do Cloud Shell é aberto na parte inferior do portal.

    ![Captura de tela da inicialização do Cloud Shell com um clique no ícone à direita da caixa de pesquisa superior.](../media/cloudshell-launch-portal.png#lightbox)

2. Na primeira vez que você abrir o Cloud Shell, talvez precise escolher o tipo de shell que deseja usar (*Bash* ou *PowerShell).* Selecione **Bash**. Se não vir essa opção, ignore a etapa.  

3. Se for solicitado que você crie um armazenamento para o Cloud Shell, selecione **Mostrar configurações avançadas** e selecione as seguintes configurações:
    - **Assinatura**: sua assinatura
    - **Regiões do Cloud Shell**: escolha qualquer região disponível
    - **Mostrar configurações de isolamento da VNet** Não selecionado
    - **Grupo de recursos**: use o grupo de recursos existente em que você provisionou seu recurso OpenAI do Azure.
    - **Conta de armazenamento**: crie uma conta de armazenamento com um nome exclusivo
    - **Compartilhamento de arquivos**: criar um compartilhamento de arquivos com um nome exclusivo

    Aguarde um minuto para a criação do armazenamento.

    > **Observação**: se você já tiver um cloud shell configurado em sua assinatura do Azure, talvez seja necessário usar a opção **Redefinir configurações do usuário** no menu ⚙️ para garantir que as versões mais recentes do Python e do .NET Framework estejam instaladas.

4. Verifique se o tipo de shell indicado no canto superior esquerdo do painel do Cloud Shell é *Bash*. Se for o *PowerShell*, altere para *Bash* usando o menu suspenso.

5. Após o terminal iniciar, insira o comando a seguir para baixar o aplicativo de exemplo e salvá-lo em uma pasta chamada `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```
  
6. Os arquivos são baixados para uma pasta chamada **azure-openai**. Navegue até os arquivos de laboratório deste exercício usando o comando a seguir.

    ```bash
   cd azure-openai/Labfiles/02-nlp-azure-openai
    ```

Aplicativos para C# e Python foram fornecidos, bem como um arquivo de texto de exemplo que você usará para testar o resumo. Ambos os aplicativos apresentam a mesma funcionalidade.

Abra o editor de código interno e observe o arquivo de texto que você resumirá com seu modelo localizado em `text-files/sample-text.txt`. Use o comando a seguir para abrir os arquivos de laboratório no editor de código.

```bash
code .
```

## Configurar seu aplicativo

Para este exercício, você concluirá algumas partes importantes do aplicativo para habilitar o uso do recurso OpenAI do Azure.

1. No editor de código, expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de linguagem de programação.

2. Abra o arquivo de configuração da sua linguagem de programação

    - C#: `appsettings.json`
    - Python: `.env`
    
3. Atualize os valores de configuração para incluir o **ponto de extremidade** e a **chave** do recurso OpenAI do Azure que você criou, bem como o nome do modelo que você implantou, `text-turbo`. Salve o arquivo.

4. Navegar até a pasta da sua linguagem de programação preferida e instalar os pacotes necessários

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

5. Abra o código do aplicativo para sua linguagem de programação e adicione o código necessário para criar a solicitação, que especifica os vários parâmetros para seu modelo, como `prompt` e `temperature`.

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));

   // Build completion options object
   ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, "You are a helpful assistant. Summarize the following text in 60 words or less."),
          new ChatMessage(ChatRole.User, text),
       },
       MaxTokens = 120,
       Temperature = 0.7f,
   };

   // Send request to Azure OpenAI model
   ChatCompletions response = client.GetChatCompletions(
       deploymentOrModelName: oaiModelName, 
       chatCompletionsOptions);
   string completion = response.Choices[0].Message.Content;

   Console.WriteLine("Summary: " + completion + "\n");
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key

   # Send request to Azure OpenAI model
   print("Sending request for summary to Azure OpenAI endpoint...\n\n")
   response = openai.ChatCompletion.create(
       engine=azure_oai_model,
       temperature=0.7,
       max_tokens=120,
       messages=[
          {"role": "system", "content": "You are a helpful assistant. Summarize the following text in 60 words or less."},
           {"role": "user", "content": text}
       ]
   )

   print("Summary: " + response.choices[0].message.content + "\n")
    ```

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta.

1. No terminal bash Cloud Shell, navegue até a pasta da linguagem de programação preferida.
1. Execute o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Observe o resumo do arquivo de texto de exemplo.
1. Navegue até o arquivo de código para sua linguagem de programação preferida e altere o valor de *temperatura* para `1`. Salve o arquivo.
1. Execute o aplicativo novamente e observe a saída.

Aumentar a temperatura geralmente faz com que o resumo varie, mesmo quando fornecido o mesmo texto, devido ao aumento da aleatoriedade. Você pode executá-lo várias vezes para ver como a saída pode ser alterada. Tente usar valores diferentes para sua temperatura com a mesma entrada.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no [portal do Azure](https://portal.azure.com?azure-portal=true).
