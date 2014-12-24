﻿<properties title="Manage an Azure website" pageTitle="Gerenciar um site do Azure" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Gerenciar um site do Azure

Este tópico contém links para recursos para gerenciar um site do Azure. O gerenciamento inclui todas as tarefas que mantém seu site executando perfeitamente. 

Durante o ciclo de vida de um site, você realizará diferentes tarefas de gerenciamento, a medida em que você move da implantação inicial à operação normal, manutenção e atualizações.

- [Antes de você implantar seu site para produção]
- [Enquanto seu site está executando]
- [Quando você atualiza seu site]

Muitas tarefas de gerenciamento de site podem ser realizadas no portal do Azure. Para obter mais informações, consulte [Gerenciar sites por meio do Portal de Gerenciamento do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage/).

## Antes de você implantar seu site para produção

### Escolha uma faixa

Os sites do Azure são oferecidos em quatro faixas: Free, Shared, Basic e Standard. Para obter informações sobre os recursos e preços para cada faixa, consulte [Detalhes dos preços](http://azure.microsoft.com/pt-br/pricing/details/websites/). 

- [Os Planos de Hospedagem na Web](http://azure.microsoft.com/pt-br/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) permitem que seu grupo tenha vários sites na mesma faixa.
- Você pode sempre [alternar as faixas](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/) após criar seu site.

### Configuração

Use o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) para definir várias opções de configuração. Para obter detalhes, consulte [Como configurar sites](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure/). Aqui está uma lista de verificação rápida:

- Selecione **versões de tempo de execução** para .NET, PHP, Java ou Python, se necessário.
- Habilite **WebSockets** se seu site usa o protocolo WebSocket. (Isto inclui aplicativos que usam o [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-nodejs-chat-app-socketio/).)
- Você está executando trabalhos web contínuos? Se estiver, habilite **Sempre ativo**.
- Defina o **documento padrão**, como index.html.

Além desta definições de configurações básicas, talvez você queira configurar o seguinte:

A criptografia - **Secure Socket Layer (SSL)**. Para usar a SSL com um nome de domínio personalizado, você deve solicitar um certificado SSL e configurar seu site para usá-lo. Consulte [Habilitar HTTPS para um site do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/).
- **Nome de domínio personalizado.** O seu site tem automaticamente um subdomínio em azurewebsites.net. Você pode associar um nome de domínio personalizado, como contoso.com. Consulte [Configurar um nome de domínio personalizado](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/).

Configuração específica de idioma:

- **PHP**: [Como configurar o PHP nos sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-configure/).
- **Python**: [Configurar o Python com os Sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-python-configure/)


## Enquanto seu site está executando

Enquanto seu site está executando, você deseja se certificar que ele esteja disponível e que seja reduzido para atender o tráfego do usuário. Você talvez precise solucionar erros.

### Monitoramento

- Por meio do Portal de Gerenciamento, você pode [adicionar métricas de desempenho](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-monitor) como uso da CPU e número de solicitações de cliente.
- Para uma percepção mais aprofundada, use o New Relic para monitorar e gerenciar o desempenho. Consulte [Gerenciamento de desempenho do aplicativo New Relic nos sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Dimensione seu site](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/) em resposta ao tráfego. Dependendo da sua faixa, você pode reduzir o número de VMs e/ou o tamanho das instâncias da VM. No Plano Padrão, você também pode configurar a autoescala, para que seu site dimensione automaticamente, em uma programação fixa ou em resposta ao carregamento. 
 
### Backups

- Definir [backups automáticos](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/) do seu site. Saiba mais sobre backups [neste vídeo](http://azure.microsoft.com/pt-br/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Saiba sobre as opções para a [recuperação de banco de dados](http://msdn.microsoft.com/pt-br/library/azure/hh852669.aspx) no banco de dados SQL do Azure.

### Solucionar problemas

- Se algo der errado, você pode [solucionar no Visual Studio](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug), usando os logs de diagnóstico e depuração ao vivo na nuvem. 
- Fora do Visual Studio, há várias maneiras de coletar os logs de diagnóstico. Consulte [Habilitar o log de diagnóstico para os sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-enable-diagnostic-log/).
- Para os aplicativos Node.js, consulte [Como depurar um aplicativo Node.js nos sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-nodejs-debug/).

### Restaurando dados

- [Restaurar](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-restore/) um site no qual foi realizado o backup anteriormente.


## Quando você atualiza seu site

Se você não habilitou os backups automáticos, você pode criar um [backup manual](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/).

Considere o uso de uma [implantação em estágios](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-staged-publishing/). Esta opção permite que você publique atualizações para uma implantação em estágios que executam lado a lado com a sua implantação de produção. 

Se você usar o Visual Studio Online, é possível configurar a implantação contínua a partir do controle de origem:

- [Usando o Controle de Versão do Team Foundation (TFVC)](http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Usando a grade](http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Antes de você implantar seu site para produção]: #before-you-deploy-your-site-to-production
[Enquanto seu site está executando]: #while-your-website-is-running
[Quando você atualiza seu site]: #when-you-update-your-website

 