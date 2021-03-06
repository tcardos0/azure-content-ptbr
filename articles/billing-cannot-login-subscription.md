<properties
	pageTitle="Não consigo fazer logon para gerenciar minha assinatura do Azure | Microsoft Azure"
	description="Descreve as informações de solução de problemas para alguns problemas comuns de logon de assinatura do Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="genli"/>

# Não consigo fazer logon para gerenciar minha assinatura do Azure

Este artigo ajudará a solucionar algumas causas comuns de problemas de logon.

## Qual portal você está tentando acessar?

Um Proprietário da Conta pode acessar o [Centro de Contas](https://account.windowsazure.com/) somente se os Administradores de Serviço (SA) e Coadministradores (CA) tiverem acesso ao [Portal do Azure](https://manage.windowsazure.com/).

Clique nos links abaixo para obter instruções sobre como atualizar as funções de administrador:

- [Para atualizar o SA na sua conta](./billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)

- [Para adicionar um novo CA no portal de gerenciamento](./billing-add-change-azure-subscription-administrator.md#add-a-co-administrator-for-a-subscription)

## Sua assinatura está associada a uma conta da Microsoft ou Institucional?

Sua conta da Microsoft é o endereço de e-mail usado, juntamente com sua senha para entrar em qualquer programa do Windows Live ou serviço, como o Outlook, Hotmail, MSN ou OneDrive. Você pode configurar uma conta da Microsoft usando qualquer endereço de e-mail que pertence a você, incluindo o e-mail da sua empresa. Consulte [www.microsoft.com/account](http://www.microsoft.com/account) para obter mais detalhes.

Se sua conta estiver associada uma Conta institucional, selecione a opção de logon correta conforme mostrado abaixo. Para obter mais informações sobre como usar uma Conta institucional, consulte [Inscreva-se no Azure como uma instituição](./active-directory/sign-up-organization.md):

![página de entrada](./media/billing-cannot-login-subscription/signin.png)

## Coadministrador: Você está usando o tipo correto de conta para gerenciar outras contas?

- Se fizer logon com uma Conta da Microsoft, você poderá adicionar apenas outras Contas da Microsoft como Coadministradoras. Esse é um requisito de segurança para impedir que contas não organizacionais descubram se certas contas (por exemplo, janedoe@contoso.com) são contas válidas.
- Se estiver conectado a uma conta institucional, você pode adicionar outras contas institucionais em sua instituição como Coadministrador. Por exemplo, o abby@contoso.com pode adicionar o bob@contoso.com como Administrador de Serviços ou Coadministrador, mas não pode adicionar o john@notcontoso.com. Usuários conectados usando contas de instituição podem também adicionar usuários da conta da Microsoft com Administradores ou Coadministradores de serviço.

Agora que é possível fazer logon no Azure com uma conta institucional, aqui estão as alterações aos requisitos de conta de SA (Administrador de Serviço) e CA (Coadministrador):

| Método de logon| Adicionar Conta da Microsoft como Coadministrador ou Administrador de Serviços? |Adicionar uma conta organizacional na mesma organização como Coadministrador ou Administrador de Serviços? |Adicionar uma conta organizacional em uma organização diferente como Coadministrador ou Administrador de Serviços?
| ------------- | ------------- |---------------|---------------|
|Conta da Microsoft |Sim|Não|Não|
|Conta organizacional|Sim|Sim|Não|

## Tente excluir cache/cookies usando o modo de Navegação InPrivate do IE e também usando um navegador diferente

Se você precisar de mais ajuda a qualquer momento neste artigo, poderá contatar os especialistas do Azure nos [fóruns do Azure MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter Suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes do Suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_0128_2016-->