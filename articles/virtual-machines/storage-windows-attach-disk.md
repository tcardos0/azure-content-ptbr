<properties
	pageTitle="Anexar um disco a uma VM | Microsoft Azure"
	description="Anexe um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássica e inicialize-a."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>

# Anexe um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássico

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-attach-disk-preview.md).

Se você precisa de um disco de dados adicional, é possível anexar um disco vazio ou um disco existente com dados a uma VM. Em ambos os casos, os discos são arquivos .vhd que ficam em uma conta de armazenamento Azure. No caso de um novo disco, depois que você anexar o disco, você também precisará inicializá-lo para que esteja pronto para ser usado por uma VM do Windows.

É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual do Azure, ela contém um disco para o sistema operacional mapeado para a unidade C e um disco temporário mapeado para a unidade D. **Não use a unidade D para armazenar dados.** Como o nome sugere, o disco temporário fornece apenas armazenamento temporário. Não oferece redundância nem backup porque não reside no armazenamento do Azure.

## Passo a passo em vídeo

Veja um passo a passo deste tutorial.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Como: inicializar um novo disco de dados no Windows Server

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Windows Server][logon].

2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Abra o gerenciador de servidor.](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Expanda o menu e selecione **Discos**.

4. A seção **Discos** lista os discos. Na maioria dos casos, ele terá disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados que você acabou de anexar à VM. O novo disco de dados listará a Partição como **desconhecida**. Clique com o botão direito do mouse no disco e selecione **Inicializar**.

5.	Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Depois de concluir, a Partição será listada como **GPT**. Clique com o botão direito do mouse no disco novamente e selecione **Novo Volume**.

6.	Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.

	![Volume inicializado com êxito](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] O tamanho da VM determina quantos discos você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).

## Recursos adicionais

[Como desanexar um disco de uma máquina virtual Windows](storage-windows-detach-disk.md)

[Sobre discos e VHDs para máquinas virtuais](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=AcomDC_0211_2016-->