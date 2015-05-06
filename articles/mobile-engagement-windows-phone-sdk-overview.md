<properties 
	pageTitle="Integração do SDK do Windows Phone para o Mobile Engagement do Azure" 
	description="Atualizações mais recentes e procedimentos para o SDK do Windows Phone para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# SDK do Windows Phone para o Mobile Engagement do Azure v 2.0.0
Comece aqui para obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo do Windows Phone. Se você gostaria de experimentá-lo primeiro, faça nosso tutorial de [15 minutos](mobile-engagement-windows-phone-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-windows-phone-sdk-content.md)

## Procedimentos de integração

1. Comece por aqui: [Como integrar o Mobile Engagement em seu aplicativo do Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)

2. Para notificações: [Como integrar o Reach (Notificações) em seu aplicativo do Windows Phone](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implementação do plano de marca: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md)

## Notas de versão

### 2.0.0 (17/02/2015)
-   Versão Inicial do Mobile Engagement do Azure
-   A configuração appId/sdkKey é substituída por uma configuração de cadeia de conexão.
-   Aprimoramentos de segurança.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-windows-phone-release-notes.md)

## Procedimentos de atualização

Se você já tiver integrado uma versão anterior do SDK no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK consulte os [procedimentos de atualização](mobile-engagement-windows-phone-upgrade-procedure/) completos. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1" e depois o procedimento "de 0.10.1 a 0.11.0".

### De 1.1.1 a 2.0.0

O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure. 

>[Azure.IMPORTANT] O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.1.1 e depois aplicar o procedimento a seguir

#### Pacote NuGet

Substitua Capptain.WindowsPhone.nupkg por azuresdk-mobileengagement-windowsphone-VERSION.nupkg na pasta lib do pacote de arquivo.

#### Aplicando o Mobile Engagement

O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a esta alteração.

Você precisa desinstalar o pacote nuget do Capptain atual. Considere que todas as alterações na pasta de recursos Capptain serão removidas. Se você quiser manter esses arquivos, então faça uma cópia deles.

Depois disso, instale o novo pacote nuget do Engagement do Microsoft Azure em seu projeto. Você pode encontrá-lo diretamente no [site do NuGet]. ou aqui no índice. Essa ação substitui todos os arquivos de recursos usados pelo Engagement e adiciona a nova DLL do Engagement às suas referências do projeto.

Você precisa limpar as referências do projeto, excluindo as referências de Capptain DLL. Se você não fizer isso, a versão do Capptain entrará em conflito e ocorrerão erros.

Se você personalizou os recursos do Capptain, copie o conteúdo de arquivos antigos e cole-os em novos arquivos do Engagement. Observe que os arquivos xaml e cs devem ser atualizados.

Quando essas etapas forem concluídas, você só precisará substituir as referências antigas do Capptain por novas referências do Engagement.

Todos os namespaces Capptain precisam ser atualizados.

Antes da migração:

			using Capptain.Agent;
			using Capptain.Reach;

Após a migração:

			using Microsoft.Azure.Engagement;

Todas as classes Capptain que contêm "Capptain" devem conter “Engagement".

Antes da migração:

			public sealed partial class MainPage : CapptainPage
			{
			  protected override string GetCapptainPageName()
			  {
			    return "Capptain Demo";
			  }
			  ...
			}

Após a migração:

			public sealed partial class MainPage : EngagementPage
			{
			  protected override string GetEngagementPageName()
			  {
			    return "Engagement Demo";
			  }
			  ...
			}

Para os arquivos xaml o namespace e atributos Capptain também se alteram.

Antes da migração:

			<capptain:CapptainPage
			...
			xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
			...
			</capptain:CapptainPage>

Após a migração:

			<engagement:EngagementPage
			...
			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
			...
			</engagement:EngagementPage>

Para outros recursos como as imagens do Capptain, observe que eles também foram renomeados para usar "Engagement".

#### ID do aplicativo / chave do SDK

O Engagement usa uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma chave do SDK com o Mobile Engagement, você só precisa especificar uma cadeia de conexão. Você pode configurá-la em seu arquivo EngagementConfiguration.

A configuração do Engagement pode ser definida no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de conexão do aplicativo entre as marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

#### Alteração do nome de itens

Todos os itens nomeados ...capptain... foi nomeado ...engagement... É o mesmo para Capptain e Engagement.

Exemplos de itens do Capptain usados normalmente:

> -   CapptainConfiguration agora denominado EngagementConfiguration
> -   CapptainAgent agora denominado EngagementAgent
> -   CapptainReach agora denominado EngagementReach
> -   CapptainHttpConfig agora denominado EngagementHttpConfig
> -   GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.

### Atualizar de versões anteriores

Consulte [Procedimentos de atualização](mobile-engagement-windows-phone-upgrades/)

<!--HONumber=47-->