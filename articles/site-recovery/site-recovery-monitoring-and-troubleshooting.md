<properties
	pageTitle="Guia de monitoramento e solução de problemas de proteção dos sites VMM e Hyper-V" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores locais no Azure ou em um armazenamento de dados secundário. Use este artigo para monitorar e solucionar problemas de proteção de sites do VMM ou do Hyper-V." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/03/2015" 
	ms.author="anbacker"/>
	
# Monitorar e solucionar problemas de proteção para VMM e sites do Hyper-V

Este guia de solução de problemas e monitoramento permite que você aprenda sobre o acompanhamento da integridade da replicação e sobre técnicas de solução de problemas do Azure Site Recovery.

## Noções básicas sobre os componentes

### Implantação de Site do VMM para replicação no site local.

Como parte da configuração de recuperação de desastres entre dois espaços locais, o Provedor do Azure Site Recovery precisa ser baixado e instalado no servidor do VMM. O provedor precisa de conectividade com a internet para garantir que todas as operações acionadas no Portal do Azure sejam traduzidas por operações locais como habilitar a proteção, desligamento das máquinas virtuais do lado primário como parte de failovers etc.

![](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Implantação de Site do VMM para replicação entre o local e o Azure.

Como parte da configuração de recuperação de desastres entre o local e o Azure, o Provedor do Azure Site Recovery precisa ser baixado e instalado no servidor do VMM junto com o Agente de Serviços de Recuperação do Azure que precisa ser instalado em cada host Hyper-V.

![](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Implantação de Site do Hyper-V para replicação entre o local e o Azure.

É igual à Implantação do VMM – a única diferença é que o Provedor e o Agente são instalados no próprio host Hyper-V.

## Monitorar as operações de configuração, proteção e recuperação

Todas as operações do Azure Site Recovery (ASR) são auditadas e podem ser controladas na guia "TRABALHOS". No caso de qualquer erro de configuração, proteção ou recuperação, acesse a guia TRABALHOS e veja se há falhas.

![](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Caso você encontre falhas na exibição dos TRABALHOS, selecione o TRABALHO e clique em DETALHES DO ERRO desse trabalho.

![](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Os detalhes do erro ajudarão você a identificar a possível causa e a recomendação para o problema.

![](media/site-recovery-monitoring-and-troubleshooting/image5.png)

No caso acima, parece haver outra operação em andamento que está causando a falha na configuração da Proteção. Resolva o problema de acordo com a recomendação e, em seguida, clique em REINICIAR para iniciar novamente a operação.

![](media/site-recovery-monitoring-and-troubleshooting/image6.png)

A opção de REINICIAR não está disponível para todas as operações. Para aquelas que não tenham a opção de reinicialização, retorne ao objeto e repita a operação. Todos os TRABALHOS podem ser cancelados a qualquer momento durante o processamento por meio do botão CANCELAR.

![](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Monitorar a integridade de replicação da máquina virtual

Monitoramento central e remoto do provedor ASR por meio do portal do Azure para cada uma das entidades protegidas. Navegue até os ITENS PROTEGIDOS e selecione NUVENS DO VMM ou GRUPOS DE PROTEÇÃO. A guia NUVENS DO VMM é usada somente para implantações do VMM e todos os outros cenários têm as entidades protegidas na guia GRUPOS DE PROTEÇÃO.

![](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Em seguida, selecione a entidade protegida na nuvem correspondente ou no grupo de proteção. Depois de selecionar a entidade protegida, todas as operações permitidas são mostradas no painel inferior.

![](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como mostrado acima, caso a INTEGRIDADE da máquina virtual seja crítica, você poderá clicar no botão DETALHES DO ERRO na parte inferior para ver o erro. Com base nas "Possíveis causas" e na "Recomendação" mencionadas, resolva o problema. Neste caso, a máquina virtual precisa ser novamente sincronizada, o que pode ser feito no próprio portal clicando no botão RESSINCRONIZAR.

![](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Observação: se houver quaisquer operações ativas que estejam em andamento ou tenham falhado, navegue até o modo de exibição de TRABALHOS como mencionado anteriormente para exibir o erro específico do TRABALHO.

## Solucionar problemas locais

Conecte-se ao console do gerenciador local do Hyper-V, selecione a máquina virtual e verifique a integridade da replicação.

![](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Nesse caso, a *Integridade da Replicação* está indicada como crítica; clique em *Exibir Integridade da Replicação* para ver os detalhes.

![](media/site-recovery-monitoring-and-troubleshooting/image13.png)

#### Visualizador de Eventos

| Cenários | Origens de eventos |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Proteção de site do VMM | Servidor VMM<ul><li> **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Host Hyper-V <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Para Azure como Destino)</li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Proteção de sites do Hyper-V | <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** </li><li> **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Opções de log de replicação do Hyper-V

Todos os eventos relativos ao Hyper-V Replica são registrados no log do Hyper-V-VMMS\\Admin localizado em **Applications and Service Logs\\Microsoft\\Windows**. Além disso, um log analítico pode ser habilitado para o VMMS do Hyper-V. Para habilitar esse log, primeiro exiba os logs analíticos e de depuração no Visualizador de Eventos. Abra o Visualizador de Eventos e, em seguida, no **menu Exibir**, clique em **Mostrar Logs Analíticos e de Depuração**.

![](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Um log Analítico pode ser visto no VMMS do Hyper-V

![](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Em seguida, no painel **Ações**, clique em **Habilitar Log**. Uma vez habilitado, ele é exibido no **Monitor de Desempenho** como uma Sessão de Rastreamento de Evento localizado em **Conjuntos de Coletores de Dados.**

![](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para exibir as informações coletadas, primeiro interrompa a sessão de rastreamento desabilitando o log; em seguida, salve o log e abra-o novamente no Visualizador de Eventos ou use outras ferramentas para convertê-lo conforme desejado.


## Noções básicas sobre o ciclo de vida da máquina virtual

![](media/site-recovery-monitoring-and-troubleshooting/image17.png)


## Contatando o Suporte da Microsoft

### Coleta de logs

Para a proteção de do Site do VMM, consulte [Coleta de Logs do ASR usando a Ferramenta de Plataforma de Diagnóstico de Suporte (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para coletar os logs necessários.

Para proteção de filiais do Hyper-V e do site do SMB, baixe a ferramenta [ ](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)e execute-a no host Hyper-V para coletar os logs.

Para cenários do VMware/físicos, consulte [Coleta de Logs do Azure Site Recovery para proteção de sites do VMware e físicos](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para coletar os logs necessários.

A ferramenta SDP coleta o arquivo de log localmente, mas ele também pode ser localizado em uma subpasta nomeada aleatoriamente em **%LocalAppData%\\ElevatedDiagnostics**

### Abrindo um tíquete de suporte

Para gerar um tíquete de suporte para o ASR, acesse o Suporte do Azure usando a URL em <http://aka.ms/getazuresupport>

## Artigos da Base de Dados de Conhecimento

-   [Como preservar a letra da unidade para máquinas virtuais
    > http://support.microsoft.com/kb/3031135

-   [Como solucionar problemas dos Serviços de Recuperação
    > do Azure](http://support.microsoft.com/kb/3005185)

-   [Como habilitar o log de depuração do Azure Site Recovery na
    > Proteção de Site do Hyper-V](http://support.microsoft.com/kb/3033922)

-   [ASR: erro "O recurso de cluster não foi encontrado" quando você tenta
    > habilitar a proteção para uma máquina virtual]http://support.microsoft.com/kb/3010979)
    
-   [Guia para compreender e solucionar problemas da Réplica do Hyper-V]
    > (http://www.microsoft.com/en-in/download/details.aspx?id=29016)
	
	
	
## Erros de ASR comuns e suas resoluções

Veja abaixo os erros comuns que você pode encontrar e suas resoluções. Cada erro é documentado em uma página WIKI separada.

### Configuração

-   [Não é possível validar o certificado selecionado. Selecione um certificado diferente.](http://social.technet.microsoft.com/wiki/contents/articles/25569.the-selected-certificate-cannot-be-validated-please-select-a-different-certificate.aspx)

-   [Não é possível registrar o servidor VMM devido a um erro interno. Consulte o modo de exibição de trabalhos no Portal de Recuperação de Site para obter mais detalhes sobre o erro. Execute a Instalação novamente para registrar o servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)

-   [Não é possível estabelecer uma conexão com o cofre do Gerenciador de Recuperação do Hyper-V. Verifique as configurações de proxy ou tente novamente mais tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Configuração

-   [O cluster do host Hyper-V contém pelo menos um adaptador de rede estático ou nenhum adaptador conectado está configurado para usar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)

-   [O perfil do Hyper-V não está habilitado nos Perfis de Funcionalidade da nuvem](http://social.technet.microsoft.com/wiki/contents/articles/25499.the-hyper-v-profile-isn-t-enabled-in-the-capability-profiles-for-cloud.aspx)

-   [Não foi possível aplicar a configuração de proteção para '% CloudName;'. Não foi possível configurar um host Hyper-V ou cluster adicionado recentemente, pois a proteção da nuvem não está configurada.](http://social.technet.microsoft.com/wiki/contents/articles/25500.protection-configuration-for-cloudname-couldn-t-be-applied-a-newly-added-hyper-v-host-or-cluster-couldn-t-be-configured-because-cloud-protection-isn-t-configured.aspx)

-   [O VMM não tem permissões para completar uma ação](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)

### Proteção

-   [Não há suporte para o sistema operacional](http://social.technet.microsoft.com/wiki/contents/articles/31103.the-operating-system-is-not-supported.aspx)

-   [Falha na habilitação da proteção, pois o Agente não está instalado na máquina host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)

-   [Não é possível encontrar um host adequado para a máquina virtual de réplica - Há poucos recursos de computação](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)

-   [Não é possível encontrar um host adequado para a máquina virtual de réplica - Não há uma rede lógica anexada](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)

-   [Não é possível conectar-se à máquina host de réplica - Não foi possível estabelecer a conexão](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### Recuperação

-   A VMM não conseguiu concluir a operação do host -

    -   [Failover para o ponto de recuperação selecionado para a máquina virtual: erro geral de acesso negado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)

    -   [Hyper-V não conseguiu fazer failover para o ponto de recuperação selecionado para a máquina virtual: operação anulada, tente um ponto de recuperação mais recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)

    -   Não foi possível estabelecer uma conexão com o servidor (0x00002EFD)

        -   [O Hyper-V não conseguiu habilitar a replicação inversa para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)

        -   [O Hyper-V não conseguiu habilitar a replicação para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)

    -   [Não foi possível confirmar o failover para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)

-   [O plano de recuperação contém máquinas virtuais que não estão prontas para o failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)

-   [A máquina virtual não está pronta para o failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)

-   [A máquina virtual não está em execução e não está desligada](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)

-   [Ocorreu uma operação fora de banda em uma máquina virtual e ](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)não foi possível confirmar o failover

-   Failover de Teste

    -   [Não foi possível iniciar o failover, pois o failover de teste está em andamento](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

-   Específico da recuperação para o Azure

    -   [Não foi possível iniciar o failover devido a caracteres sem suporte no nome da VM](http://social.technet.microsoft.com/wiki/contents/articles/31107.failover-could-not-be-started-due-to-unsupported-characters-in-the-vm-name.aspx)

### Servidor de Configuração, Servidor de Processo, Destino Mestre

Servidor de Configuração (CS), Servidor de Processo (PS), Destino Mestre (MT)

-   [O host ESXi no qual o PS/CS está hospedado como uma VM falha com uma tela roxa.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

<!---HONumber=July15_HO2-->