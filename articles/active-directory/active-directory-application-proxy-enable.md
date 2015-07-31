<properties
	pageTitle="Habilitando o Proxy de Aplicativo do AD do Azure."
	description="Aborda como colocar em funcionamento com o Proxy de aplicativo do Azure AD."
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

# Habilitando o Proxy de Aplicativo do AD do Azure.
> [AZURE.NOTE]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn532272.aspx).

O proxy de aplicativo do Microsoft Azure AD permite que você publique aplicativos, como sites do SharePoint, o Outlook Web Access e aplicativos baseados no IIS, na sua rede privada e fornece acesso seguro aos usuários fora da sua rede. Funcionários podem fazer logon em seus aplicativos de casa, em seus próprios dispositivos e realizar a autenticação por meio desse proxy baseado em nuvem.

Esta seção percorre a habilitação do o Proxy de aplicativo do Microsoft Azure AD para seu diretório de nuvem no Azure AD, a instalação do conector do Proxy de aplicativo na sua rede privada, o registro do conector com sua assinatura de locatário do AD do Microsoft Azure.

##Pré-requisitos de Proxy de aplicativo
Antes de habilitar e usar os serviços de Proxy de aplicativo, você precisa ter:

- Uma conta de administrador do Microsoft Azure. Se você não tiver um, pode obter um aqui.
- Um servidor executando o Windows Server 2012 R2 ou Windows 8.1 ou superior nos quais você pode instalar o conector do Proxy de aplicativo. O servidor deve poder enviar solicitações HTTPS para os serviços de Proxy de aplicativo na nuvem e ter uma conexão HTTPS para os aplicativos que você deseja publicar. 
- Se um firewall estiver colocado no caminho, certifique-se de que o firewall esteja aberto para permitir solicitações de HTTPS (TCP) que originadas do conector para o Proxy do aplicativo. O conector usa essas portas com subdomínios que fazem parte do domínio de alto nível: msappproxy.net. Não esqueça de abrir **todas as**portas a seguir como**saída**tráfego:

Número da porta | Descrição
--- | ---
80 | Para habilitar o tráfego HTTP de saída para a validação de segurança.
443 | Para habilitar a autenticação de usuário no Azure AD (necessário somente para o processo de registro do conector)
10100 - 10120 | Para habilitar respostas HTTP LOB enviadas de volta para o proxy
9352, 5671 | Para habilitar a comunicação entre o conector de serviço do Azure para solicitações de entrada.
9350 | Opcional. Para habilitar o melhor desempenho para solicitações de entrada.
8080 | Para habilitar a sequência de inicialização do conector e habilitar a atualização automática do conector
9090 | Para ativar o registro do conector (necessário somente para o processo de registro do conector)
9091 | Para ativar a renovação automática de certificados de confiança de conector
 
Se o firewall reforça o tráfego de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede. Além disso, certifique-se de habilitar a porta 8080 para Autoridade NT\\Sistema.


##Etapa 1: habilite o Proxy de aplicativo no AD do Azure
1. Entre como administrador no Portal de gerenciamento do Azure.
2. Vá para o Active Directory e selecione o diretório no qual você deseja habilitar o Proxy de aplicativo.
3. Clique em configurar, role para baixo até o Proxy de aplicativo e alterne para Habilitar serviços Proxy do aplicativo para ter esse diretório como habilitado.

	![Habilitar Proxy de aplicativo](http://i.imgur.com/87woFzq.png) <p>
4. Clique em Baixar agora na parte inferior da tela. Você será levado à página de download. Leia e aceite os termos de licença e clique no botão Download para salvar o arquivo do Windows Installer (.exe) para o conector de Proxy de aplicativo. 

##Etapa 2: Instalar e registrar o conector
1. Execute AADApplicationProxyConnectorInstaller.exe no servidor que você preparou (consulte os pré-requisitos do Proxy de aplicativo).
2. Siga as instruções no Assistente para instalar.
3. Durante a instalação, você será solicitado a registrar o conector com sua conta de Proxy do aplicativo ativa.
<p>Forneça suas credenciais de administrador global do AD do Azure. -Certifique-se de que o administrador que registra o conector está no mesmo diretório em que você ativou o serviço Proxy de aplicativo, por exemplo se o domínio de locatário for contoso.com, o administrador deve ser admin@contoso.comou qualquer outro alias no domínio. E se você for um administrador global do locatário do AD do Azure. Seu locatário de administrador global pode ser diferente das suas credenciais do Microsoft Azure. -Se a configuração de segurança reforçada do Internet Explorer estiver ativada no servidor onde você está instalando o conector do AD do Azure, a tela de registro pode estar bloqueada. Se isso acontecer, siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a segurança reforçada do Internet Explorer está desativado. -Se o registro do conector não for bem-sucedido, consulte Solucionar problemas de Proxy de aplicativo.

4. Quando a instalação for concluída, dois novos serviços são adicionados ao seu servidor, conforme mostrado abaixo. Esses são o serviço de conector, que permite a conectividade, e um serviço de atualização automatizada que verifica periodicamente novas versões do conector e atualiza o conector conforme necessário. Clique em Concluir na janela de instalação para concluir a instalação![Serviço de conector de Proxy de aplicativo](http://i.imgur.com/zsVJKOz.png)<p>
5. Agora você está pronto para publicar aplicativos com o Proxy de aplicativo.

Se você deseja desinstalar o conector, depois de desinstalar o serviço de conector e o serviço do atualizador, certifique-se de reiniciar o computador para remover completamente o serviço. <p>Para fins de alta disponibilidade, você deve implantar pelo menos um conector adicional. Para implantar um conector adicional, repita as etapas 2 e 3 acima. Cada conector deve ser registrado separadamente.



## Recursos adicionais

* [Inscrever-se no Azure como uma organização](..sign-up-organization.md)
* [Identidade do Azure](..fundamentals-identity.md)

<!---HONumber=July15_HO3-->