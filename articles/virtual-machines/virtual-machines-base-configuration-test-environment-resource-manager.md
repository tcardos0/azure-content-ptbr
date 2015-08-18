<properties
	pageTitle="Ambiente de teste Configuração de Base com o Gerenciador de Recursos do Azure"
	description="Aprenda a criar um ambiente de desenvolvimento/teste simples que simule uma intranet simplificada no Microsoft Azure usando o Gerenciador de Recursos."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Ambiente de teste Configuração de Base com o Gerenciador de Recursos do Azure

Este artigo apresenta instruções passo a passo para criar o ambiente de teste Configuração de Base em uma Rede Virtual do Microsoft Azure usando máquinas virtuais criadas no Gerenciador de Recursos.

Você pode usar o ambiente de teste resultante:

- Para testes e desenvolvimento de aplicativos.
- Como a configuração inicial de um ambiente de teste estendido do seu próprio design que inclua máquinas virtuais adicionais e serviços do Azure.

O ambiente de teste Configuração de Base consiste na sub-rede Corpnet em uma rede virtual do Azure somente de nuvem denominada TestLab, que simula uma intranet simplificada e privada conectada à Internet.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Ele contém:

- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada DC1, que é configurada como um controlador de domínio de intranet e um servidor DNS (Sistema de Nomes de Domínio).
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada APP1, que é configurada como um servidor Web e de aplicativos geral.
- Uma máquina virtual do Azure que executa o Windows Server 2012 R2, denominada CLIENT1, que atua como um cliente de intranet.

Essa configuração permite que os computadores DC1, APP1, CLIENT1 e computadores adicionais da sub-rede Corpnet sejam:

- Conectados à Internet para instalar atualizações, acessar recursos da Internet em tempo real e participar de tecnologias de nuvem pública, como o Microsoft Office 365 e outros serviços do Azure.
- Gerenciados remotamente usando Conexões de Área de Trabalho Remota de seu computador que está conectado à Internet ou à rede de sua organização.

Há quatro fases para configurar a sub-rede Corpnet do ambiente de teste de Configuração de Base do Windows Server 2012 R2 no Azure.

1.	Crie a rede virtual.
2.	Configurar o DC1.
3.	Configurar o APP1.
4.	Configurar o CLIENT1.

Se ainda não tiver uma conta do Azure, você poderá se inscrever para obter uma avaliação gratuita em [Experimentar o Azure](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]As máquinas virtuais no Azure incorrem em um custo monetário contínuo quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Para obter mais informações sobre os custos da execução de máquinas virtuais do Azure, consulte [Detalhes de preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Calculadora de preços do Azure](http://azure.microsoft.com/pricing/calculator/). Para reduzir os custos, consulte [Minimizando os custos de máquinas de virtuais do ambiente de teste no Azure](#costs).

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Ambiente de teste Configuração de Base](virtual-machines-base-configuration-test-environment.md)


## Fase 1: Criar a rede virtual

Em primeiro lugar, se necessário, use as instruções em [Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md) para instalar o Azure PowerShell no computador local. Abra um prompt do Azure PowerShell.

Em seguida, selecione a assinatura correta do Azure com estes comandos. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelo nome correto.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Você pode obter o nome da assinatura na propriedade SubscriptionName da exibição do comando **Get-AzureSubscription**.

Em seguida, alterne o PowerShell do Azure para o modo do Gerenciador de Recursos.

	Switch-AzureMode AzureResourceManager 

Depois crie um novo grupo de recursos para seu laboratório de teste Configuração de Base. Para determinar um nome exclusivo de grupo de recursos, use este comando para listar os grupos de recursos existentes.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Para listar os locais do Azure nos quais é possível criar máquinas virtuais baseadas no Gerenciador de Recursos, use estes comandos.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Crie seu novo grupo de recursos com estes comandos. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Máquinas virtuais baseadas no Gerenciador de Recursos exigem uma conta de armazenamento baseada no Gerenciador de Recursos. Você deve escolher um nome global exclusivo para sua conta de armazenamento que contenha apenas letras minúsculas e números. Você pode usar este comando para listar as contas de armazenamento existentes.

	Get-AzureStorageAccount | Sort Name | Select Name

Para testar se um nome de conta de armazenamento escolhido é globalmente exclusivo, execute o comando **Test-AzureName** no modo de Gerenciamento de Serviço do Azure do PowerShell. Use estes comandos.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Se o comando Test-AzureName exibir **False**, o nome proposto é exclusivo. Após encontrar um nome exclusivo, retorne o PowerShell do Azure de volta ao modo do Gerenciador de Recursos usando este comando.

	Switch-AzureMode AzureResourceManager 

Crie uma nova conta de armazenamento para o novo ambiente de teste com estes comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Em seguida, crie a Rede Virtual TestLab do Azure que hospedará a sub-rede Corpnet da configuração de base.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## Fase 2: configurar o DC1

DC1 é um controlador de domínio para o domínio corp.contoso.com do AD DS (Serviços de Domínio do Active Directory) e um servidor DNS para as máquinas virtuais da rede virtual TestLab.

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para DC1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual DC1.

1.	No Portal de Visualização do Azure, clique em **Procurar Tudo** no painel esquerdo, clique em **Máquinas virtuais** na lista **Procurar** e clique na máquina virtual **DC1**.  
2.	No painel **DC1**, clique em **Conectar**.
3.	Quando solicitado, abra o arquivo DC1.rdp baixado.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **DC1\**[Nome da conta do administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, adicione um disco de dados extra como um novo volume com a letra da unidade F:.

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6.	Na página Especifique o tamanho do volume, clique em **Avançar**.
7.	Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8.	Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9.	Na página Confirmar seleções, clique em **Criar**.
10.	Ao concluir, clique em **Fechar**.

Em seguida, configure DC1 como um controlador de domínio e servidor DNS para o domínio corp.contoso.com. Execute estes comandos em um prompt de comando do Windows PowerShell de nível de administrador.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Após a reinicialização de DC1, reconecte-se à máquina virtual DC1.

1.	No Portal de Visualização do Azure, clique em Procurar Tudo no painel esquerdo, clique em Máquinas virtuais na lista Procurar e clique na máquina virtual DC1.
2.	No painel DC1, clique em Conectar.
3.	Quando solicitado a abrir DC1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **CORP\**[Nome da conta do administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, crie uma conta de usuário no Active Directory que será usada ao fazer logon em computadores membros do domínio CORP. Execute estes comandos, um de cada vez, em um prompt de comando do Windows PowerShell com nível de administrador.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Observe que o primeiro comando resulta em um prompt para fornecer a senha da conta User1. Como essa conta será usada para conexões de área de trabalho remota para todos os computadores membros do domínio CORP, escolha uma senha forte. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registre a senha da conta User1 e armazene-a em um local seguro.

Feche a sessão Área de Trabalho Remota com DC1 e reconecte usando a conta CORP\\User1.

Em seguida, para permitir o tráfego para a ferramenta Ping, execute este comando em um prompt de comando do Windows PowerShell com nível de administrador.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## Fase 3: configurar o APP1

O APP1 fornece serviços Web e de compartilhamento de arquivos.

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para APP1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual APP1 usando o nome e a senha da conta do administrador local da APP1 e, em seguida, abra um prompt de comando do Windows PowerShell no nível de administrador.

Para verificar a comunicação de rede e a resolução de nomes entre APP1 e DC1, execute o comando **ping dc1.corp.contoso.com** e verifique se há quatro respostas.

Em seguida, integre a máquina virtual APP1 ao domínio CORP com estes comandos no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio CORP\\User1 depois de inserir o comando Add-Computer.

Depois de reiniciar APP1, conecte-se a ela usando o nome e a senha da conta CORP\\User1 e abra um prompt de comando do Windows PowerShell no nível de administrador.

Em seguida, torne APP1 um servidor Web com este comando no prompt de comando do Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Crie uma pasta compartilhada e um arquivo de texto dentro da pasta em APP1 com estes comandos.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Esta é a configuração atual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## Fase 4: configurar o CLIENT1

CLIENT1 atua como um tablet, computador laptop ou desktop típico na intranet da Contoso.

Em primeiro lugar, preencha o nome do grupo de recursos, o local do Azure, o nome da conta de armazenamento e execute estes comandos no prompt de comando do Azure PowerShell no computador local para criar uma Máquina Virtual do Azure para CLIENT1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à máquina virtual CLIENT1 usando o nome e a senha da conta do administrador local CLIENT1 e, em seguida, abra um prompt de comando do Windows PowerShell no nível de administrador.

Para verificar a comunicação de rede e a resolução de nomes entre CLIENT1 e DC1, execute o comando **ping dc1.corp.contoso.com** em um prompt de comando do Windows PowerShell e verifique se há quatro respostas.

Em seguida, integre a máquina virtual CLIENT1 ao domínio CORP com estes comando no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio CORP\\User1 depois de inserir o comando Add-Computer.

Depois de reiniciar CLIENT1, conecte-se a ela usando o nome e a senha da conta CORP\\User1 e abra um prompt de comando do Windows PowerShell no nível de administrador.

Verifique se que você pode acessar recursos Web e de compartilhamento de arquivos em APP1 por meio de CLIENT1.

1.	No Gerenciador de Servidores, no painel de árvore, clique em **Servidor Local**.
2.	Em **Propriedades para CLIENT1**, clique em **Ativado** ao lado de **Configuração de Segurança Aprimorada do IE**.
3.	Em **Configuração de Segurança Aprimorada do Internet Explorer**, clique em **Desativado** para **Administradores** e **Usuários** e, em seguida, clique em **OK**.
4.	Na tela Inicial, clique em **Internet Explorer** e em **OK**.
5.	Na barra de endereços, digite ****http://app1.corp.contoso.com/** e pressione ENTER. Você verá a página da Web de Serviços de Informações da Internet padrão para APP1.
6.	Na barra de tarefas da área de trabalho, clique no ícone do Gerenciador de Arquivos.
7.	Na barra de endereços, digite **\\\\app1\\Files** e pressione ENTER.
8.	Você deverá ver uma janela de pasta com o conteúdo da pasta compartilhada Arquivos.
9.	Na janela de pasta compartilhada **Arquivos**, clique duas vezes no arquivo **Example.txt**. Você deverá ver o conteúdo do arquivo Example.txt.
10.	Feche as janelas de **example.txt - Bloco de Notas** e da pasta compartilhada **Arquivos**.

Essa é a configuração final.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

A configuração básica no Azure agora está pronta para desenvolvimento e teste de aplicativos ou para ambientes de teste adicionais.

## Recursos adicionais

[Ambientes de teste de nuvem híbrida](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Ambiente de teste Configuração de Base](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Minimizando os custos de máquinas virtuais do ambiente de teste no Azure

Para minimizar o custo da execução de máquinas virtuais no ambiente de teste, você pode fazer o seguinte:

- Crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível. Ao concluir, exclua as máquinas virtuais do ambiente de teste.
- Desligue as máquinas virtuais do ambiente teste para um estado desalocado.

Para desligar as máquinas virtuais com o Azure PowerShell, preencha o nome do grupo de recursos e execute estes comandos.

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Para garantir que suas máquinas virtuais funcionem corretamente ao iniciar todas elas no estado Parado (Desalocado), você deve iniciá-las na seguinte ordem:

1.	DC1
2.	APP1
3.	CLIENT1

Para iniciar as máquinas virtuais na ordem com o Azure PowerShell, preencha o nome do grupo de recursos e execute estes comandos.

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=06-->