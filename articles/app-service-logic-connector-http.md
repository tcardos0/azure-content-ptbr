﻿<properties 
   pageTitle="Ouvinte e Conector HTTP" 
   description="Usando o ouvinte HTTP e a ação de HTTP em seu aplicativo lógico" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


# Usando o ouvinte HTTP e a ação de HTTP em seu aplicativo lógico#

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.  No entanto, existem alguns cenários em que você pode precisar trabalhar com conexões HTTP diretas, como:

1.	Para desenvolver um aplicativo lógico que dá suporte para um front-end de interativo de usuário móvel ou Web.
2.	Para obter e processar dados de um serviço Web que não tem um conector pré-configurado.
3.	Para executar ações que já estão expostas como um serviço Web, mas não estão disponíveis como aplicativo de API.


Nesses cenários, a galeria fornece duas opções

1. O ouvinte HTTP:  Este conector atua como gatilho e escuta as solicitações HTTP em um ponto de extremidade configurado.  Quando uma chamada é recebida no ponto de extremidade configurado, ele aciona uma nova instância do fluxo e transmite os dados recebidos na solicitação para o fluxo de processamento.  Ele também pode ser configurado para responder automaticamente à solicitação de entrada quando o fluxo foi iniciado ou permitir que você construa uma resposta com base na execução de fluxo e enviar uma resposta para o chamador.
2.	A ação de HTTP:  Isso permite que você configure uma solicitação Web para qualquer ponto de extremidade disponível na Internet, obtém uma resposta de volta e a disponibiliza para uso no fluxo.

## Criando um ouvinte HTTP para seu aplicativo lógico
Para usar o ouvinte HTTP, você precisa primeiro criar uma instância do aplicativo de API do ouvinte HTTP.  Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Ouvinte HTTP".
3.	Configure o ouvinte HTTP da seguinte maneira:

	![][1]

4.	Ao definir as configurações de pacote, você verá a seguinte opção sobre se o ouvinte deve responder automaticamente ou exigir que você envie uma resposta explícita.  Defina como falso para poder enviar sua própria resposta.

	![][2]

5.	Clique em OK para criar.
6.	Depois de criar a instância de aplicativo de API, abra as configurações para configurar a segurança.  O ouvinte HTTP atualmente dá suporte à autenticação básica.  Você pode configurar isso usando a opção de segurança quando abre o ouvinte HTTP.

	![][3]

Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o ouvinte HTTP.

## Usando o ouvinte HTTP em seu aplicativo lógico
Depois de ter criado seu aplicativo de API, você pode usar o ouvinte HTTP como gatilho para o aplicativo lógico.  Para fazer isso, você precisa:

4.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o ouvinte HTTP.
5.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.  O ouvinte HTTP apareceria na seção "Usados Recentemente" na galeria à direita.  Selecione-o.
6.	Agora, você pode definir o método HTTP e a URL relativa em que você precisa do ouvinte para disparar o fluxo.

	![][4]
	![][5]

7.	Para obter o URI completo, clique duas vezes no ouvinte HTTP para exibir suas configurações e copie a URL para o "Host" de seu aplicativo de API.


	![][6]
8.	Agora você pode usar os dados recebidos na solicitação HTTP em outras ações no fluxo da seguinte maneira:
	![][7]
	![][8]
9.	Por fim, para enviar uma resposta, adicione outro ouvinte HTTP e selecione a ação de Enviar Resposta HTTP.  Defina a ID da solicitação como a RequestID obtida do ouvinte HTTP e preencha o corpo da resposta e o status HTTP que você deseja retornar
	![][9]

## Usando a ação HTTP
A ação HTTP tem suporte nativo pelos aplicativos lógicos e não é necessária a criação de um aplicativo de API para poder usá-la.  Você pode inserir uma ação HTTP a qualquer momento em seu aplicativo lógico e escolher o URI, os cabeçalhos e o corpo para a chamada.
A ação HTTP dá suporte a várias opções de segurança do lado cliente.  Para usá-las, leia o artigo [aqui](http://aka.ms/logicapphttpauth).

A saída da ação HTTP são os cabeçalhos e o corpo, que podem ser usados downstream no fluxo, de maneira semelhante a como as saídas de outras ações e conectores são usadas.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png







<!--HONumber=49-->