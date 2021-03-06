<properties
	pageTitle="Criar uma máquina virtual personalizada do Windows | Microsoft Azure"
	description="Saiba como criar uma máquina virtual personalizada do Windows no portal clássico do Azure usando o modelo de implantação clássico."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

	
# Criar uma máquina virtual personalizada executando Windows


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 


Uma máquina virtual *personalizada* é simplesmente uma máquina virtual que você cria usando a opção **Da Galeria** porque isso lhe dá mais opções de configuração do que a opção **Criação Rápida**. Essas opções incluem:

- Conectar a máquina virtual a uma rede virtual.
- Instalar o agente de máquina Virtual do Azure e extensões de máquina Virtual do Azure, como para antimalware.
- Adicionar a máquina virtual a serviços de nuvem existentes.
- Adicionar a máquina virtual a uma conta de armazenamento existente.
- Adicionar a máquina virtual a um conjunto de disponibilidade.

> [AZURE.IMPORTANT]Se você quiser que sua máquina virtual use uma rede virtual para que você possa se conectar a ela diretamente pelo nome do host ou estabelecer conexões entre locais, especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte a seção [Visão geral da rede virtual do Azure](virtual-networks-overview.md).


## Para criar a máquina virtual


[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=AcomDC_0121_2016-->