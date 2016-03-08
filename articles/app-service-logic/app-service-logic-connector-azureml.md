<properties
   pageTitle="Usando o Conector de Aprendizado de Máquina do Azure em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector de Aprendizado de Máquina do Azure e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="jehollan"/>
   
# Visão geral
O Conector de AM do Azure para Aplicativos Lógicos permite chamar as APIs de AM do Azure para pontuação em lote (Serviço de execução em lote) e readaptação. Esses recursos, junto com os Gatilhos do Aplicativo Lógico, permitem o agendamento de trabalhos em lotes e a configuração da readaptação de modelos.

 ![][1]
 
## Introdução ao Conector de Aprendizado de Máquina do Azure e à adição dele ao seu Aplicativo Lógico
Para começar, crie um experimento no Estúdio AM do Azure e depois configure e implante um serviço Web. Você pode usar a URL da API e a Chave da URL de Postagem de BES, encontradas na página de Ajuda de Execução em Lote. ([Saiba mais](https://github.com/Azure/azure-content/blob/master/articles/machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

Para executar um trabalho de BES usando o Conector, adicione o Conector de AM do Azure ao seu Aplicativo Lógico. Em seguida, insira as informações necessárias (veja mais informações sobre isso logo abaixo). Para configurar a Readaptação, adicione um segundo Conector de AM do Azure e forneça os parâmetros de entrada (confira [aqui](machine-learning-retrain-models-programmatically.md) mais informações sobre como definir um modelo para a readaptação).

## Executar um trabalho de Execução em Lote de AM do Azure
O Conector de AM do Azure oferece as quatro opções a seguir para executar trabalhos de Execução em Lote (BES): 1. Trabalho em Lotes com Entrada e Saída: o Teste tem módulos de entrada e saída do serviço Web 2. Trabalho em Lotes Sem Entrada e Saída: o Teste não tem módulos de entrada e saída do serviço Web (por exemplo, usa os módulos Leitor e Gravador) 3. Trabalho em Lotes apenas com Entrada: o Teste tem um módulo de entrada de serviço Web, mas nenhum módulo de saída de serviço Web (por exemplo, usa um módulo Gravador) 4. Trabalho em Lotes apenas com Saída: o Teste não tem um módulo de entrada de serviço Web, mas tem um módulo de saída de serviço Web (por exemplo, usa um módulo de Leitor). Observe que o BES é uma solicitação assíncrona e pode demorar para ser concluído dependendo do tamanho dos dados e da complexidade do modelo. Quando o trabalho estiver concluído, o Conector retornará o resultado de saída.

### Executar a Execução em Lotes: com Entrada e Saída
Se o Teste no Estúdio tiver módulos de entrada e saída no serviço Web, será necessário fornecer informações sobre a conta de armazenamento de blob e o local ([saiba mais aqui](machine-learning-consume-web-services.md)). Além disso, você pode incluir Parâmetros Globais (serviço Web) se estiver configurando em teste ([saiba mais aqui](machine-learning-web-service-parameters.md)).

![][2]

Clique na reticências para mostrar e ocultar os campos de Parâmetros Globais. Isso permite que você forneça um ou mais parâmetros globais (serviço Web) em uma lista de campos e valores separados por vírgula.

![][3]

Outras variações em trabalhos de BES, como um trabalho sem entrada ou saída no serviço Web, também estão disponíveis por meio do Conector.

## Configuração da readaptação

Use a ação Definir readaptação para configurar uma readaptação única ou agendada de seu modelo de AM. Junto com um trabalho de Execução em Lote criado no Conector, você pode concluir as etapas para treinamento e atualização do modelo de um serviço Web. Nesse fluxo de trabalho, você usa o Conector duas vezes. 1. O primeiro Conector é usado para executar o trabalho de BES a fim de readaptar seu modelo e retornar a saída. A saída dessa execução terá a URL do novo modelo (.ilearner). Opcionalmente, ela também poderá, se você tiver configurado isso em seu teste, retornar a URL da saída do módulo Avaliar, que é um arquivo csv. Na próxima etapa, você poderá usar os dados na saída do módulo Avaliar para tomar uma decisão sobre substituir ou não o modelo em seu serviço Web (por exemplo, se Precisão for maior do que 0,85). 1. O segundo Conector é usado para configurar a Readaptação. Ele usa parâmetros da saída do primeiro Conector para verificar, opcionalmente, a condição do modelo de atualização e atualizar o serviço Web com o modelo recém-treinado. * Por exemplo, você pode acessar a saída do Trabalho de BES com a URL do modelo recém-treinado inserindo `@{body('besconnector').Results.output2.FullURL}` no campo URL do Modelo Readaptado. Isso pressupõe que a saída do serviço Web em seu Teste de Treinamento chama-se output2. * Para o Nome do Recurso, use o nome completo do Modelo Treinado salvo no Teste de Previsão, por exemplo, MyTrainedModel [modelo treinado] * Para o campo Chave do Resultado da Avaliação, você pode inserir qualquer um dos parâmetros retornados na saída do Módulo Avaliar do Teste de Treinamento (se você o tiver incluído). Você pode ver a lista de parâmetros disponíveis ao visualizar os resultados do módulo Avaliar no Teste de Treinamento no Estúdio AM do Azure. Para um teste de classificação, isso incluiria a Exatidão, a Precisão, o Cancelamento, a Pontuação F, o AUC, a Perda Média de Log e a Perda de Log de Treinamento.

![][4]
 
### Readaptação agendada
 
Usando os Gatilhos do Aplicativo Lógico, você pode configurar o Conector para execução com base em um agendamento. Isso pode permitir a readaptação regular de um modelo à medida que novos dados chegam. O trabalho de BES readaptaria o modelo e a ação de Readaptação atualizaria o modelo após a conclusão do treinamento.
 
![][5]
 
## Conector de saída 
 
**BES**: após a conclusão do trabalho em lotes, a saída do Conector terá as seguintes informações para cada saída do serviço Web.
 
 ![][6]
 
Observe que isso não estará disponível se você não tiver incluído uma saída do serviço Web (por exemplo, se você estiver usando um Módulo de Gravador para gravar em um banco de dados desde o Teste no Estúdio).

**Readaptação**: após a conclusão da Readaptação, a saída terá as seguintes informações.

![][7]

## Resumo

Usando o Conector de AM do Azure para Aplicativos Lógicos, você pode executar a pontuação em lote e readaptar os trabalhos a serem executados sob demanda ou de acordo com um agendamento recorrente. A combinação das duas ações pode classificar seus dados e readaptar, avaliar e atualizar automaticamente o modelo do serviço Web sem a necessidade de escrever qualquer código.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_1217_2015-->