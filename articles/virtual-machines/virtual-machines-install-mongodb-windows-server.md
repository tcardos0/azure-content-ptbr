<properties 
	pageTitle="Instalar o MongoDB em uma máquina virtual que conta com o Windows Server" 
	description="Saiba como instalar o MongoDB em uma máquina virtual do Azure que executa o Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="kathydav"/>

#Instalar o MongoDB em uma máquina virtual na qual o Windows Server está em execução

[O MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Usando o [Portal de Gerenciamento Azure][AzureManagementPortal], você pode criar uma máquina virtual que executa o Windows Server da galeria de imagens. Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Este artigo aborda como:

- Usar o Portal de Gerenciamento para criar uma máquina virtual do Windows Server por meio da galeria
- Conectar-se à máquina virtual usando a Área de trabalho Remota
- Anexar um disco de dados à máquina virtual
- Instalar o MongoDB na máquina virtual

## Criar uma máquina virtual na qual o Windows Server está em execução

Seguindo as instruções gerais, você pode modificá-las criando um ponto de extremidade para permitir que o MongoDB seja acessado remotamente. (Também é possível criá-lo depois, descrito após as instruções de instalação do MongoDB.) Na última página do assistente, adicione um ponto de extremidade e o configure assim:

- Chamá-lo de **Mongo**
- Usar **TCP** como protocolo
- Defina as portas públicas e privadas como **27017**.
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Anexar um disco de dados
Para fornecer um armazenamento para a máquina virtual, anexe um disco de dados e inicialize-o de forma que o Windows possa usá-lo. É possível anexar um disco existente caso você já tenha dados que deseja usar ou anexar um disco vazio.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre como inicializar o disco, confira "Como inicializar um novo disco de dados no Windows Server" em [Como anexar um disco de dados a uma Máquina Virtual do Windows](storage-windows-attach-disk.md).

## Instalar e executar o MongoDB na máquina virtual 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Resumo
Neste tutorial você aprendeu a criar uma Máquina Virtual do Windows Server e a se conectar remotamente a ela, além de anexar um disco de dados. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual Windows. Para obter mais informações sobre o MongoDB, consulte [Documentação do MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com
 

<!---HONumber=58_postMigration-->