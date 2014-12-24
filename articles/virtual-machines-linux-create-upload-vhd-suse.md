<properties urlDisplayName="Upload a SUSE Linux VHD" pageTitle="Criar e carregar um VHD do SUSE Linux no Azure" metaKeywords="Azure VHD, uploading Linux VHD, SUSE, SLES, openSUSE" description="Saiba como criar e carregar um disco r&iacute;gido virtual (VHD) do Azure que cont&eacute;m o sistema operacional SUSE Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Criando e carregando um disco r&iacute;gido virtual que cont&eacute;m o sistema operacional SUSE Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Preparar uma máquina virtual do SLES ou openSUSE para o Azure

-   [Preparar uma máquina virtual do SLES 11 SP3 para o Azure][Preparar uma máquina virtual do SLES 11 SP3 para o Azure]
-   [Preparar uma máquina virtual do openSUSE 13.1+ para o Azure][Preparar uma máquina virtual do openSUSE 13.1+ para o Azure]

## Pré-requisitos

Este artigo pressupõe que você já instalou um sistema operacional SUSE ou openSUSE Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual][Instalar a função Hyper-V e configurar uma máquina Virtual].

**Notas de instalação do SLES / openSUSE**

-   o [SUSE Studio][SUSE Studio] pode criar e gerenciar facilmente suas imagens SLES / openSUSE para Azure e Hyper-V. Essa é a abordagem recomendada para personalizar suas próprias imagens SUSE e openSUSE. As seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em seu próprio SUSE Studio:

-   [SLES 11 SP3 para Azure no SUSE Studio Gallery][SLES 11 SP3 para Azure no SUSE Studio Gallery]
-   [openSUSE 13.1 para Azure no SUSE Studio Gallery][openSUSE 13.1 para Azure no SUSE Studio Gallery]

-   Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

-   Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou [RAID][RAID] em discos de dados.

-   Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.

-   Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.

## <span id="sles11"></span> </a>Preparar o SUSE Linux Enterprise Server 11 SP3

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Adicione o repositório que contém o kernel mais recente e o Agente Linux do Azure. Execute o comando `zypper lr`. Por exemplo, com o SLES 11 SP3, a saída deve ser semelhante ao seguinte:

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    Caso o comando retorne uma mensagem de erro como a seguinte:

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    use os seguintes comandos para adicionar esses repositórios:

        # sudo zypper ar -f http://azure-update.susecloud.net/repo/$RCE/SLES11-SP3-Pool/sle-11-x86_64 SLES11-SP3-Pool 
        # sudo zypper ar -f http://azure-update.susecloud.net/repo/$RCE/SLES11-SP3-Updates/sle-11-x86_64 SLES11-SP3-Updates

    Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

        # sudo zypper mr -e [REPOSITORY NUMBER]

4.  Atualize o kernel para a versão mais recente disponível:

        # sudo zypper up kernel-default

    Ou para atualizar o sistema com todos os patches mais recentes:

        # sudo zypper update

5.  Instale o Agente Linux do Azure:

        # sudo zypper install WALinuxAgent

6.  Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.

7.  É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

8.  Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

10. Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Clique em **Ação -\> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

------------------------------------------------------------------------

## <span id="osuse"></span> </a>Preparar o openSUSE 13.1+

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual

2.  Clique em **Conectar** para abrir a janela para a máquina virtual

3.  No shell, execute o comando '`zypper lr`'. Se esse comando retornar uma saída semelhante à seguinte (observe que os números de versão podem variar):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    , os repositórios estão configurados conforme o esperado e nenhum ajuste é necessário.

    Se o comando retornar "Nenhum repositório definido...", use os seguintes comandos para adicionar esses repositórios:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1 
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Em seguida, você pode verificar se os repositórios foram adicionados executando novamente o comando "`zypper lr`". Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]

4.  Atualize o kernel para a versão mais recente disponível:

        # sudo zypper up kernel-default

    Ou para atualizar o sistema com todos os patches mais recentes:

        # sudo zypper update

5.  Instale o Agente Linux do Azure

        # sudo zypper install WALinuxAgent

6.  Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, remova os seguintes parâmetros da linha de inicialização do kernel, se existirem:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importante:** Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

10. Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Verifique se o Agente Linux do Azure é executado durante a inicialização:

        # sudo systemctl enable waagent.service

13. Clique em **Ação -\> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

  [Preparar uma máquina virtual do SLES 11 SP3 para o Azure]: #sles11
  [Preparar uma máquina virtual do openSUSE 13.1+ para o Azure]: #osuse
  [Instalar a função Hyper-V e configurar uma máquina Virtual]: http://technet.microsoft.com/library/hh846766.aspx
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP3 para Azure no SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [openSUSE 13.1 para Azure no SUSE Studio Gallery]: https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure
  [RAID]: ../virtual-machines-linux-configure-raid