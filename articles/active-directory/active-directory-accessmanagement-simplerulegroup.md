<properties
	pageTitle="Criação de uma regra simples para configurar associações dinâmicas para um grupo | Microsoft Azure"
	description="Explica como criar uma regra simples para configurar associações dinâmicas para um grupo"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Criação de uma regra simples para configurar associações dinâmicas para um grupo

Para habilitar a associação dinâmica de um determinado grupo, execute as seguintes etapas:

1. No portal do Azure, na guia **Grupos**, selecione o grupo que deseja editar e, na guia **Configurar** esse grupo, defina a opção **Habilitar Associações Dinâmicas** como **Sim**.

2. Agora você pode configurar uma única regra simples para o grupo que controlará a associação dinâmica como para esse grupo de funções. Verifique se a opção **Adicionar usuários em que** está marcada e selecione uma propriedade de usuário na lista (por exemplo, departamento, jobTitle, etc.)

3. Em seguida, selecione uma condição (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) e finalmente especificar um valor para a propriedade do usuário selecionado. Por exemplo, se um grupo for atribuído a um aplicativo SaaS e você habilitar associações dinâmicas a este grupo definindo uma regra na qual **Adicionar usuários em que** é definido como o jobTitle that Equals(-eq)Sales Rep, todos os usuários no diretório do AD do Azure cujos cargos forem definidos como Representante de Vendas terão acesso a esse aplicativo SaaS.

4. Observe que você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office. Associações dinâmicas de grupos requerem uma licença AD Premium do Azure.

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->