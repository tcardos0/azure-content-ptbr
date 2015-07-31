<properties 
	pageTitle="Implantar um domínio do Active Directory altamente disponível com um modelo do Gerenciador de Recursos do Azure" 
	description="Implante facilmente dois servidores atuando como controladores de domínio do Active Directory com um modelo do Gerenciador de Recursos e o Portal de Visualização do Azure, Azure PowerShell ou a CLI do Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="josephd"/>


# Implantar um domínio do Active Directory altamente disponível com um modelo do Gerenciador de Recursos do Azure

Use as instruções neste artigo para implantar um domínio do Active Directory altamente disponível usando um modelo do Gerenciador de Recursos. Este modelo cria duas máquinas virtuais em uma nova rede virtual na mesma subrrede.

![](./media/virtual-machines-workload-template-ad-domain/two-server-ad.png)

Você pode executar o modelo com o Portal de Visualização do Azure, o Azure PowerShell ou a CLI do Azure.
 
## Portal de visualização do Azure

Para implantar essa carga de trabalho usando um modelo do Gerenciador de Recursos e o Portal de Visualização do Azure, clique [aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-ad-domain/azure-portal-template.png)
 
1.	Para o painel **Modelo**, clique em **Salvar**.
2.	Clique em **Parâmetros**. No painel **Parâmetros**, insira novos valores, selecione os valores permitidos ou aceite os valores padrão e, em seguida, clique em **OK**.
3.	Se necessário, clique em **Assinatura** e selecione a assinatura correta do Azure.
4.	Clique em **Grupo de recursos** e selecione um grupo de recursos existente. Como alternativa, clique em **Ou criar novo** para criar um novo para essa carga de trabalho.
5.	Se necessário, clique em **Local do grupo de recursos** e selecione o local correto do Azure.
6.	Se necessário, clique em Termos Legais para rever os termos e o contrato para usar o modelo.
7.	Clique em **Criar**.

Dependendo do modelo, pode levar algum tempo para o Azure criar a carga de trabalho. Quando a execução do modelo for concluída, você terá um novo domínio do Active Directory com dois servidores no seu grupo de recursos novo ou existente.

## PowerShell do Azure

Antes de começar, verifique se você tem a versão correta do Azure PowerShell instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter mais detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Preencha o nome de uma implantação do Azure, um novo nome do Grupo de Recursos e um local de datacenter do Azure no seguinte conjunto de comandos. Remova tudo entre aspas, incluindo os caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aqui está um exemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Em seguida, execute o bloco de comando no prompt do Azure PowerShell.

Ao executar o comando **New-AzureResourceGroupDeployment**, você será solicitado a fornecer os valores para uma série de parâmetros. Quando você tiver especificado todos os valores de parâmetro, o **New-AzureResourceGroupDeployment** criará e configurará as máquinas virtuais.

Quando a execução do modelo for concluída, você terá uma nova configuração de domínio do Active Directory com dois servidores no seu grupo de recursos novo.

## CLI do Azure

Antes de começar, verifique se você tem a versão correta do Azure CLI instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter mais detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Primeiro, crie um novo grupo de recursos. Use o seguinte comando e especifique o nome do grupo e o local do datacenter do Azure no qual você deseja implantar.

	azure group create <group name> <location>

Em seguida, use o seguinte comando e especifique o nome do novo grupo de recursos e o nome de uma implantação do Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json <group name> <deployment name>

Aqui está um exemplo.

	azure group create adtestbed eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json adtestbed wldevtest

Ao executar o comando **azure group deployment create**, você será solicitado a fornecer os valores para uma série de parâmetros. Quando você tiver especificado todos os valores de parâmetro, o Azure criará e configurará as máquinas virtuais.

Quando a execução do modelo estiver concluída, você terá uma nova configuração de domínio do Active Directory com dois servidores no seu grupo de recursos novo.


## Recursos adicionais

[Implantar e gerenciar máquinas virtuais usando modelos de Gerenciador de Recursos e o PowerShell do Azure](virtual-machines-deploy-rmtemplates-powershell.md)

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)

[Implantar e gerenciar Máquinas Virtuais usando modelos de Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)

<!---HONumber=July15_HO2-->