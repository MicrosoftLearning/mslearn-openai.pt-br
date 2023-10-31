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
2. No Azure OpenAI Studio, crie uma implantação com as seguintes configurações:
    - **Nome do modelo**: gpt-35-turbo
    - **Versão do modelo**: *usar a versão padrão*
    - **Nome da implantação**: text-turbo

## Observe o comportamento normal do chat sem adicionar seus dados

Antes de conectar o OpenAI do Azure aos seus dados, primeiro observe como o modelo base responde a consultas sem nenhum dado para fundamentação.

1. Navegue até o playground **Chat** e verifique se o modelo `gpt-35-turbo` implantado está selecionado no painel **Configuração** (esse deverá ser o padrão, se você tiver apenas um modelo implantado).
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

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true). Inclua também a conta de armazenamento e o recurso de pesquisa, pois eles podem incorrer em um custo relativamente grande.
