---
lab:
  title: Use dados próprios com o OpenAI do Azure
---

# Use dados próprios com o OpenAI do Azure

O Serviço OpenAI do Azure permite que você use dados próprios com a inteligência do LLM subjacente. Você pode limitar o modelo para usar apenas seus dados para tópicos pertinentes ou misturá-los com os resultados do modelo pré-treinado.

Este exercício levará aproximadamente **20** minutos.

## Antes de começar

Você precisará de uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure. 

- Para se inscrever em uma assinatura gratuita do Azure, visite [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acesso ao serviço OpenAI do Azure, visite [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionar um recurso de OpenAI do Azure

Antes de usar os modelos do OpenAI do Azure, você precisa provisionar um recurso do OpenAI do Azure em sua assinatura do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com?azure-portal=true).
2. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.
    - **Grupo de recursos**: escolha um grupo de recursos existente ou crie um novo com um nome de sua escolha.
    - **Região**: escolha qualquer região disponível.
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

Para conversar com o OpenAI do Azure, primeiro você precisa implantar um modelo a ser usado por meio do **Azure OpenAI Studio**. Depois de implantado, usaremos o modelo com o playground e usaremos nossos dados para fundamentar as respostas dele.

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

> **Observação**: em algumas regiões, a nova interface de implantação do modelo não mostra a opção **Versão do modelo**. Nesse caso, não se preocupe e continue sem definir a opção

## Observe o comportamento normal do chat sem adicionar seus dados

Antes de conectar o OpenAI do Azure aos seus dados, primeiro observe como o modelo base responde a consultas sem nenhum dado para fundamentação.

1. Navegue até o playground **Chat** e verifique se o modelo implantado está selecionado no painel **Configuração** (esse deverá ser o padrão, se você tiver apenas um modelo implantado).
1. Insira os prompts a seguir e observe a saída.

    ```code
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```code
    What are some facts about New York?
    ```

1. Tente fazer perguntas semelhantes sobre turismo e lugares para ficar para outros locais que serão incluídos em nossos dados de fundamentação, como Londres ou São Francisco. Você provavelmente receberá respostas completas sobre áreas ou bairros, e alguns fatos gerais sobre a cidade.

## Conectar seus dados no playground de chat

Em seguida, adicione seus dados no playground de chat para ver como ele responde usando seus dados como aterramento

1. [Baixe os dados](https://aka.ms/own-data-brochures) que você usará do GitHub. Extraia os PDFs no `.zip` fornecido.
1. Navegue até o playground **Chat** e selecione *Adicionar seus dados* no painel Configuração do assistente.
1. Selecione **Adicionar uma fonte de dados** e escolha *Carregar arquivos* na lista suspensa.
1. Você precisará criar uma conta de armazenamento e um recurso do Azure Cognitive Search. Na lista suspensa do recurso de armazenamento, selecione **Criar um recurso de Armazenamento de Blobs do Azure** e crie uma conta de armazenamento com as configurações a seguir. Deixe qualquer coisa não especificada com o valor padrão.

    - **Assinatura**: *a mesma assinatura do recurso OpenAI do Azure*
    - **Grupo de recursos**: *o mesmo grupo de recursos que o do recurso do Azure OpenAI*
    - **Nome da conta de armazenamento**: *insira um nome globalmente exclusivo*
    - **Região**: *a mesma região que o recurso OpenAI do Azure*
    - **Redundância**: LRS (armazenamento com redundância local)

1. Depois que o recurso estiver sendo criado, volte para o Azure OpenAI Studio e selecione **Criar um recurso de Azure Cognitive Search** com as configurações a seguir. Deixe qualquer coisa não especificada com o valor padrão.

    - **Assinatura**: *a mesma assinatura do recurso OpenAI do Azure*
    - **Grupo de recursos**: *o mesmo grupo de recursos que o do recurso do Azure OpenAI*
    - **Nome do serviço**: *insira um nome globalmente exclusivo*
    - **Local**: *o mesmo local que o do recurso OpenAI do Azure*
    - **Tipo de preço**: Básico

1. Aguarde até que o recurso de pesquisa tenha sido implantado e, em seguida, volte para o Azure AI Studio e atualize a página.
1. Em **Adicionar dados**, insira os seguintes valores para a fonte de dados e selecione **Avançar**.

    - **Selecionar fonte de dados**: carregar arquivos
    - **Selecionar o recurso de Armazenamento de Blobs do Azure**: *escolha o recurso de armazenamento que você criou*
        - Ativar o CORS quando solicitado
    - **Selecionar o recurso do Azure Cognitive Search**: *escolha o recurso de pesquisa que você criou*
    - **Inserir o nome do índice**: margiestravel
    - **Adicionar busca em vetores a este recurso de pesquisa**: desmarcado
    - **Reconheço que conectar-se a uma conta do Azure Cognitive Search incorrerá em uso para minha conta** : verificado

1. Na página **Upload de arquivos**, carregue os PDFs que você baixou e selecione **Avançar**.
1. Na página **Gerenciamento de dados**, selecione o tipo de pesquisa **Palavra-chave** no menu suspenso e, em seguida, **Avançar**.
1. Na página **Revisar e concluir** selecione **Salvar e fechar**, que adicionará seus dados. Isso pode levar alguns minutos, durante os quais você precisa deixar a janela aberta. Após a conclusão, você verá a fonte de dados, o recurso de pesquisa e o índice especificados no painel **Configuração do assistente**.

## Conversar com um modelo fundamentado em seus dados

Agora que você adicionou seus dados, faça as mesmas perguntas que fez anteriormente e veja como a resposta difere.

```
I'd like to take a trip to New York. Where should I stay?
```

```
What are some facts about New York?
```

Você observará uma resposta muito diferente desta vez, com detalhes sobre determinados hotéis e uma menção da Margie's Travel, bem como referências à origem das informações fornecidas. Se você abrir a referência de PDF listada na resposta, verá os mesmos hotéis que o modelo fornecido.

Tente perguntar sobre outras cidades incluídas nos dados de fundamentação, que são Dubai, Las Vegas, Londres e São Francisco.

> **Observação**: **Adicionar seus dados** ainda está em versão prévia e pode nem sempre se comportar conforme o esperado para esse recurso, por exemplo, pode fornecer a referência incorreta para uma cidade não incluída nos dados de fundamentação.

## Conectar seu aplicativo aos seus próprios dados

Em seguida, explore como conectar seu aplicativo para usar seus próprios dados.

### Configurar um aplicativo no Cloud Shell

Para mostrar como conectar um aplicativo OpenAI do Azure aos seus próprios dados, usaremos um aplicativo de linha de comando curto que é executado no Cloud Shell no Azure. Abra uma nova guia do navegador para trabalhar com o Cloud Shell.

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
    cd azure-openai/Labfiles/06-use-own-data
    ```

7. Abra o editor de código integrado executando o comando a seguir:

    ```bash
    code .
    ```

    > **Dica**: consulte a [documentação do editor de código do cloud shell do Azure](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) para obter mais detalhes sobre como usá-lo para trabalhar com arquivos no ambiente do cloud shell do Azure.

## Configurar seu aplicativo

Para este exercício, você concluirá algumas partes importantes do aplicativo para habilitar o uso do recurso OpenAI do Azure. Os aplicativos para C# e Python foram fornecidos. Ambos os aplicativos apresentam a mesma funcionalidade.

1. No editor de código, expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de linguagem de programação.

2. Abra o arquivo de configuração da linguagem de programação escolhida.

    - C#: `appsettings.json`
    - Python: `.env`
    
3. Atualize os valores da configuração para incluir:
    - O **ponto de extremidade** e uma **chave** do recurso Azure OpenAI que você criou (disponível na página **Chaves e Ponto de Extremidade** para seu recurso Azure OpenAI no portal do Azure)
    - O nome especificado para a implantação do modelo (disponível na página **Implantações** no Azure OpenAI Studio).
    - O ponto de extremidade do seu serviço de pesquisa (o valor de **Url** na página de visão geral do seu recurso de pesquisa no portal do Azure).
    - Uma **chave** para seu recurso de pesquisa (disponível na página **Chaves** para seu recurso de pesquisa no portal do Azure - você pode usar qualquer uma das chaves de administrador)
    - O nome do índice de pesquisa (que deve ser `margiestravel`).
    
4. Salve o arquivo de configuração atualizado.

5. No painel do console, insira os seguintes comandos para navegar até a pasta do idioma de sua preferência e instalar os pacotes necessários.

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

6. No editor de código, navegue até sua pasta da linguagem preferida, selecione o arquivo de código e adicione as bibliotecas necessárias.

    **C#**: OwnData.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: ownData.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

7. Revise o arquivo de código, especificamente onde os valores de pesquisa são usados ao concluir os parâmetros para a chamada de API.

## Execute seu aplicativo.

Agora que seu aplicativo foi configurado, execute-o para enviar sua solicitação ao modelo e observe a resposta. Você notará que a resposta agora está fundamentada em seus dados de forma semelhante à experiência do estúdio.

1. No terminal bash Cloud Shell, navegue até a pasta da linguagem de programação preferida.
1. Expanda o terminal para ocupar a maior parte da janela do navegador e execute o aplicativo.

    - **C#** : `dotnet run`
    - **Python**: `python ownData.py`

1. Envie o prompt `Tell me about London` e você verá a resposta fazendo referência aos seus dados.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true). Inclua também a conta de armazenamento e o recurso de pesquisa, pois eles podem incorrer em um custo relativamente grande.
