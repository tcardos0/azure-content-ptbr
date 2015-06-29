<properties 
	pageTitle="Armazenar scripts de servidor no controle do código-fonte - Serviços Móveis do Azure" 
	description="Saiba como usar o complemento New Relic para monitorar seu serviço móvel." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>

# Usar o New Relic para monitorar serviços móveis

Este tópico mostra como configurar o complemento New Relic de terceiros para trabalhar com os Serviços Móveis do Azure a fim de fornecer monitoramento aprimorado de seu serviço móvel.

O tutorial o orientará por estas etapas:

1. [Inscrever-se para o New Relic usando a Azure Store].
2. [Instalar o módulo New Relic].
3. [Ativar a análise para o desenvolvedor do New Relic para o serviço móvel].
4. [Monitorar o serviço móvel no painel do New Relic].

Para concluir este tutorial, você já deverá ter criado um serviço móvel, concluindo o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados].

##<a name="sign-up"></a>Inscreva-se no New Relic usando a Azure Store

A primeira etapa é adquirir o serviço New Relic. Este tutorial mostra como adquirir o serviço na Azure Store. Os Serviços Móveis oferecem suporte a inscrições do New Relic adquiridas fora da Azure Store.

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. No painel inferior do portal de gerenciamento, clique em **Novo**.

3. Clique em **Repositório**.

4. Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.

5. Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.

7. Digite um nome para como o serviço New Relic aparecerá nas configurações do Azure ou use o valor padrão **NewRelic**. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.

8. Escolha um valor para a região; por exemplo, **oeste dos Estados Unidos**.

9. Clique em **Próximo**.

10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços, bem como os termos legais. Se você concordar com os termos, clique em **Comprar**

11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.

##<a name="install-module"></a>Instalar o módulo New Relic

Depois de se inscrever para o serviço New Relic, você deverá instalar o módulo Node.js do New Relic em seu serviço móvel. O controle de origem deverá estar ativado para que o seu serviço móvel possa carregar o módulo.

1. Se ainda não tiver feito isso, siga as etapas no tutorial [Armazenar scripts de servidor no controle do código-fonte] para habilitar o controle de origem para o seu serviço móvel, clonar o repositório e instalar o <a href="http://nodejs.org/" target="_blank">NPM (Gerenciador de Pacotes do Node)</a>.

2. Navegue até a pasta `.\service` do seu repositório Git local e, em seguida, no prompt de comando, execute o comando a seguir:

		npm install newrelic

	O NPM instalará o [módulo New Relic][newrelic] no subdiretório `\newrelic`.

3. Abra uma ferramenta de linha de comando Git, como **GitBash** (Windows) ou **Bash** (Unix Shell), e digite o seguinte comando no prompt de comando Git:

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	Isso carregará o novo módulo `newrelic` para o seu serviço móvel.

Em seguida, você deverá habilitar o monitoramento do New Relic de seu serviço móvel no [Portal de Gerenciamento][Azure Management Portal].

##<a name="enable-service"></a>Habilitar a análise para o desenvolvedor do New Relic para o serviço móvel

1. No [Portal de Gerenciamento][Azure Management Portal], selecione o serviço móvel e clique na guia **Configurar**.

	![][0]

2. Role para baixo até **Análise para o Desenvolvedor** e siga um destes procedimentos, dependendo de como você adquiriu sua assinatura do New Relic:

	+ Compre na Azure Store:

		Clique em **Complemento**, selecione o complemento New Relic em **Escolher Complemento** e clique em **Salvar**.

		![][1]

	+ Adquirido diretamente do New Relic:

		Clique em **Personalizado**, selecione New Relic em **Provedor**, digite a sua chave e clique em **Salvar**.

		![][2]

		A chave pode ser obtida no painel do New Relic.

3. Após a conclusão do registro, você verá um novo valor em **Configurações do Aplicativo**:

	![][3]

##<a name="monitor"></a>Monitorar o serviço móvel no painel do New Relic

1. Execute o aplicativo cliente para gerar solicitações de leitura, criação, atualização e exclusão para seu serviço móvel.

2. Aguarde alguns minutos para que os dados sejam processados e navegue até o painel do New Relic.

	Se a sua assinatura do New Relic tiver sido adquirida como um complemento, selecione-a no [Portal de Gerenciamento][Azure Management Portal]e clique em **Gerenciar**.

3. No New Relic, clique em **Aplicativos** e em seu serviço móvel.

	![][4]

4. Clique nas **transações Web** para ver as solicitações recentes que acabou de fazer para o seu serviço móvel:

	![][5]

##<a name="next-steps"> </a>Próximas etapas

+ Para otimizar o desempenho do seu aplicativo móvel para **iOS**/**Android**, consulte [New Relic Mobile].
+ Para informações sobre preços, consulte o [página nova Relíquia no armazenamento do Azure]
+ Para obter mais informações sobre como usar o New Relic, consulte [Visão geral de aplicativos] na documentação do New Relic. 

<!-- Anchors. -->
[Inscrever-se para o New Relic usando a Azure Store]: #sign-up
[Instalar o módulo New Relic]: #install-module
[Ativar a análise para o desenvolvedor do New Relic para o serviço móvel]: #enable-service
[Monitorar o serviço móvel no painel do New Relic]: #monitor
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Work with server scripts in Mobile Services]: ../work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[Armazenar scripts de servidor no controle do código-fonte]: /develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[página nova Relíquia no armazenamento do Azure]: /gallery/store/new-relic/new-relic/
[Visão geral de aplicativos]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started/
[Introdução aos dados]: /develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring


<!--HONumber=54--> 