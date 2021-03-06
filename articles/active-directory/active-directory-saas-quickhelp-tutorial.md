<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o QuickHelp | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o QuickHelp."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o QuickHelp

O objetivo deste tutorial é mostrar a você como integrar o QuickHelp ao Active Directory do Azure (AD do Azure).<br>A integração do QuickHelp com o AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao QuickHelp 
- Você pode habilitar seus usuários a fazerem logon automaticamente no QuickHelp (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao QuickHelp, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do QuickHelp com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar QuickHelp a partir da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionar QuickHelp a partir da galeria
Para configurar a integração do QuickHelp ao AD do Azure, você precisa adicionar o QuickHelp a partir da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o QuickHelp a partir da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br>![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **QuickHelp**.<br><br> ![Aplicativos][5]<br>
7. No painel de resultados, selecione **QuickHelp** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Aplicativos][500]<br>


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o QuickHelp, com base em um usuário de teste chamado "Brenda Fernandes".


Para configurar e testar o logon único do AD do Azure com o QuickHelp, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usem esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do QuickHelp](#creating-a-quickhelp-test-user)**: para ter um equivalente de Brenda Fernandes no QuickHelp que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único no aplicativo do QuickHelp.<br>

**Para configurar o logon único do AD do Azure com o QuickHelp, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **QuickHelp**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no QuickHelp**, selecione **Logon Único do AD do Azure** e clique em **Avançar**.<br><br> ![Logon único do AD do Azure][7] <br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Definir configurações de aplicativo][8] <br>
 
     a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no site do QuickHelp (por exemplo: *https://quickhelp.com/bsiazure/*).

     >[AZURE.NOTE] Entre em contato com sua equipe de suporte do QuickHelp se você não souber o valor da URL do logon.

     b. Clique em **Próximo**.

 
4. Na página **Configurar logon único no QuickHelp**, execute as seguintes etapas: clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador. <br><br>![O que é o Azure AD Connect][9] <br>



1. Faça logon no site do QuickHelp da sua empresa como administrador.

2. No menu na parte superior, clique em **Administrador**. <br><br>![Configurar o logon único][21]<br>


1. No menu **Administrador do QuickHelp**, clique em **Configurações**. <br><br>![Configurar o logon único][22]<br>

1. Clique em **Configurações da Autenticação**.

1. Na página **Configurações de Autenticação**, execute as seguintes etapas <br><br>![Configurar o logon único][23]<br>

    a. Como **Tipo de SSO**, selecione **WSFederation**.

    b. Para carregar o arquivo de metadados do Azure baixado, clique em **Procurar**, navegue até o arquivo e clique em **Carregar Metadados**.

    c. Na caixa de texto **Email**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Na caixa de texto **Nome**, **digite http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Na caixa de texto **Sobrenome**, **digite http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. No **ação barra**, clique em **Salvar**.







6. No portal do AD do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. <br><br>![O que é o Azure AD Connect][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![O que é o Azure AD Connect][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação esquerdo, clique em **Active Directory**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)<br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) <br>

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png)<br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do QuickHelp

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no QuickHelp. Para que o logon único funcione, o AD do Azure precisa saber qual usuário do QuickHelp é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no QuickHelp.

O QuickHelp dá suporte ao provisionamento just-in-time. Isso significa que, se necessário, uma conta de usuário automaticamente é criada da ajuda rápida e a conta é vinculada à conta do AD do Azure.

Não há itens de ação para você nesta seção.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes para usar o logon único do Azure concedendo-lhe acesso ao QuickHelp.<br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao QuickHelp, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **QuickHelp**. <br><br>![Atribuir usuário][202] <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco QuickHelp no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo QuickHelp.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=AcomDC_0128_2016-->