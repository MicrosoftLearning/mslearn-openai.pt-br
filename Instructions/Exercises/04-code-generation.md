---
lab:
  title: Gerar e aprimorar o código com o Serviço OpenAI do Azure
  status: stale
---

# Gerar e aprimorar o código com o Serviço OpenAI do Azure

Os modelos do Serviço OpenAI do Azure podem gerar código para você usando prompts de linguagem natural, corrigindo bugs no código concluído e fornecendo comentários de código. Esses modelos também podem explicar e simplificar o código existente para ajudar você a entender o que ele faz e como aprimorá-lo.

No cenário deste exercício, você desempenhará o papel de um desenvolvedor de software explorando como usar a IA generativa para tornar as tarefas de codificação mais fáceis e eficientes. As técnicas usadas no exercício podem ser aplicadas a outros arquivos de código, linguagens de programação e casos de uso.

Este exercício levará aproximadamente **25** minutos.

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

    > \* Os recursos do OpenAI do Azure são restritos por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. A escolha aleatória de uma região reduz o risco de uma só região atingir o limite de cota em cenários nos quais você compartilha uma assinatura com outros usuários. Caso um limite de cota seja atingido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente.

3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

O Azure fornece um portal baseado na Web chamado **portal do Azure AI Foundry**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará a exploração do OpenAI do Azure usando o portal do Azure AI Foundry para implantar um modelo.

> **Observação**: à medida que você usar o portal do Azure AI Foundry, poderão ser exibidas caixas de mensagens sugerindo tarefas para você executar. Você pode fechá-los e seguir as etapas desse exercício.

1. No portal do Azure, na página **Visão geral** do recurso OpenAI do Azure, role para baixo até a seção **Introdução** e clique no botão para acessar o **portal do AI Foundry** (antigo Estúdio de IA).
1. No portal do Azure AI Foundry, no painel à esquerda, selecione a página **Implantações** e visualize as implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Modelo**: gpt-35-turbo-16k *(se o modelo 16k não estiver disponível, escolha gpt-35-turbo)*
    - **Versão do modelo**: *usar a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Desabilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Gerar código no playground de chat

Antes de usar em seu aplicativo, examine como o OpenAI do Azure pode gerar e explicar o código no playground de chat.

1. Na seção **Playground**, selecione a página **Chat**. A página do playground do **Chat** consiste em uma série de botões e dois painéis principais (que podem ser organizados da direita para a esquerda na horizontal ou de cima para baixo na vertical, dependendo da resolução da tela):
    - **Configuração** - usada para selecionar sua implantação, definir a mensagem do sistema e definir parâmetros para interagir com sua implantação.
    - **Sessão de chat** - usada para enviar mensagens de bate-papo e exibir respostas.
1. Em **Implantações**, verifique se a implantação do modelo está selecionada.
1. Na área **Mensagem do sistema**, defina a mensagem do sistema como `You are a programming assistant helping write code` e aplique as alterações.
1. Na **Sessão de chat**, envie a seguinte consulta:

    ```
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    O modelo provavelmente responderá com uma função, com alguma explicação do que a função faz e como chamá-la.

1. Em seguida, envie o prompt `Do the same thing, but this time write it in C#`.

    O modelo provavelmente respondeu de maneira muito semelhante à primeira vez, mas desta vez codificando em C#. Você pode solicitar novamente uma linguagem diferente de sua escolha ou uma função para concluir uma tarefa diferente, como reverter a cadeia de caracteres de entrada.

1. Em seguida, vamos explorar o uso da IA para reconhecer o código. Envie a solicitação a seguir como a mensagem do usuário.

    ```
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    O modelo deve descrever o que a função faz, que é multiplicar dois números usando um loop.

7. Envie a solicitação `Can you simplify the function?`.

    O modelo deve gravar uma versão mais simples da função.

8. Envie a solicitação: `Add some comments to the function.`

    O modelo adiciona comentários ao código.

## Preparar-se para desenvolver um aplicativo no Visual Studio Code

Agora vamos explorar como você pode criar um aplicativo personalizado que usa o Serviço OpenAI do Azure para gerar imagens. Você desenvolverá seu aplicativo usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: Se você já clonou o repositório **mslearn-openai**, abra-o no código do Visual Studio. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
2. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

## Configurar seu aplicativo

Aplicativos para C# e Python foram fornecidos, bem como um arquivo de texto de exemplo que você usará para testar o resumo. Ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes-chave do aplicativo para habilitar o uso do recurso do OpenAI do Azure.

1. No Visual Studio Code, no painel do **Explorer**, navegue até a pasta **Labfiles/04-code-generation** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de idioma. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
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
    - O **nome de implantação** que você especificou para a implantação do modelo (disponível na página **Implantações** no portal do Azure AI Foundry).
5. Salve o arquivo de configuração.

## Adicionar código para usar o modelo de serviço do OpenAI do Azure

Agora você está pronto para usar o SDK do OpenAI do Azure para consumir seu modelo implantado.

1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código para o seu idioma preferido. Na função que chama o modelo do OpenAI do Azure, no comentário ***Formate e envie a solicitação para o modelo***, adicione o código para formatar e envie a solicitação para o modelo.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemPrompt),
            new ChatRequestUserMessage(userPrompt)
        },
        Temperature = 0.7f,
        MaxTokens = 1000,
        DeploymentName = oaiDeploymentName
    };

    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);

    ChatCompletions completions = response.Value;
    string completion = completions.Choices[0].Message.Content;
    ```

    **Python**: code-generation.py

    ```python
    # Format and send the request to the model
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

4. Salve as alterações no arquivo de código.

## Executar o aplicativo

Agora que seu aplicativo foi configurado, execute-o para tentar gerar código para cada caso de uso. O caso de uso é numerado no aplicativo e pode ser executado em qualquer ordem.

> **Observação**: alguns usuários podem ter limitação de taxa se chamarem o modelo com muita frequência. Se você enfrentar um erro sobre um limite de taxa de token, aguarde um minuto e tente novamente.

1. No painel **Explorer**, expanda a pasta **Labfiles/04-code-generation/sample-code** e revise a função e o aplicativo *go-fish* para o seu idioma. Esses arquivos serão usados para as tarefas no aplicativo.
2. No painel do terminal interativo, verifique se o contexto da pasta é a pasta da sua linguagem de preferência. Em seguida, insira o comando a seguir para executar o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python code-generation.py`

    > **Dica**: você pode usar o ícone **Maximizar tamanho do painel** (**^**) na barra de ferramentas do terminal para ver mais do texto do console.

3. Escolha a opção **1** para adicionar comentários ao código e insira a solicitação a seguir. Observe que a resposta pode levar alguns segundos para cada uma dessas tarefas.

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    Os resultados serão colocados em **resultado/app.txt**. Abra esse arquivo e compare-o com o arquivo de função em **código de exemplo**.

4. Em seguida, escolha a opção **2** para gravar testes de unidade para essa mesma função e insira a solicitação a seguir.

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    Os resultados substituirão o que estava em **resultado/app.txt**e detalharão quatro testes de unidade para essa função.

5. Em seguida, escolha a opção **3** para corrigir bugs em um aplicativo para jogar o Go Fish. Insira a solicitação a seguir:

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    Os resultados substituirão o que estava em **resultado/app.txt** e deverão ter um código muito semelhante, com algumas correções.

    - **C#** : as correções são feitas nas linhas 30 e 59
    - **Python**: as correções são feitas nas linhas 18 e 31

    O aplicativo para Go Fish em **código de exemplo** poderá ser executado se você substituir as linhas que contêm bugs pela resposta do OpenAI do Azure. Se você executá-lo sem as correções, ele não funcionará corretamente.
    
    > **Observação**: É importante observar que, embora o código desse aplicativo Go Fish tenha sido corrigido em relação a alguma sintaxe, ele não é uma representação estritamente precisa do jogo. Se você olhar com atenção, há problemas em não verificar se o baralho está vazio ao dar as cartas, não remover pares da mão dos jogadores quando eles recebem um par e alguns outros bugs cuja identificação exige compreensão de jogos de cartas. Este é um ótimo exemplo de como os modelos de IA generativos podem ser úteis para ajudar na geração de código, mas não podem ser confiáveis como corretos e precisam ser verificados pelo desenvolvedor.

    Se quiser ver a resposta completa do OpenAI do Azure, você pode definir a variável **printFullResponse** como `True` e executar novamente o aplicativo.

## Limpar

Quando terminar o recurso do OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no **portal do Azure** em `https://portal.azure.com`.
