<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o SciQuest Spend Director | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o SciQuest Spend Director."
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
	ms.date="01/05/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o SciQuest Spend Director

O objetivo deste tutorial é mostrar a você como integrar o SciQuest Spend Director com o Active Directory do Azure (Azure AD).<br>A integração do SciQuest Spend Director com o Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao SciQuest Spend Director 
- Você pode habilitar seus usuários a fazerem logon automaticamente no SciQuest Spend Director (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do Azure AD com o SciQuest Spend Director, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do SciQuest Spend Director com o logon único habilitado


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do SciQuest Spend Director por meio da galeria 
2. Configurar e testar o logon único do Azure AD


## Adição do SciQuest Spend Director por meio da galeria
Para configurar a integração do SciQuest Spend Director com o Azure AD, você precisa adicionar o SciQuest Spend Director, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o SciQuest Spend Director por meio da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.<br><br> ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo por meio da galeria**.<br><br> ![Aplicativos][4]
6. Na caixa de pesquisa, digite **sciQuest spend director**.<br>![Aplicativos][5]
7. No painel de resultados, selecione **SciQuest Spend Director** e clique em **Concluir** para adicionar o aplicativo.<br> ![Aplicativos][6]


##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o SciQuest Spend Director, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SciQuest Spend Director é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do SciQuest Spend Director.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor do **Nome de usuário** no SciQuest Spend Director.
 
Para configurar e testar o logon único do Azure AD com o SciQuest Spend Director, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para permitir que os usuários usem esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do SciQuest Spend Director](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no SciQuest Spend Director vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do SciQuest Spend Director.<br>

**Para configurar o logon único do Azure AD com o SciQuest Spend Director, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração de aplicativos do **SciQuest Spend Director**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.<br><br>![Configurar o logon único][8]

2. Na página **Como você deseja que os usuários façam logon no SciQuest Spend Director**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br> ![Logon único do AD do Azure][9]

3. Na página de caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Definir configurações de aplicativo][10]
 
     3\.1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo SciQuest Spend Director usando o seguinte padrão: **https://.*sciquest.com/.**

     3\.2. Na caixa de texto **URL de Resposta**, digite o mesmo valor que você digitou na caixa de texto **URL de Logon**.

     3\.3. Clique em **Próximo**.
 
4. Na página **Configurar logon único no SciQuest Spend Director**, clique em **Baixar metadados** e salve o arquivo de metadados localmente em seu computador.<br><br>![O que é o Azure AD Connect][11]

5. Entre em contato com o suporte da SciQuest para habilitar esse método de autenticação usando os metadados baixado acima.

6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.<br><br>![O que é o Azure AD Connect][15]
10. Na página **Confirmação de logon único**, clique em **Concluir**.<br><br>![O que é o Azure AD Connect][16]




### Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br>![O que é o Azure AD Connect][100] 
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br>![O que é o Azure AD Connect][101] 
4. Para abrir a caixa de diálogo **Adicionar usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.<br><br>![O que é o Azure AD Connect][102] 
5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:<br><br>![O que é o Azure AD Connect][103] 
  1. Em **Tipo de usuário**, selecione **Novo usuário na organização**.
  2. Na **caixa de texto** de Nome de usuário, digite **BrittaSimon**.
  3. Clique em Avançar.
6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:<br><br>![O que é o Azure AD Connect][104] 
  1. Na caixa de texto **Nome**, digite **Britta**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br>![O que é o Azure AD Connect][105]  
8. Na página de caixa de diálogo **Obter senha temporária** execute as seguintes etapas:<br><br>![O que é o Azure AD Connect][106]   
  1. Anote o valor da **Nova senha**.
  2. Clique em **Concluído**.   
  
 
### Criação de um usuário de teste do SciQuest Spend Director

O objetivo desta seção é criar um usuário chamado Britta Simon no SciQuest Spend Director.

Você precisa entrar em contato com a equipe de suporte do SciQuest Spend Director e fornecer a eles os detalhes da sua conta de teste para que ela seja criada.

Como alternativa, você também pode usar o provisionamento just-in-time, um recurso de logon único com suporte do SciQuest Spend Director. <br> Se o provisionamento just-in-time estiver habilitado, os usuários serão automaticamente criados pelo SciQuest Spend Director durante uma tentativa de logon único caso não existam. Este recurso elimina a necessidade de criar manualmente os usuários correspondentes ao logon único.

Para habilitar o provisionamento just-in-time, você precisa entrar em contato com a equipe de suporte do SciQuest Spend Director.
  

### Atribuição do usuário de teste do Azure AD

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao SciQuest Spend Director.<br><br>![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon ao SciQuest Spend Director, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.<br> <br><br>![O que é o Azure AD Connect][201]
2. Na lista de aplicativos, selecione **SciQuest Spend Director**. <br><br>![O que é o Azure AD Connect][202]
1. No menu na parte superior, clique em **Usuários**.<br> <br><br>![O que é o Azure AD Connect][203]
1. Na lista de Usuários, selecione **Britta Simon**. <br><br>![O que é o Azure AD Connect][204]
2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![O que é o Azure AD Connect][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando clica no bloco SciQuest Spend Director no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo SciQuest Spend Director.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=AcomDC_0107_2016-->