<properties title="Error During Authentication Detection" pageTitle="Erro na Detecção da Autenticação" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

###Erro na Detecção da Autenticação 
Ao detectar o código de autenticação anterior, o assistente detectou um tipo de autenticação incompatível.   

###O que está sendo verificado?

####Tipos de projeto

O assistente verifica o tipo de projeto que você está desenvolvendo para que possa injetar a lógica de autenticação adequada ao projeto.  Se houver qualquer controlador que deriva de 'ApiController' no projeto, ele será considerado um projeto de API da Web.  Se houver apenas os controladores que derivam de `MVC.Controller` no projeto, será considerado um projeto MVC.  Qualquer outra coisa é considerada sem suporte pelo assistente.  Atualmente, não há suporte para projetos de formulários da Web.

#####Código de autenticação compatível

O assistente também verifica se as configurações de autenticação configuradas anteriormente com o assistente são compatíveis com ele.  Se todas as configurações estiverem presentes, ele é considerado um caso reentrante e o assistente será aberto e exibirá as configurações.  Se apenas algumas das configurações estiverem presentes, ele é considerado um caso de erro.

Em um projeto do MVC, o assistente verifica qualquer uma das configurações a seguir, fruto do uso anterior do assistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica qualquer uma das configurações a seguir em um projeto de API da Web, fruto do uso anterior do assistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

#####Código de autenticação incompatível

Por fim, o assistente tentou detectar versões do código de autenticação que foram configurados com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O assistente detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas Individuais de Usuário 
* Contas organizacionais 
 

Para detectar a Autenticação do Windows em um projeto MVC, o assistente procura o elemento `authentication` de seu arquivo **web.config**.

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
</pre>

Para detectar a Autenticação do Windows em um projeto da API da Web, o assistente procura o elemento `IISExpressWindowsAuthentication` do arquivo **.csproj**:

<PRE class="prettyprint">
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
</PRE>

Para detectar a autenticação de contas de usuário individual, o assistente procura o elemento de pacote de seu arquivo **Packages.config**.

<PRE class="prettyprint">
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
</PRE>

Para detectar uma forma anterior de Autenticação de conta organizacional, o assistente procura o seguinte elemento **web.config**:

<PRE class="prettyprint">
	&lt;configuration*gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
</PRE>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute o assistente novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](http://msdn.microsoft.com/library/azure/dn499820.aspx).