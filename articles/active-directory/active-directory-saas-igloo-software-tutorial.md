<properties pageTitle="Tutorial: integração do Active Directory do Azure ao Igloo Software | Microsoft Azure" description="Saiba como usar o Igloo Software com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao Igloo Software
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=526509).
  
O objetivo deste tutorial é mostrar a integração do Azure ao Igloo Software. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do [Igloo Software](http://www.igloosoftware.com/) com logon único atribuído
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Igloo Software poderão fazer logon único no aplicativo, em seu site de empresa da Igloo Software (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Igloo Software
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Cenário")
##Habilitando a integração de aplicativos com o Igloo Software
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Igloo Software.

###Para habilitar a integração de aplicativos com o Igloo Software, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Igloo Software**.

    ![Galeria de Aplicativos](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Igloo Software** e clique em **Concluir** para adicionar o aplicativo.

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no Igloo Software com a própria conta no Azure AD usando federação baseada no protocolo SAML. Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Desktop Central. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **Igloo Software**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Igloo Software**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Igloo Software**, digite sua URL usando o seguinte padrão "**https://company.igloocommunities.com/?signin*" e clique em **Próximo**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Igloo Software**, para baixar o certificado, clique em **Baixar certificado** e salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Igloo Software como administrador.

6.  Vá até o**Painel de Controle**.

    ![Painel de Controle](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Painel de Controle")

7.  Na guia **Associação**, clique em **Configurações de Entrada**.

    ![Configurações de Entrada](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Configurações de Entrada")

8.  Na seção de Configuração de SAML, clique em **Configurar a Autenticação SAML**.

    ![Configuração de SAML](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuração de SAML")

9.  Na seção **Configuração Geral**, execute as seguintes etapas:

    ![Configuração Geral](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuração Geral")

    1.  Na caixa de texto **Nome da Conexão**, digite um nome personalizado para sua configuração.
    2.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Igloo Software**, copie o valor da **URL de Logon Remoto** e cole-a na caixa de texto **URL de Logon do IdP**.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Igloo Software**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IdP**.
    4.  Para **Tipo de HTTP de Solicitação e Resposta de Logoff** selecione **POST**.
    5.  Crie um arquivo de texto do certificado baixado.
        
		>[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Remova a primeira linha (**) e a última linha (**) da versão do arquivo de texto do seu certificado, copie o texto restante do certificado e cole-o na caixa de texto **Certificado Público**.

10. Em **Configuração de Resposta e Autenticação**, execute as seguintes etapas:

    ![Configuração de Resposta e Autenticação](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuração de Resposta e Autenticação")

    1.  Para **Provedor de Identidade**, selecione **Microsoft ADFS**.
    2.  Para **Tipo de Identificador**, selecione **Endereço de Email**.
    3.  Na caixa de texto **Atributo de Email**, digite **emailaddress**.
    4.  Na caixa de texto **Atributo de Nome**, digite **givenname**.
    5.  Na caixa de texto **Atributo de Sobrenome**, digite **surname**.

11. Execute as seguintes etapas para concluir a configuração:

    ![Criação de usuário na entrada](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Criação de usuário na entrada")

    1.  Para **Criação de usuário na entrada**, selecione**Criar um novo usuário em seu site ao entrarem**.
    2.  Para **Configurações de Entrada**, selecione **Usar botão SAML na tela "Entrar"**.
    3.  Clique em **Salvar**.

12. No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há item de ação para a configuração do provisionamento de usuário para o Igloo Software. Quando um usuário atribuído tenta fazer logon no Igloo Software usando o painel de acesso, o Igloo Software verifica se o usuário existe. Se ainda não houver conta de usuário, ela será criada automaticamente pelo Igloo Software.
##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Igloo Software, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Igloo Software**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->