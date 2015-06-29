<properties
   pageTitle="Obter a mesma experiência de Office 365 em qualquer dispositivo"
   description="Aprenda a compartilhar qualquer aplicativo de Office 365 com os usuários usando o RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/14/2015"
   ms.author="guscatal"/>


Obter a mesma experiência de Office 365 em qualquer dispositivo
===================


Este artigo abordará como implantar o Office 365 em qualquer dispositivo em sua empresa. Os usuários possam obter as mesmas capacidades e a experiência de interface de usuário em Android, Apple e Windows.

Faremos isso usando o Azure RemoteApp hospedando o Office 365 em máquinas virtuais com capacidade de dimensionamento no Azure, às quais os usuários possam se conectar. Chamamos esse conjunto de máquinas virtuais de uma “coleção nuvem”.

----------


Criar uma coleção na nuvem
-------------
Pela primeira vez depois de criar uma conta do Azure, navegue até "RemoteApp" clicando no link no lado esquerdo. ![Mostrando o Azure RemoteApp no Portal do Azure](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Então, prossiga clicando em novo na parte inferior e fazendo a "criação rápida" de uma coleção. Forneça um nome, a região, a assinatura, o plano e a imagem "Office Professional 2013" que fornecemos. ![Caixa de diálogo Criar](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

Depois de você concluir o formulário, o processo de criação de coleção deve começar. Isso pode levar até uma hora ou mais.

![Aguardando](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

Depois que o processo for concluído, ele terá a aparência a seguir. Se clicarmos em "Publicar", podemos ver que a maioria dos aplicativos do Office já foram publicados para nós. ![Coleção criada](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![Aplicativos publicados](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

Nesse ponto você também pode adicionar mais usuários que têm acesso a esta coleção, clicando em "Acesso de usuário" ![Configurar o acesso do usuário](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

Agora vamos experimentar conectar ao Office 365!

Conexão com o Office 365
-------------
Vá até https://www.remoteapp.windowsazure.com/ e clique em "Instalar cliente" para instalar o cliente do Azure RemoteApp no dispositivo que você está. As capturas de tela abaixo são para o Windows.

Depois que o aplicativo for iniciado, pediremos que você entre com sua live ID, insira a mesma usada na sua conta do Azure por enquanto. Quando você tiver feito logon, você deve receber uma notificação sobre novos convites, clique lá e você deve ver uma lista como a mostrada abaixo; então, aceite o convite que corresponde a seu email de proprietário de conta do Azure.

![Novo convite](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

Abaixo, sua aparência quando há novos convites.

![Aceite um aplicativo](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Depois de aceitar o convite, você deve ver todos os aplicativos do Office no cliente do RemoteApp do Azure.

![Lista de aplicativos](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Quando você clica em qualquer um desses, o aplicativo deve ser iniciado na Máquina Virtual do Azure e você deve estar com tudo pronto! Aproveite!

![iniciando](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![powerpoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)

<!--HONumber=52-->
 