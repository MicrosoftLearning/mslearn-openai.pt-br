---
lab:
  title: Gerar imagens com um modelo DALL-E
---

# Gerar imagens com um modelo DALL-E

O Serviço OpenAI do Azure inclui um modelo de geração de imagens chamado DALL-E. Você pode usar esse modelo para enviar prompts de linguagem natural que descrevem uma imagem desejada e o modelo gerará uma imagem original com base na descrição fornecida.

Neste exercício, você usará um modelo DALL-E versão 3 para gerar imagens com base em prompts de linguagem natural.

Este exercício levará aproximadamente **25** minutos.

## Provisionar um recurso de OpenAI do Azure

Antes de usar o Azure OpenAI para gerar imagens, você deve provisionar um recurso do Azure OpenAI em sua assinatura do Azure. O recurso deve estar em uma região em que há suporte para modelos DALL-E.

1. Entre no **portal do Azure** em `https://portal.azure.com`.
1. Crie um recurso do **OpenAI do Azure** com as seguintes configurações:
    - **Assinatura**: *Selecione uma assinatura do Azure aprovada para acesso ao Serviço OpenAI do Azure, incluindo DALL-E*
    - **Grupo de recursos**: *escolher ou criar um grupo de recursos*
    - **Região**: *Escolha **Leste dos EUA** ou **Suécia Central***\*
    - **Nome**: *um nome exclusivo de sua preferência*
    - **Tipo de preço**: Standard S0

    > Os modelos DALL-E 3 \* só estão disponíveis nos recursos do Serviço OpenAI do Azure nas regiões **Leste dos EUA** e **Suécia Central**.

1. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.
1. Na página **Visão geral** do recurso OpenAI do Azure, role para baixo até a seção **Introdução** e selecione o botão para ir para o **AI Studio**.
1. No Estúdio de IA do Azure, no painel à esquerda, selecione a página **Implantações** e visualize as implantações de modelo existentes. Se você ainda não tiver uma para DALL-E 3, crie uma nova implantação do modelo **dall-e-3** com as seguintes configurações:
    - **Nome da implantação**: dalle3
    - **Versão do modelo**: *usar a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Unidades de capacidade**: mil
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica:**: desativado
1. Depois de implantar, navegue de volta para a página **Imagens** no painel esquerdo.

## Explorar a geração de imagens no playground de imagens

Você pode usar o playground Imagens no **Estúdio de IA do Azure** para testar a geração de imagens.

1. Na seção **playground Imagens**, sua implantação do DALL-E 3 será selecionada automaticamente. Se não estiver, selecione a implantação no menu suspenso de implantação.
1. Na caixa **Prompt**, insira uma descrição de uma imagem que você gostaria de gerar. Por exemplo, `An elephant on a skateboard` e, em seguida, selecione **Gerar** e veja a imagem gerada.

    ![O playground Imagens no Estúdio de IA do Azure com uma imagem gerada.](../media/images-playground.png)

1. Modifique o prompt para fornecer uma descrição mais específica. Por exemplo, `An elephant on a skateboard in the style of Picasso`. Em seguida, gere a nova imagem e examine os resultados.

    ![O playground Imagens no Estúdio de IA do Azure com duas imagens geradas.](../media/images-playground-new-style.png)

## Usar a API REST para gerar imagens

O serviço OpenAI do Azure fornece uma API REST que você pode usar para enviar prompts para geração de conteúdo, incluindo imagens geradas por um modelo DALL-E.

### Preparar-se para desenvolver um aplicativo no Visual Studio Code

Agora vamos explorar como você pode criar um aplicativo personalizado que usa o Serviço OpenAI do Azure para gerar imagens. Você desenvolverá seu aplicativo usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: Se você já clonou o repositório **mslearn-openai**, abra-o no código do Visual Studio. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
2. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-openai` em uma pasta local (não importa qual pasta).
3. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

4. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

### Configurar seu aplicativo

Os aplicativos para C# e Python foram fornecidos. Ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você adicionará o ponto de extremidade e a chave para o recurso do Azure OpenAI ao arquivo de configuração do aplicativo.

1. No Visual Studio Code, no painel **Explorer**, navegue até a pasta **Labfiles/05-image-generation** e expanda a pasta **CSharp** ou **Python**, dependendo da sua preferência de idioma. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.
2. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de configuração para o seu idioma preferido

    - **C#**: appsettings.json
    - **Python**: .env
    
3. Atualize os valores de configuração para incluir o **ponto de extremidade** e a **chave** do recurso do Azure OpenAI criado (disponível na página **Chaves e ponto de extremidade** para o recurso do Azure OpenAI no portal do Azure).
4. Salve o arquivo de configuração.

### Exibir código do aplicativo

Agora você está pronto para explorar o código usado para chamar a API REST e gerar uma imagem.

1. No painel do **Explorer**, selecione o arquivo de código principal para o seu aplicativo:

    - C#: `Program.cs`
    - Python: `generate-image.py`

2. Examine o código que o arquivo contém, observando os seguintes principais recursos:
    - O código faz uma solicitação HTTPS para o ponto de extremidade do serviço, incluindo a chave do serviço no cabeçalho. Ambos esses valores são obtidos do arquivo de configuração.
    - A solicitação inclui alguns parâmetros, incluindo o prompt de onde a imagem deveria ser baseada, o número de imagens a serem geradas e o tamanho das imagens geradas.
    - A resposta inclui um prompt revisado que o modelo DALL-E extrapolou do prompt fornecido pelo usuário para torná-lo mais descritivo, e a URL da imagem gerada.
    
    > **Importante**: se você nomeou sua implantação diferente do *dalle3* recomendado, precisará atualizar o código para usar o nome da sua implantação.

### Executar o aplicativo

Agora que você revisou o código, é hora de executá-lo e gerar algumas imagens.

1. Clique com o botão direito do mouse na pasta **CSharp** ou **Python** que contém seus arquivos de código e abra um terminal integrado. Em seguida, insira o comando apropriado para executar o seu aplicativo:

   **C#**
   ```
   dotnet run
   ```
   
   **Python**
   ```
   pip install requests
   python generate-image.py
   ```

3. Quando solicitado, insira uma descrição para uma imagem. Por exemplo, *Uma girafa empinando pipa*.

4. Aguarde até que a imagem seja gerada – um hiperlink será exibido no painel do terminal. Em seguida, selecione o hiperlink para abrir uma nova guia do navegador e examinar a imagem que foi gerada.

   > **DICA**: Se o aplicativo não retornar uma resposta, aguarde um minuto e tente novamente. Os recursos recém-implantados podem levar até 5 minutos para ficarem disponíveis.

5. Feche a guia do navegador que contém a imagem gerada e execute novamente o aplicativo para gerar uma nova imagem com um prompt diferente.

## Limpar

Quando terminar de usar o recurso do Azure OpenAI, lembre-se de excluir o recurso no **portal do Azure** em `https://portal.azure.com`.
