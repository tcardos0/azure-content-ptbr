<properties
   pageTitle="Aplicativo de Exemplo para Uso com Ambientes de Limite de Segurança | Microsoft Azure"
   description="Implantar esse aplicativo Web simples depois de criar um DMZ para testar cenários de fluxo de tráfego"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor"/>

# Aplicativo de Exemplo para Uso com Ambientes de Limite de Segurança

[Voltar à página de Práticas Recomendadas de Limite de Segurança][HOME]

Esses scripts do PowerShell podem ser executados localmente nos servidores IIS01 e AppVM01 para instalar e configurar um aplicativo Web muito simples que exibe uma página HTML do servidor front-end IIS01 com o conteúdo do servidor back-end AppVM01.

Esse aplicativo fornece um ambiente de teste simples para diversos dos Exemplos DMZ e como as alterações às regras de Pontos de Extremidade, NSGs, UDR e Firewall podem afetar os fluxos de tráfego.

## Regra de Firewall para Permitir ICMP
Essa declaração simples do PowerShell pode ser executada em qualquer VM do Windows para permitir o tráfego ICMP (Ping). Isso permitirá testes e solução de problemas mais fáceis ao permitir que o protocolo ping passe por meio do Firewall do Windows (na maioria das distribuições do Linux, o ICMP é ativado por padrão).

	# Turn On ICMPv4
	New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
		-Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Observação:** se você usar os scripts abaixo, esta adição de regra de firewall é a primeira instrução.

## IIS01 - Script de Instalação do Aplicativo Web
Esse script fará o seguinte:

1.	Abrirá o IMCPv4 (Ping) no Firewall do Windows do servidor local para testes mais fáceis
2.	Instalará o IIS e o .Net Framework v4.5
3.	Criará uma página da Web ASP.NET e um arquivo de Web.config
4.	Alterará o pool de aplicativos Padrão para facilitar o acesso aos arquivos
5.	Definirá o usuário Anônimo para sua conta e senha de administrador

Esse script do PowerShell deve ser executado localmente com RDP em IIS01.

	# IIS Server Post Build Config Script
	# Get Admin Account and Password
		Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
		$theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
		$thePassword = Read-Host -Prompt "The Admin Password"
		
	# Turn On ICMPv4
		Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
		New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
		
	# Install IIS
		Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
		add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
		
	# Create Web App Pages
		Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
		$MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
		<%@ Import Namespace="System.IO" %>
		<script language="vb" runat="server">
			Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
			    Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
			    Dim objStreamReader As StreamReader
			    objStreamReader = File.OpenText(FILENAME)
			    Dim contents As String = objStreamReader.ReadToEnd()
			    lblOutput.Text = contents
			    objStreamReader.Close()
		        lblTime.Text = Now()
			End Sub
		</script>
			
		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head runat="server">
			<title>DMZ Example App</title>
		</head>
		<body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
		  <form id="frmMain" runat="server">
		    <div>
		      <h1>Looks like you made it!</h1>
		      This is a page from the inside (a web server on a private network),<br />
		      and it is making its way to the outside! (If you are viewing this from the internet)<br />
		      <br />
		      The following sections show:
		      <ul style="margin-top: 0px;">
		        <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
		        <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
		        <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
		      </ul>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>Image File Linked from the Internet</b>:<br />
		        <br />
		        <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
		    </div>
		  </form>
		</body>
		</html>'
		
		$WebConfig ='<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		  <system.web>
		    <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
		    <httpRuntime targetFramework="4.5" />
		    <identity impersonate="true" />
		    <customErrors mode="Off"/>
		  </system.web>
		  <system.webServer>
		    <defaultDocument>
		      <files>
		        <add value="Home.aspx" />
		      </files>
		    </defaultDocument>
		  </system.webServer>
		</configuration>'
			
		$MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
		$WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
	
	# Set App Pool to Clasic Pipeline to remote file access will work easier
		Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
		c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
		c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
		
	# Make sure the IIS settings take
		Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
		Restart-Service -Name W3SVC
		
		Write-Host
		Write-Host "Web App Creation Successfull!" -ForegroundColor Green
		Write-Host


## AppVM01 - Script de Instalação do Servidor de Arquivos
Esse script configura o back-end para esse aplicativo simples. Esse script fará o seguinte:

1.	Abrirá o IMCPv4 (Ping) no firewall para testes mais fáceis
2.	Criará um novo diretório
3.	Criará um arquivo de texto para ser acessado remotamente pela página da Web acima
4.	Definirá permissões no diretório e no arquivo como Anônimo para permitir o acesso
5.	Desativará a Segurança Aprimorada do IE para permitir a navegação mais fácil por meio deste servidor 

>[AZURE.IMPORTANT]**Práticas recomendadas**: nunca desative a Segurança Aprimorada do IE em um servidor de produção, além disso, costuma não ser boa ideia surfar na web por meio de um servidor de produção. Além disso, abrir compartilhamentos de arquivos para acesso anônimo é uma ideia ruim, mas que foi executado aqui para simplificar.

Esse script do PowerShell deve ser executado localmente com RDP em AppVM01. O PowerShell deve ser executado como Administrador para garantir a execução bem-sucedida.
	
	# AppVM01 Server Post Build Config Script
	# PowerShell must be run as Administrator for Net Share commands to work
	
	# Turn On ICMPv4
	    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
	
	# Create Directory
	    New-Item "C:\WebShare" -ItemType Directory
	
	# Write out Rand.txt
	    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
	    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
	
	# Set Permissions on share
	    $Acl = Get-Acl "C:\WebShare"
	    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
	    $Acl.SetAccessRule($AccessRule)
	    Set-Acl "C:\WebShare" $Acl
	
	# Create network share
	    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
	
	# Turn Off IE Enhanced Security Configuration for Admins
	    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
	
	    Write-Host
	    Write-Host "File Server Setup Successfull!" -ForegroundColor Green
	    Write-Host
	

## DNS01 - Script de Instalação do Servidor DNS
Não há nenhum script incluído neste aplicativo de exemplo para configurar o servidor DNS. Se testar as regras de firewall, o NSG ou UDR precisa incluir o tráfego DNS, o servidor DNS01 precisará ser configurado manualmente. O arquivo xml de Configuração de Rede para os dois exemplos inclui DNS01 como o servidor DNS primário e o servidor DNS público hospedado pelo Nível 3 como o servidor DNS de backup. O servidor DNS do Nível 3 seria o servidor DNS real usado para tráfego não local e com DNS01 não configurado, não ocorreria nenhum DNS local.

<!--Link References-->
[HOME]: ../best-practices-network-security.md

<!---HONumber=Oct15_HO1-->