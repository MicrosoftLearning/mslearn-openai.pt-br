---
lab:
  title: Gerar imagens com um modelo DALL-E
---

# Gerar imagens com um modelo DALL-E

O Serviço OpenAI do Azure inclui um modelo de geração de imagens chamado DALL-E. Você pode usar esse modelo para enviar prompts de linguagem natural que descrevem uma imagem desejada e o modelo gerará uma imagem original com base na descrição fornecida.

Este exercício levará aproximadamente **25** minutos.

## Antes de começar

Você precisará de uma assinatura do Azure que tenha sido aprovada para acesso ao serviço OpenAI do Azure, incluindo DALL-E. Se você já solicitou acesso ao serviço OpenAI do Azure, talvez seja necessário enviar outro aplicativo para obter acesso ao DALL-E.

- Para se inscrever em uma assinatura gratuita do Azure, visite [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acesso ao serviço OpenAI do Azure, visite [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionar um recurso de OpenAI do Azure

Antes de usar os modelos do OpenAI do Azure, você precisa provisionar um recurso do OpenAI do Azure em sua assinatura do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: uma assinatura do Azure aprovada para acessar o serviço OpenAI do Azure.
    - **Grupo de recursos**: crie um grupo de recursos com um nome de sua escolha.
    - **Região**: escolha **EastUS** como a região
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.
4. Navegue até a página **Chaves e Ponto de Extremidade**. Você pode recuperar o ponto de extremidade exclusivo e as chaves de autenticação para seu serviço daqui. Você precisará deles mais tarde.

## Explorar a geração de imagens no playground da DALL-E

Você pode usar o playground da DALL-E no **Azure OpenAI Studio** para experimentar a geração de imagens.

1. No portal do Azure, na página **Visão geral** do recurso do Azure OpenAI, use o botão **Explorar** para abrir o Azure OpenAI Studio em uma nova guia do navegador. Como alternativa, navegue diretamente até o [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
2. Selecione o **Playground da DALL-E**.
3. Na caixa **Prompt**, insira uma descrição de uma imagem que você gostaria de gerar. Por exemplo, *Um elefante em um skate*. Em seguida, selecione **Gerar** e exiba a imagem gerada.

    ![O Playground da DALL-E no Azure OpenAI Studio com uma imagem gerada.](../media/dall-e-playground.png)

4. Modifique o prompt para fornecer uma descrição mais específica. Por exemplo, *Um elefante em um skate no estilo de Picasso*. Em seguida, gere a nova imagem e examine os resultados.

    ![O Playground da DALL-E no Azure OpenAI Studio com duas imagens geradas.](../media/dall-e-playground-new-image.png)

## Usar a API REST para gerar imagens

O serviço OpenAI do Azure fornece uma API REST que você pode usar para enviar prompts para geração de conteúdo, incluindo imagens geradas por um modelo DALL-E.

### Preparar o ambiente do aplicativo

Neste exercício, você usará um aplicativo Python ou Microsoft C# simples para gerar imagens chamando a API REST. Você executará o código na interface do console do cloud shell no portal do Azure.

1. No [portal do Azure](https://portal.azure.com?azure-portal=true), selecione o botão **[>_]** (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Um painel do Cloud Shell é aberto na parte inferior do portal. 

    ![Captura de tela da inicialização do Cloud Shell com um clique no ícone à direita da caixa de pesquisa superior.](../media/cloudshell-launch-portal.png#lightbox)

2. Na primeira vez que você abrir o Cloud Shell, talvez precise escolher o tipo de shell que deseja usar (*Bash* ou *PowerShell).* Selecione **Bash**. Se não vir essa opção, ignore a etapa.  

3. Se você precisar criar o armazenamento para o Cloud Shell, verifique se sua assinatura está especificada e selecione **Criar armazenamento**. Aguarde um minuto para a criação do armazenamento.

    > **Observação**: se você já tiver um cloud shell configurado em sua assinatura do Azure, talvez seja necessário usar a opção **Redefinir configurações do usuário** no menu ⚙️ para garantir que as versões mais recentes do Python e do .NET Framework estejam instaladas.

4. Verifique se o tipo de shell indicado na parte superior esquerda do painel do Cloud Shell está marcado como *Bash*. Se for o *PowerShell*, altere para *Bash* usando o menu suspenso.

5. Depois que o terminal for iniciado, insira o comando a seguir para baixar o código do aplicativo com o qual você trabalhará.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

    Os arquivos são baixados para uma pasta chamada **azure-openai**. Os aplicativos para C# e Python foram fornecidos. Ambos os aplicativos apresentam a mesma funcionalidade.

6. Navegue até a pasta da linguagem de programação que você deseja usar executando o comando apropriado.

    **Python**

    ```bash
   cd azure-openai/Labfiles/05-image-generation/Python
    ```

    **C#**

    ```bash
   cd azure-openai/Labfiles/05-image-generation/CSharp
    ```

7. Use o comando a seguir para abrir o editor de código interno e ver os arquivos de código com os quais você trabalhará.

    ```bash
   code .
    ```

### Configurar seu aplicativo

O aplicativo usa um arquivo de configuração para armazenar os detalhes necessários para se conectar à sua conta do serviço Azure OpenAI.

1. No editor de código, selecione o arquivo de configuração para seu aplicativo , dependendo da sua preferência de linguagem de programação.

    - C#: `appsettings.json`
    - Python: `.env`
    
2. Atualize os valores de configuração para incluir o **Ponto de Extremidade** e a **Key1** para o serviço OpenAI do Azure e salve o arquivo.

    > **Dica**: você pode ajustar a divisão na parte superior do painel do Cloud Shell para ver o portal do Azure e obter os valores de ponto de extremidade e chave da página **Chaves e Ponto de Extremidade** para o serviço OpenAI do Azure.

3. Se você estiver usando o **Python**, também precisará instalar o pacote **python-dotenv** usado para ler o arquivo de configuração. No painel de prompt do console, verifique se a pasta atual é **~/azure-openai/Labfiles/05-image-generation/Python**. Em seguida, insira este comando:

    ```bash
   pip install python-dotenv
    ```

### Exibir código do aplicativo

Agora você está pronto para explorar o código usado para chamar a API REST e gerar uma imagem.

1. No painel do editor de código, selecione o arquivo de código principal para seu aplicativo:

    - C#: `Program.cs`
    - Python: `generate-image.py`

2. Examine o código que o arquivo contém, observando os seguintes principais recursos:
    - O código faz solicitações https para o ponto de extremidade do serviço, incluindo a chave do serviço no cabeçalho. Ambos esses valores são obtidos do arquivo de configuração.
    - O processo consiste em <u>duas</u> solicitações REST: uma para iniciar a solicitação de geração de imagem e outra para recuperar os resultados.
    A solicitação inicial inclui os seguintes dados:
        - O prompt fornecido pelo usuário que descreve a imagem a ser gerada
        - O número de imagens a serem geradas (nesse caso, 1)
        - A resolução (tamanho) da imagem a ser gerada.
    - O cabeçalho de resposta da solicitação inicial inclui um valor **operation-location** usado para o retorno de chamada subsequente para obter os resultados.
    - O código sonda a URL de retorno de chamada até que o status da tarefa de geração de imagem seja *bem-sucedido* e então extrai e exibe uma URL para a imagem gerada.

### Executar o aplicativo

Agora que você revisou o código, é hora de executá-lo e gerar algumas imagens.

1. No painel de prompt do console, insira o comando apropriado para executar seu aplicativo:

    **Python**

    ```bash
   python generate-image.py
    ```

    **C#**

    ```bash
   dotnet run
    ```

2. Quando solicitado, insira uma descrição para uma imagem. Por exemplo, *Uma girafa empinando pipa*.

3. Aguarde até que a imagem seja gerada – um hiperlink será exibido no painel do console. Em seguida, selecione o hiperlink para abrir uma nova guia do navegador e examinar a imagem que foi gerada.

4. Feche a guia que contém a imagem gerada e execute novamente o aplicativo para gerar uma nova imagem com um prompt diferente.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true).
