---
lab:
  title: Introdução ao OpenAI do Azure
---

# Introdução ao Serviço OpenAI do Azure

O Serviço OpenAI do Azure traz os modelos de IA generativa desenvolvidos pelo OpenAI para a plataforma do Azure, permitindo que você desenvolva soluções avançadas de IA que se beneficiem da segurança, escalabilidade e integração de serviços fornecidos pela plataforma de nuvem do Azure. Neste exercício, você aprenderá a começar a usar o OpenAI do Azure provisionando o serviço como um recurso do Azure e usando o Azure OpenAI Studio para implantar e explorar modelos de OpenAI.

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
    - **Grupo de recursos**: escolha um grupo de recursos existente ou crie um novo com um nome de sua escolha.
    - **Região**: escolha qualquer região disponível.
    - **Nome**: um nome exclusivo de sua preferência.
    - **Tipo de preço**: Standard S0
3. Aguarde o fim da implantação. Em seguida, vá para o recurso OpenAI do Azure implantado no portal do Azure.

## Implantar um modelo

O OpenAI do Azure fornece um portal baseado na Web chamado **Azure OpenAI Studio**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará sua exploração do OpenAI do Azure usando o Azure OpenAI Studio para implantar um modelo.

1. Na página **Visão geral** do recurso OpenAI do Azure, use o botão **Vá para o OpenAI do Azure**  para abrir o Est do OpenAI do Azure em uma nova guia do navegador.
2. No Azure OpenAI Studio, na página **Implantações**, exiba suas implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Modelo**: gpt-35-turbo-16k
    - **Versão do Modelo**: atualização automática para padrão
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Opções avançadas**
        - **Filtro de conteúdo**: Padrão
        - **Limite de taxa de tokens por minuto**: 5K\*
        - **Habilitar cota dinâmica**: Habilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

> **Observação**: em algumas regiões, a nova interface de implantação do modelo não mostra a opção **Versão do modelo**. Nesse caso, não se preocupe e continue sem definir a opção.

## Usar o playground Chat

O playground *Chat* fornece uma interface de chatbot para modelos GPT 3.5 e superiores. Ele usa a API *ChatCompletions* em vez da API *Completions* mais antiga.

1. Na seção **Playground**, selecione a página **Chat** e verifique se o seu modelo está selecionado no painel de configuração.
2. Na seção **Configuração do assistente**, na caixa **Mensagem do sistema**, substitua o texto atual pela seguinte instrução: `The system is an AI teacher that helps people learn about AI`.

3. Abaixo da caixa **Mensagem** do sistema, clique em **Adicionar exemplos de poucos disparos** e insira a seguinte mensagem e resposta nas caixas designadas:

    - **Usuário**: `What are different types of artificial intelligence?`
    - **Assistente**: `There are three main types of artificial intelligence: Narrow or Weak AI (such as virtual assistants like Siri or Alexa, image recognition software, and spam filters), General or Strong AI (AI designed to be as intelligent as a human being. This type of AI does not currently exist and is purely theoretical), and Artificial Superintelligence (AI that is more intelligent than any human being and can perform tasks that are beyond human comprehension. This type of AI is also purely theoretical and has not yet been developed).`

    > **Observação**: exemplos de poucas capturas são usados para fornecer ao modelo exemplos dos tipos de respostas esperados. O modelo tentará refletir o tom e o estilo dos exemplos nas respostas dele.

4. Salve as alterações para iniciar uma nova sessão e defina o contexto comportamental do sistema de chat.
5. Na caixa de texto na parte inferior da página, insira o texto `What is artificial intelligence?`
6. Use o botão **Enviar** para enviar a mensagem e exibir a resposta.

    > **Observação**: você pode receber uma resposta de que a implantação da API ainda não está pronta. Nesse caso, aguarde alguns minutos e tente novamente.

7. Examine a resposta e envie a seguinte mensagem para continuar a conversa: `How is it related to machine learning?`
8. Examine a resposta, observando que o contexto da interação anterior é mantido (para que o modelo entenda que "isso" se refere à inteligência artificial).
9. Use o botão **Exibir Código** para exibir o código para a interação. O prompt consiste na mensagem do *sistema*, nos exemplos de poucas capturas de mensagens do *usuário* e *assistente*, e na sequência de mensagens do *usuário* e *assistente* na sessão de chat até o momento.

## Explorar prompts e parâmetros

Você pode usar o prompt e os parâmetros para maximizar a probabilidade de gerar a resposta necessária.

1. No painel **Parâmetros**, defina os seguintes valores de parâmetro:
    - **Temperatura**: 0
    - **Número máximo de respostas**: 500

2. Enviar a mensagem a seguir

    ```
    Write three multiple choice questions based on the following text.

    Most computer vision solutions are based on machine learning models that can be applied to visual input from cameras, videos, or images.*

    - Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    - Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    - Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.
    ```

3. Examine os resultados, que devem ser compostos por perguntas de múltipla escolha que um professor poderia usar para testar os alunos sobre tópicos de pesquisa visual computacional no prompt. A resposta total deve ser menor que o comprimento máximo especificado como um parâmetro.

    Observe o seguinte sobre o prompt e os parâmetros que você usou:

    - O prompt indica especificamente que a saída desejada deve ser três perguntas de múltipla escolha.
    - Os parâmetros incluem *Temperatura*, que controla o grau em que a geração de resposta inclui um elemento de aleatoriedade. O valor de **0** usado em seu envio minimiza a aleatoriedade, resultando em respostas estáveis e previsíveis.

## Explorar a geração de código

Além de gerar respostas de linguagem natural, você pode usar modelos GPT para gerar código.

1. No painel **Configuração do assistente**, selecione o modelo **Exemplo Vazio** para redefinir a mensagem do sistema.
2. Insira a mensagem do sistema: `You are a Python developer.` e salve as alterações.
3. No painel **Sessão de chat**, selecione **Limpar chat** para limpar o histórico do chat e iniciar uma nova sessão.
4. Envie a seguinte mensagem de usuário:

    ```
    Write a Python function named Multiply that multiplies two numeric parameters.
    ```

5. Examine a resposta, que deve incluir um código Python de exemplo que atenda ao requisito no prompt.

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no [portal do Azure](https://portal.azure.com).
