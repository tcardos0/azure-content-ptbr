<properties
	pageTitle="Como fornecer acesso remoto seguro a aplicativos locais"
	description="Aborda como usar o Proxy de Aplicativo do AD do Azure para fornecer acesso remoto seguro aos seus aplicativos locais."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/07/2015"
	ms.author="rkarlin"/>

# Como fornecer acesso remoto seguro a aplicativos locais

Você deseja fornecer acesso para usuários remotos que têm todos os tipos de dispositivos – gerenciados, não gerenciados; tablets, smartphones e laptops. E fornecer acesso seguro a uma infinidade de recursos pode ser complexo. Nos últimos anos, proxies reversos eram uma maneira popular de fornecer acesso remoto seguro, mas precisavam estar atrás de firewalls que eram difíceis de proteger e de deixar altamente disponíveis.

## Acesso remoto seguro na nuvem
Em um ambiente de nuvem moderno, levamos o acesso remoto para o próximo nível usando o Proxy de Aplicativo no Active Directory do Microsoft Azure. O Proxy de Aplicativo é um recurso do AD do Azure que é fornecido como um serviço, o que significa que é fácil de implantar e usar. Ele se integra ao Active Directory do Azure, a mesma plataforma de identidade utilizada pelo Office 365.

## O que é o Proxy de Aplicativo do Active Directory do Azure?
O Proxy de Aplicativo oferece logon único e acesso remoto seguro para aplicativos da Web hospedados no local, como sites do SharePoint e Outlook Web Access. Agora, os aplicativos da Web local podem ser acessados da mesma forma que os aplicativos SaaS no Active Directory do Azure, sem a necessidade de uma VPN ou de alteração na infraestrutura de rede. O Proxy de Aplicativo permite que você publique aplicativos. Funcionários podem fazer logon em seus aplicativos de casa, em seus próprios dispositivos e realizar a autenticação por meio desse proxy baseado em nuvem.

## Como ele funciona?
### Como habilitar o acesso
O Proxy de Aplicativo funciona por meio da instalação de um serviço leve do Windows Server chamado Conector dentro de sua rede. O Conector não precisa abrir nenhuma porta de entrada, e não é necessário colocar nada no DMZ. Se você tiver muito tráfego em seus aplicativos, poderá adicionar mais Conectores e o serviço se encarregará do balanceamento de carga. Os Conectores não têm monitoração de estado e extraem tudo da nuvem conforme o necessário. Quando um usuário acessa aplicativos remotamente, a partir de qualquer dispositivo, é autenticado pelo Active Directory do Azure e obtém acesso ao aplicativo.

### Logon único
O Proxy de Aplicativo do AD do Azure oferece logon único (SSO) a aplicativos que usam IWA ou aplicativos com reconhecimento de declaração. Se seu aplicativo usa a autenticação integrada do Windows, o Proxy de Aplicativo representa o usuário usando a delegação restrita de Kerberos para fornecer o SSO. O SSO para aplicativos com reconhecimento de declaração é obtido porque o usuário já foi autenticado pelo Active Directory do Azure.

## Como começar
Verifique se você tem uma assinatura premium ou básica do AD do Azure e um diretório do AD do Azure para os quais é um administrador global. Você também precisa de licenças básicas ou premium do AD do Azure para o administrador do diretório e os usuários que acessam os aplicativos. Veja aqui para obter mais informações.

### Introdução à obtenção de acesso remoto a aplicativos locais
A configuração do Proxy de Aplicativo é realizada em duas etapas:

1. [Habilitar o Proxy de Aplicativo e configurar o Conector](active-directory-application-proxy-enable.md)<br>
2. [Publicar aplicativos](active-directory-application-proxy-publish.md) - use o assistente rápido e fácil para publicar seus aplicativos locais e torná-los acessíveis remotamente.

## O que vem a seguir?
Você pode fazer muito mais com o Proxy de Aplicativo:


- [Publicar aplicativos usando seu próprio nome de domínio](https://msdn.microsoft.com/library/azure/mt210927.aspx)
- [Habilitar o logon único](https://msdn.microsoft.com/library/azure/dn879065.aspx)
- [Trabalho com aplicativos com reconhecimento de declaração](https://msdn.microsoft.com/library/azure/mt210926.aspx)
- [Habilitar o acesso condicional](https://msdn.microsoft.com/library/azure/dn931796.aspx)


### Saiba mais sobre o Proxy de Aplicativo
- [Veja nossa ajuda online](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [Confira o blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
- [Assista aos nossos vídeos no Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionais
* [Inscrever-se no Azure como uma organização](../sign-up-organization.md)
* [Identidade do Azure](../fundamentals-identity.md)

<!---HONumber=July15_HO3-->