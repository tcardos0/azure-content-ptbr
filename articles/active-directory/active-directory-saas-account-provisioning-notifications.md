<properties
	pageTitle="Notificações de provisionamento de conta | Microsoft Azure"
	description="Saiba como garantir que você será notificado sobre problemas relacionados ao provisionamento de usuário que exigem sua atenção habilitando notificações de provisionamento de conta."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Notificações de provisionamento de conta

Com o provisionamento de usuário, você pode automatizar o processo de gerenciar usuários em aplicativos SaaS de terceiros. <br> Embora esse seja um processo automatizado, sua interação com esse processo às vezes é necessária. <br> É, por exemplo, o caso, quando a senha da conta que você configurou para trocar dados com um aplicativo SaaS de terceiros expirou.

Ao habilitar notificações de provisionamento de conta você pode garantir que será notificado sobre problemas relacionados ao provisionamento de usuário, que exigem sua atenção.

Você ativa ou desativa notificações de provisionamento de conta como parte de sua configuração de provisionamento de usuário de um aplicativo SaaS de terceiro.

![Provisionamento do usuário][1]



Para ativar notificações de provisionamento de conta, marque a caixa de seleção relacionada na página de diálogo **Confirmação** e digite o alias de email do destinatário.

![Notificações de provisionamento de conta][2]
 


Você pode inserir uma lista de distribuição como destinatário; no entanto, é importante observar que o email de notificação contém links para relatórios que só são acessíveis pelos administradores do AD do Azure.

Se você tiver notificações de provisionamento de conta habilitadas, receberá emails sobre problemas críticos relacionados ao provisionamento do usuário. No entanto, para evitar uma sobrecarga de emails, você só receberá um email de notificação por dia para cada aplicativo SaaS para o qual o email de notificação está habilitado.


##Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=AcomDC_0211_2016-->