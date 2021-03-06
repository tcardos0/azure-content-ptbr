<properties
   pageTitle="Implantar o StorSimple Virtual Array - Provisionar no Hyper-V"
   description="Esse segundo tutorial sobre a implantação do StorSimple Virtual Array envolve o provisionamento de um dispositivo virtual no Hyper-V."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="alkohli"/>

# Implantar o StorSimple Virtual Array - Provisionar um Virtual Array no Hyper-V (visualização)

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## Visão geral 

Este tutorial de provisionamento se aplica ao Microsoft Azure StorSimple Virtual Array (também conhecido como dispositivos virtuais locais StorSimple ou dispositivos virtuais StorSimple) executando somente a v.1.1.1.0 (visualização pública). Este tutorial descreve como provisionar um StorSimple Virtual Array em um sistema de host que executa o Hyper-V 2008 R2, o Hyper-V 2012 ou o Hyper-V 2012 R2.

Você precisará de privilégios de administrador para provisionar e configurar um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos.

> [AZURE.IMPORTANT]
> 
> Esta visualização pública destina-se somente para avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção.

## Pré-requisitos de provisionamento

Aqui você encontrará os pré-requisitos para provisionar um dispositivo virtual em um sistema de host que executa o Hyper-V 2008 R2, o Hyper-V 2012 ou o Hyper-V 2012 R2.

### Para o serviço StorSimple Manager

Antes de começar, verifique se:

-   Você concluiu todas as etapas em [Preparar o portal para a StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md).

-   Você baixou a imagem do dispositivo virtual para o Hyper-V no portal do Azure. Para obter mais informações, consulte [Etapa 3: baixar a imagem do dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Para o dispositivo virtual StorSimple 

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host que executa o Hyper-V (2008 R2 ou superior) que pode ser usado para provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

	-   Um mínimo de quatro núcleos.
	
	-   Pelo menos 8 GB de RAM.
	
	-   Uma interface de rede.
	
	-   Um disco virtual de 500 GB para dados do sistema.

### Para a rede no datacenter 

Antes de começar, verifique se:

-   Você revisou os requisitos de rede para implantar um dispositivo virtual StorSimple e configurou a rede de datacenter de acordo com os requisitos. Para obter mais informações, consulte os [Requisitos de rede do StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## Provisionamento passo a passo 

Para provisionar e se conectar a um dispositivo virtual, você precisará executar as seguintes etapas:

1.  Verificar se o sistema de host tem recursos suficientes para cumprir os requisitos mínimos de dispositivos virtuais.

2.  Provisionar um dispositivo virtual no seu hipervisor.

3.  Iniciar o dispositivo virtual e obter o endereço IP.

Cada uma das etapas acima é explicada nas seções a seguir.

## Etapa 1: Verificar se o sistema de host cumpre os requisitos mínimos de dispositivo virtual

Para criar um dispositivo virtual, você precisará de:

-   Hyper-V 2008 R2 SP1, Hyper-V 2012 ou Hyper-V 2012 R2 em execução no seu sistema de host Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2.

-   Microsoft Hyper-V Manager em um cliente Microsoft Windows conectado ao host.

Você deve garantir que o hardware subjacente (sistema de host) em que está criando o dispositivo virtual possa dedicar os recursos a seguir ao seu dispositivo virtual:

- Um mínimo de quatro núcleos.
- Pelo menos 8 GB de RAM.
- Uma interface de rede.
- Um disco virtual de 500 GB para dados do sistema.

## Etapa 2: provisionar um dispositivo virtual no hipervisor

Execute as etapas a seguir para provisionar um dispositivo no seu hipervisor.

#### Para provisionar um dispositivo virtual

1.  No host do Windows Server, copie a imagem do dispositivo virtual na unidade local. Esta é a imagem que você baixou por meio do portal do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.

2.  Abra o **Gerenciador do Servidor**. No canto superior direito, clique em **Ferramentas** e selecione **Gerenciador do Hyper-V**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Se você estiver executando o Hyper-V 2008 R2, abra o Gerenciador do Hyper-V. No Gerenciador do Servidor, clique em **Funções > Hyper-V > Gerenciador do Hyper-V**.

1.  No **Gerenciador do Hyper-V**, no painel de escopo, clique com o botão direito do mouse no nó do seu sistema para abrir o menu de contexto. Selecione **Novo** e, em seguida, selecione **Máquina virtual**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Na página **Antes de começar** clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image3.png)

1.  Na página **Especificar nome e local**, forneça um **Nome** para seu dispositivo virtual. Clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Na página **Especificar geração**, escolha **Geração 1**. Clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

	Esta tela não será apresentada se o Hyper-V 2008 R2 estiver em execução.

1.  Na página **Atribuir memória**:

    a. Especifique uma **Memória de inicialização** de 8192 MB ou mais. O requisito mínimo de memória para um dispositivo virtual StorSimple é de 8 GB ou mais. Não marque a opção para **Usar Memória Dinâmica para esta máquina virtual**.

    b. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Na página **Configurar rede**:

    a. Na lista suspensa para **Conexão**, selecione um comutador virtual. Você precisará selecionar um comutador virtual que esteja conectado à Internet.

    b. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Na página **Conectar disco rígido virtual**:

    a. Selecione a opção de **usar um disco rígido virtual existente**. Aponte para o VHD que é baixado no sistema de host.

    b. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8.png)

1.  Examine o **Resumo** apresentado a você. Clique em **Concluir** para criar a máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Para cumprir os requisitos mínimos, você precisará de quatro núcleos. Para adicionar quatro processadores virtuais, com o sistema de host selecionado na janela **Gerenciador do Hyper-V**, no painel à direita sob a lista de **Máquinas virtuais**, localize a máquina virtual que você acabou de criar. Selecione e clique com o botão direito do mouse no nome do computador e selecione **Configurações**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Na página **Configurações**, no painel à esquerda, clique em **Processador**. No painel à direita, defina o **número de processadores virtuais** como quatro (ou mais). Clique em **Aplicar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Para cumprir os requisitos mínimos, você também precisa adicionar um disco de dados virtual de 500 GB. Na página **Configurações**:

    1.  No painel à esquerda, selecione **Controlador SCSI**. 
    2.  No painel à direita, selecione **Disco rígido** e clique em **Adicionar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Na página **Disco rígido**, selecione a opção **Disco rígido virtual** e clique em **Novo**. Isso iniciará o **Assistente de novo disco rígido virtual**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Na **página Antes de começar**, clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image14.png)

1.  Na **página Escolher formato de disco**, aceite a opção padrão de formato **VHDX**. Clique em **Avançar**. Esta tela não será apresentada se o Hyper-V 2008 R2 estiver em execução.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Na **página Escolher tipo de disco**, defina o tipo de disco rígido virtual como **Expansão dinâmica** (recomendado). Se escolher um disco de **Tamanho fixo**, também funcionará, mas talvez seja necessário esperar muito mais tempo. É recomendável que você não use a opção **Diferenciar**. Clique em **Próximo**. Observe que **Expansão dinâmica** é o padrão no Hyper-V 2012 e no Hyper-V 2012 R2. No Hyper-V 2008 R2, o padrão é **Tamanho fixo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Na página **Especificar nome e local**, forneça um **nome** e também um **local** (pode-se navegar até um) para o disco de dados. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Na página **Configurar disco**, selecione a opção **Criar um novo disco de rígido virtual em branco** e especifique o tamanho como **500 GB** (ou mais). Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Na página **Resumo**, examine os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Concluir** para criar o disco. O assistente será fechado e um disco rígido virtual será adicionado ao seu computador.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

1.  Você retornará para a página **Configurações**. Clique em **OK** para fechar a página **Configurações** e retornar à janela do Gerenciador do Hyper-V.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## Etapa 3: iniciar o dispositivo virtual e obter o IP

Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Depois que o dispositivo estiver em execução, selecione-o, clique com o botão direito do mouse e selecione **Conectar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Você terá que esperar de 5 a 10 minutos para que o dispositivo esteja pronto. Será exibida uma mensagem de status no console para indicar o andamento. Depois que o dispositivo estiver pronto, vá para **Ação**. Pressione `Ctrl + Alt + Delete` para fazer logon no dispositivo virtual. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Password1*.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Por motivos de segurança, a senha do administrador do dispositivo expira no primeiro logon. Será solicitado que você altere a senha.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	Insira uma senha que contenha pelo menos oito caracteres. A senha deve cumprir pelo menos três dos quatro requisitos a seguir: caracteres maiúsculos, minúsculos, numéricos e especiais. Insira a senha novamente para confirmá-la. Você será notificado de que a senha foi alterada.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Depois que a senha for alterada com êxito, o dispositivo virtual poderá ser reiniciado. Aguarde até o dispositivo iniciar.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	O console do Windows PowerShell do dispositivo será exibido junto com uma barra de progresso.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  As etapas 6 a 8 se aplicam somente ao iniciar em um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você verá a tela a seguir.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28.png)

 	Agora, você precisará configurar a rede.

1.  Use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29.png)

1.  Use o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é mostrado abaixo:

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e o registro.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31.png)

	Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa (mostrado abaixo). Será necessário modificar a configuração do dispositivo para que ele tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração na [Etapa 1: verifique se o sistema de host cumpre os requisitos mínimos de dispositivo virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

	![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Se você enfrentar qualquer outro erro durante a configuração inicial usando a interface do usuário da Web local, consulte os seguintes fluxos de trabalho em [Gerenciar sua StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).

-   Execute testes de diagnóstico para [solucionar problemas na configuração da interface do usuário da Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Gere um pacote de log e exiba os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![ícone de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Vídeo disponível**

Assista ao vídeo para ver como você pode provisionar um StorSimple Virtual Array no Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## Próximas etapas

-   [Configurar sua StorSimple Virtual Array como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar sua StorSimple Virtual Array como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0128_2016-->