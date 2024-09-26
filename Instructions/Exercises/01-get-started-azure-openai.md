---
lab:
  title: Comece com o serviço Azure OpenAI
---

# Comece com o serviço Azure OpenAI

O Serviço OpenAI do Azure traz os modelos de IA generativa desenvolvidos pelo OpenAI para a plataforma do Azure, permitindo que você desenvolva soluções avançadas de IA que se beneficiem da segurança, escalabilidade e integração de serviços fornecidos pela plataforma de nuvem do Azure. Nesse exercício, você aprenderá como começar a usar o OpenAI do Azure provisionando o serviço como um recurso do Azure e usando o Estúdio de IA do Azure para implantar e explorar modelos de IA generativa.

No cenário deste exercício, você desempenhará o papel de um desenvolvedor de software encarregado de implementar um agente de IA que possa usar IA generativa para ajudar uma organização de marketing a melhorar sua eficácia em alcançar clientes e anunciar novos produtos. As técnicas utilizadas no exercício podem ser aplicadas a qualquer cenário em que uma organização pretenda utilizar modelos generativos de IA para ajudar os funcionários a serem mais eficazes e produtivos.

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

O Azure fornece um portal baseado na Web chamado **Estúdio de IA do Azure**, que você pode usar para implantar, gerenciar e explorar modelos. Você iniciará sua exploração do OpenAI do Azure usando o Estúdio de IA do Azure para implantar um modelo.

> **Observação**: À medida que você usa o Estúdio de IA do Azure, podem ser exibidas caixas de mensagens sugerindo tarefas para você executar. Você pode fechá-los e seguir as etapas desse exercício.

1. No portal do Azure, na página **Visão geral** do recurso OpenAI do Azure, role para baixo até a seção **Introdução** e selecione o botão para acessar o **AI Studio**.
1. No Estúdio de IA do Azure, no painel à esquerda, selecione a página **Implantações** e visualize as implantações de modelo existentes. Se você ainda não tiver uma implantação, crie uma nova implantação do modelo **gpt-35-turbo-16k** com as seguintes configurações:
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Modelo**: gpt-35-turbo-16k *(se o modelo 16k não estiver disponível, escolha gpt-35-turbo)*
    - **Versão do modelo**: *usar a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Limite de taxa de tokens por minuto**: 5K\*
    - **Filtro de conteúdo**: Padrão
    - **Habilitar cota dinâmica**: Desabilitado

    > \* Um limite de taxa de 5.000 tokens por minuto é mais do que adequado para concluir este exercício, deixando capacidade para outras pessoas que usam a mesma assinatura.

## Usar o playground Chat

Agora que implantou um modelo, você pode usá-lo para gerar respostas com base em prompts de linguagem natural. O playground de *Chat* no Estúdio de IA do Azure fornece uma interface de chatbot para modelos GPT 3.5 e superiores.

> **Observação:** O playground *Chat* usa a API *ChatCompletions* em vez da API *Completions* mais antiga que é usada pelo playground *Completions*. O playground Completions é fornecido para compatibilidade com modelos mais antigos.

1. Na seção **Playground**, selecione a página **Chat**. A página do playground do **Chat** consiste em uma série de botões e dois painéis principais (que podem ser organizados da direita para a esquerda na horizontal ou de cima para baixo na vertical, dependendo da resolução da tela):
    - **Configuração** - usada para selecionar sua implantação, definir a mensagem do sistema e definir parâmetros para interagir com sua implantação.
    - **Sessão de chat** - usada para enviar mensagens de bate-papo e exibir respostas.
1. Em **Implantações**, certifique-se de que a implantação do modelo gpt-35-turbo-16k esteja selecionada.
1. Revise a **Mensagem do sistema** padrão, que deve ser *Você é assistente de IA que ajuda as pessoas a encontrar informações.* A mensagem do sistema é incluída nos prompts enviados ao modelo e fornece contexto para as respostas do modelo; definir expectativas sobre como um agente de IA baseado no modelo deve interagir com o usuário.
1. No painel **Sessão de chat**, insira a consulta do usuário `How can I use generative AI to help me market a new product?`

    > **Observação**: você pode receber uma resposta de que a implantação da API ainda não está pronta. Nesse caso, aguarde alguns minutos e tente novamente.

1. Revise a resposta, observando que o modelo gerou uma resposta coesa em linguagem natural que é relevante para a consulta que foi solicitada.
1. Insira a consulta do usuário `What skills do I need if I want to develop a solution to accomplish this?`.
1. Revise a resposta, observando que a sessão de chat manteve o contexto conversacional (portanto, “isto” é interpretado como uma solução de IA generativa para marketing). Essa contextualização é obtida incluindo o histórico de conversas recentes em cada envio de prompt sucessivo, de modo que o prompt enviado ao modelo para a segunda consulta inclua a consulta e a resposta originais, bem como a nova entrada do usuário.
1. Na barra de ferramentas do painel **Sessão de chat**, selecione **Limpar chat** e confirme que deseja reiniciar a sessão de chat.
1. Insira a consulta `Can you help me find resources to learn those skills?` e revise a resposta, que deve ser uma resposta válida em linguagem natural, mas como o histórico de chat anterior foi perdido, a resposta provavelmente será sobre como encontrar recursos genéricos de qualificação, em vez de estar relacionada às habilidades específicas necessárias para construir uma solução generativa de marketing de IA.

## Experimente mensagens do sistema, prompts e exemplos rápidos

Até agora, você conversou por chat com seu modelo com base na mensagem padrão do sistema. Você pode personalizar a configuração do sistema para ter mais controle sobre os tipos de respostas geradas pelo seu modelo.

1. Na barra de ferramentas principal, selecione as **Amostras de prompt** e use o modelo de prompt **Marketing Writing Assistant**.
1. Revise a nova mensagem do sistema, que descreve como um agente de IA deve usar o modelo para responder.
1. No painel **Sessão de chat**, insira a consulta do usuário `Create an advertisement for a new scrubbing brush`.
1. Revise a resposta, que deve incluir um texto publicitário para uma escova de esfregar. A cópia pode ser bastante extensa e criativa.

    Em um cenário real, um profissional de marketing provavelmente já saberia o nome do produto de escova e também teria algumas ideias sobre os principais recursos que deveriam ser destacados em um anúncio. Para obter os resultados mais úteis de um modelo generativo de IA, os usuários precisam projetar seus prompts para incluir o máximo possível de informações pertinentes.

1. Digite o prompt `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`.
1. Revise a resposta, que deve levar em consideração as informações adicionais fornecidas sobre o produto da escova de esfregar.

    A resposta agora deve ser mais útil, mas para ter ainda mais controle sobre a saída do modelo, você pode fornecer um ou mais exemplos de *poucos disparos* nos quais as respostas devem ser baseadas.

1. Na caixa de texto **Mensagem do sistema**, expanda a lista suspensa da **seção Adicionar** e selecione **Exemplos**. Em seguida, digite a seguinte mensagem e resposta nas caixas designadas:

    **Usuário**:
    
    ```prompt
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```
    
    **Assistente:**
    
    ```prompt
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. Use o botão **Aplicar alterações** para salvar os exemplos e iniciar uma nova sessão.
1. Na seção **Sessão de chat**, insira a consulta do usuário `Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`.
1. Revise a resposta, que deve ser um novo anúncio do "Scrubadub 2000" modelado no exemplo "Ultramop" fornecido na configuração do sistema.

## Experimente com parâmetros

Você explorou como a mensagem, os exemplos e os prompts do sistema podem ajudar a refinar as respostas retornadas pelo modelo. Você também pode usar parâmetros para controlar o comportamento do modelo.

1. No painel **Configuração**, selecione a guia **Parâmetros** e defina *os seguintes valores de parâmetro:
    - **Resposta máxima**: 1.000
    - **Temperatura**: 1

1. Na seção **Sessão de chat**, use o botão **Limpar chat** para redefinir a sessão de chat. Em seguida, insira a consulta do usuário `Create an advertisement for a cleaning sponge` e revise a resposta. A cópia do anúncio resultante deve incluir no máximo 1000 tokens de texto e alguns elementos criativos - por exemplo, o modelo pode ter inventado um nome de produto para a esponja e feito algumas afirmações sobre suas características.
1. Use o botão **Limpar chat** para redefinir a sessão de bate-papo novamente e, em seguida, insira novamente a mesma consulta de antes (`Create an advertisement for a cleaning sponge`) e revise a resposta. A resposta pode ser diferente da resposta anterior.
1. No painel **Configuração**, na aba **Parâmetros**, altere o valor do parâmetro **Temperatura** para 0.
1. Na seção **Sessão de chat**, use o botão **Limpar chat** para redefinir a sessão de chat novamente e, em seguida, insira novamente a mesma consulta de antes (`Create an advertisement for a cleaning sponge`) e revise a resposta. Dessa vez, a resposta pode não ser tão criativa.
1. Use o botão **Limpar chat** para redefinir a sessão de bate-papo mais uma vez e, em seguida, insira novamente a mesma consulta de antes (`Create an advertisement for a cleaning sponge`) e revise a resposta; que deve ser muito semelhante (se não idêntico) à resposta anterior.

    O parâmetro **Temperatura** controla o grau em que o modelo pode ser criativo na geração de uma resposta. Um valor baixo resulta em uma resposta consistente com pouca variação aleatória, enquanto um valor alto incentiva o modelo a adicionar elementos criativos à sua saída; o que pode afetar a precisão e o realismo da resposta.

## Implante seu modelo em um aplicativo web

Agora que explorou algumas das funcionalidades de um modelo de IA generativa no playground do Estúdio de IA do Azure, você pode implantar um aplicativo Web do Azure para fornecer uma interface básica de agente de IA por meio da qual os usuários podem conversar com o modelo.

> **Observação**: o Estúdio de IA do Azure ainda está em versão prévia. Para alguns usuários, não é possível realizar a implantação no aplicativo Web devido a um bug no modelo no estúdio. Se for esse o caso, pule esta seção.

1. No canto superior direito da página do playground **Chat**, no menu **Implantar em**, selecione **Um novo aplicativo web**.
1. Na caixa de diálogo **Implantar em um aplicativo web**, crie um novo aplicativo web com as seguintes configurações:
    - **Nome**: *um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *O grupo de recursos no qual você provisionou seu recurso Azure OpenAI*
    - **Localizações**: *A região onde você provisionou seu recurso Azure OpenAI*
    - **Plano de preços**: Gratuito (F1) - *Se não estiver disponível, selecione Básico (B1)*
    - **Habilite o histórico de chat no aplicativo da web**: <u>Não</u> selecionado
    - **Reconheço que os aplicativos Web incorrerão no uso da minha conta**: Selecionado
1. Implante o novo aplicativo da web e aguarde a conclusão da implantação (o que pode levar cerca de 10 minutos)
1. Após a implantação bem-sucedida do seu aplicativo Web, use o botão no canto superior direito da página do **Chat** Playground para iniciar o aplicativo Web. O aplicativo pode levar alguns minutos para iniciar. Se solicitado, aceite a solicitação de permissões.
1. No aplicativo da web, insira a seguinte mensagem de chat:

    ```prompt
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. Revise a resposta.

    > **Observação**: Você implantou o *modelo* em um aplicativo Web, mas essa implantação não inclui as configurações e os parâmetros do sistema definidos no playground; portanto, a resposta pode não refletir os exemplos que você especificou no playground. Em um cenário real, você adicionaria lógica ao seu aplicativo para modificar o prompt para que ele incluísse os dados contextuais apropriados para os tipos de resposta que você deseja gerar. Esse tipo de personalização está além do escopo desse exercício de nível introdutório, mas você pode aprender sobre técnicas de engenharia imediata e APIs do Azure OpenAI em outros exercícios e na documentação do produto.

1. Quando terminar de experimentar seu modelo no aplicativo Web, feche a guia do aplicativo Web no navegador para retornar ao Estúdio de IA do Azure.

## Limpar

Quando terminar o recurso do OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no **portal do Azure** em `https://portal.azure.com`.
