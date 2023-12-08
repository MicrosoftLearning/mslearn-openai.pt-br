---
lab:
  title: Gerar e aprimorar o código com o Serviço OpenAI do Azure
---

# Gerar e aprimorar o código com o Serviço OpenAI do Azure

Os modelos do Serviço OpenAI do Azure podem gerar código para você usando prompts de linguagem natural, corrigindo bugs no código concluído e fornecendo comentários de código. Esses modelos também podem explicar e simplificar o código existente para ajudar você a entender o que ele faz e como aprimorá-lo.

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
    - **Grupo de recursos**: escolha um grupo de recursos existente ou crie um novo com um nome de sua escolha.
    - **Região**: escolha qualquer região disponível.
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.
4. Navegue até a página **Chaves e Ponto de Extremidade** e salve-os em um arquivo de texto a ser usado posteriormente.

## Implantar um modelo

Para usar a API OpenAI do Azure para geração de código, primeiro você precisa implantar um modelo a ser usado por meio do **Azure OpenAI Studio**. Depois de implantado, usaremos o modelo com o playground e referenciaremos esse modelo em nosso aplicativo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Explorar** para abrir o Azure OpenAI Studio em uma nova guia do navegador. Como alternativa, navegue diretamente até o [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
2. No Azure OpenAI Studio, na página **Implantações**, exiba suas implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Modelo**: gpt-35-turbo-16k
    - **Versão do Modelo**: atualização automática para padrão
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Opções avançadas**
        - **Filtro de conteúdo**: Padrão
        - **Limite de taxa de tokens por minuto**: 5K\*
        - **Habilitar cota dinâmica**: Habilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

> **Observação**: em algumas regiões, a nova interface de implantação do modelo não mostra a opção **Versão do modelo**. Neste caso, não se preocupe e continue sem definir a opção

## Gerar código no playground de chat

Antes de usar em seu aplicativo, examine como o OpenAI do Azure pode gerar e explicar o código no playground de chat.

1. No [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), navegue até o playground **Chat** no painel esquerdo.
1. 1. Na **Configuração**, verifique se a implantação do modelo está selecionada.
1. Na seção **Configuração do assistente** na parte superior, selecione o modelo de mensagem do sistema **Padrão**.
1. Na seção **Sessão de chat**, insira o prompt a seguir e pressione *Enter*.

    ```code
   Write a function in python that takes a character and string as input, and returns how many times that character appears in the string
    ```

1. O modelo provavelmente responderá com uma função, com alguma explicação do que a função faz e como chamá-la.
1. Em seguida, envie o prompt `Do the same thing, but this time write it in C#`.
1. Observe a saída. O modelo provavelmente respondeu de maneira muito semelhante à primeira vez, mas desta vez codificando em C#. Você pode solicitar novamente uma linguagem diferente de sua escolha ou uma função para concluir uma tarefa diferente, como reverter a cadeia de caracteres de entrada.
1. Em seguida, vamos explorar o uso da IA para entender o código com este exemplo de uma função aleatória que você viu escrita no Ruby. Envie o prompt a seguir como a mensagem do usuário.

    ```code
    What does the following function do?  
    ---  
    def random_func(n)
      start = [0, 1]
      (n - 2).times do
        start << start[-1] + start[-2]
      end
      start.shuffle.each do |num|
        puts num
      end
    end
    ```

1. Observe a saída, que explica o que a função faz em linguagem natural. Tente pedir ao modelo para reescrevê-la em uma linguagem de programação com a qual você está familiarizado.

## Configurar um aplicativo no Cloud Shell

Para mostrar como integrar com um modelo do OpenAI do Azure, usaremos um aplicativo de linha de comando curto que é executado no Cloud Shell no Azure. Abra uma nova guia do navegador para trabalhar com o Cloud Shell.

1. No [portal do Azure](https://portal.azure.com?azure-portal=true), selecione o botão **[>_]** (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Um painel do Cloud Shell é aberto na parte inferior do portal.

    ![Captura de tela da inicialização do Cloud Shell com um clique no ícone à direita da caixa de pesquisa superior.](../media/cloudshell-launch-portal.png#lightbox)

2. Na primeira vez que você abrir o Cloud Shell, talvez precise escolher o tipo de shell que deseja usar (*Bash* ou *PowerShell).* Selecione **Bash**. Se não vir essa opção, ignore a etapa.  

3. Se for solicitado que você crie armazenamento para o Cloud Shell, selecione **Mostrar configurações avançadas** e selecione as seguintes configurações:
    - **Assinatura**: sua assinatura
    - **Regiões do Cloud Shell**: escolha qualquer região disponível
    - **Mostrar configurações de isolamento de VNET** Desmarcado
    - **Grupo de recursos**: use o grupo de recursos existente em que você provisionou o recurso do Azure OpenAI
    - **Conta de armazenamento**: crie uma nova conta de armazenamento com um nome exclusivo
    - **Compartilhamento de arquivos**: crie um novo compartilhamento de arquivos com um nome exclusivo

    Aguarde um minuto para a criação do armazenamento.

    > **Observação**: se você já tiver um cloud shell configurado em sua assinatura do Azure, talvez seja necessário usar a opção **Redefinir configurações do usuário** no menu ⚙️ para garantir que as versões mais recentes do Python e do .NET Framework estejam instaladas.

4. Verifique se o tipo de shell indicado na parte superior esquerda do painel do Cloud Shell está marcado como *Bash*. Se for o *PowerShell*, altere para *Bash* usando o menu suspenso.

5. Após o terminal iniciar, insira o comando a seguir para baixar o aplicativo de exemplo e salvá-lo em uma pasta chamada `azure-openai`.

    ```bash
    rm -r azure-openai -f
    git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. Os arquivos são baixados para uma pasta chamada **azure-openai**. Navegue até os arquivos de laboratório deste exercício usando o comando a seguir.

    ```bash
    cd azure-openai/Labfiles/04-code-generation
    ```

7. Abra o editor de código integrado executando o comando a seguir:

    ```bash
    code .
    ```

8. No editor de código, expanda a pasta **sample-code** e revise os arquivos de código que seu aplicativo usará no modelo para melhorar.

    > **Dica**: consulte a [documentação do editor de código do cloud shell do Azure](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) para obter mais detalhes sobre como usá-lo para trabalhar com arquivos no ambiente do cloud shell do Azure.

## Configurar seu aplicativo

Para este exercício, você concluirá algumas partes importantes do aplicativo para habilitar o uso do recurso OpenAI do Azure. Os aplicativos para C# e Python foram fornecidos.

1. No editor de código, expanda a pasta de linguagens de programação para a sua linguagem preferida.

2. Abra o arquivo de configuração da linguagem de programação escolhida.

    - **C#** : `appsettings.json`
    - **Python**: `.env`

3. Atualize os valores de configuração para incluir o **ponto de extremidade** e a **chave** do recurso OpenAI do Azure que você criou, bem como o nome da implantação. Salve o arquivo.

4. No painel do console, insira os seguintes comandos para navegar até a pasta do idioma de sua preferência e instalar os pacotes necessários.

    **C#**

    ```bash
    cd CSharp
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.9
    ```

    **Python**

    ```bash
    cd Python
    pip install python-dotenv
    pip install openai==1.2.0
    ```

5. Selecione o arquivo de código nessa pasta para sua linguagem de programação e adicione as bibliotecas necessárias.

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: code-generation.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

6. Adicione o código necessário para configurar o cliente.

    **C#**: Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**: code-generation.py

    ```python
    # Set OpenAI configuration settings
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2023-05-15"
            )
    ```

7. Na função que chama o modelo OpenAI do Azure, adicione o código para formatar e enviar a solicitação para o modelo.

    **C#**: Program.cs

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
        MaxTokens = 1000,
        DeploymentName = oaiModelName
    };

    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);

    ChatCompletions completions = response.Value;
    string completion = completions.Choices[0].Message.Content;
    ```

    **Python**: code-generation.py

    ```python
    # Build the messages array
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para tentar gerar código para cada caso de uso. O caso de uso é numerado no aplicativo e pode ser executado em qualquer ordem.

> **Observação**: alguns usuários podem ter limitação de taxa se chamarem o modelo com muita frequência. Se você enfrentar um erro sobre um limite de taxa de token, aguarde um minuto e tente novamente.

1. No editor de código, expanda a pasta `sample-code` e observe brevemente a função e o aplicativo para sua linguagem de programação. Esses arquivos serão usados para as tarefas no aplicativo.
1. No terminal bash Cloud Shell, navegue até a pasta da linguagem de programação preferida.
1. Execute o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python code-generation.py`

1. Escolha a opção **1** para adicionar comentários ao seu código. Observe que a resposta pode levar alguns segundos para cada uma dessas tarefas.
1. Os resultados serão colocados em `result/app.txt`. Abra esse arquivo e compare-o com o arquivo de função no `sample-code`.
1. Em seguida, escolha a opção **2** para gravar testes de unidade para essa mesma função.
1. Os resultados substituirão o que estava em `result/app.txt` e detalharão quatro testes de unidade para essa função.
1. Em seguida, escolha a opção **3** para corrigir bugs em um aplicativo para jogar o Go Fish.
1. Os resultados substituirão o que estava em `result/app.txt` e devem ter um código muito semelhante por algumas coisas corrigidas.

    - **C#** : as correções são feitas nas linhas 30 e 59
    - **Python**: as correções são feitas nas linhas 18 e 31

O aplicativo para Go Fish no `sample-code` poderá ser executado se você substituir as linhas por bugs pela resposta do Azure OpenAI. Se você executá-lo sem as correções, ele não funcionará corretamente.

É importante observar que, embora o código desse aplicativo Go Fish tenha sido corrigido para alguma sintaxe, ele não é uma representação estritamente precisa do jogo. Se você olhar com atenção, há problemas em não verificar se o baralho está vazio ao dar as cartas, não remover pares da mão dos jogadores quando eles recebem um par e alguns outros bugs cuja identificação exige compreensão de jogos de cartas. Este é um ótimo exemplo de como os modelos de IA generativos podem ser úteis para ajudar na geração de código, mas não podem ser confiáveis como corretos e precisam ser verificados pelo desenvolvedor.

Se você quiser ver a resposta completa do OpenAI do Azure, poderá definir a variável `printFullResponse` como `True` e executar novamente o aplicativo.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true).
