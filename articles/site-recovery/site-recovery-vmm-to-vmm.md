<properties 
	pageTitle="Configurar a proteção entre os sites VMM locais" 
	description="O Azure Site Recovery coordena a replicação, failover e recuperação das máquinas virtuais Hyper-V entre os sites VMM locais." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Configurar a proteção entre os sites VMM locais


## Visão geral


O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte [Visão geral do Azure Site Recovery](../hyper-v-recovery-manager-overview.md).

Este guia de cenários descreve como implantar o Site Recovery para orquestrar e automatizar a proteção para cargas de trabalho em execução em máquinas virtuais nos servidores host Hyper-V que estão localizados em nuvens privadas do VMM. Nesse cenário, máquinas virtuais são replicadas de um site primário do VMM para um site secundário do VMM usando a Réplica do Hyper-V.

O guia inclui os pré-requisitos para o cenário e mostra como configurar um cofre do Site Recovery, instalar o Provedor do Azure Site Recovery nos servidores VMM de origem e destino, registrar os servidores no cofre, definir as configurações de proteção para nuvens VMM que serão aplicadas a todas as máquinas virtuais protegidas e, em seguida, habilitar a proteção para essas máquinas virtuais. Conclua testando o failover para verificar se tudo está funcionando conforme o esperado.

Se tiver problemas ao configurar este cenário, poste suas perguntas no [Fórum de Serviços de Recuperação do Azure](http://go.microsoft.com/fwlink/?LinkId=313628).


## Antes de começar
Verifique se estes pré-requisitos estão em vigor:
### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Se você não tiver uma, comece com uma [avaliação gratuita](http://aka.ms/try-azure). Além disso, você pode ler sobre [preços do Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).
- Para entender como as informações e os dados são usados, leia a [Declaração de Privacidade do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899) e as <a href="#privacy">informações de privacidade adicionais para o Site Recovery</a> na parte inferior deste tópico.

### Pré-requisitos do VMM
- Você precisará de pelo menos um servidor VMM.
- O servidor VMM deve estar executando pelo menos o System Center 2012 SP1 com as últimas atualizações cumulativas. 
- Qualquer servidor VMM que contenha máquinas virtuais que você deseje proteger deverá estar executando o Provedor do Azure Site Recovery. Ele é instalado durante a implantação do Azure Site Recovery.
- Se quiser configurar a proteção com um único servidor VMM, você precisará de pelo menos duas nuvens configuradas no servidor.
- Se você desejar implantar a proteção com dois servidores VMM, cada servidor deverá ter pelo menos uma nuvem configurada no servidor VMM primário que você deseja proteger e uma nuvem configurada no servidor VMM secundário que você deseja usar para proteção e recuperação
- Todas as nuvens VMM devem ter o perfil de Capacidade do Hyper-V definido.
- A nuvem principal que você deseja proteger deve conter o seguinte:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais servidores host Hyper-V em cada grupo de hosts.
	- Uma ou mais máquinas virtuais no servidor host. 
- Saiba mais sobre como configurar nuvens VMM:
	- Leia mais sobre nuvens VMM privadas em [Novidades na nuvem privada com o System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e em [VMM 2012 e as nuvens](http://go.microsoft.com/fwlink/?LinkId=324956). 
	- Saiba mais em [Configurando a malha de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Depois que os elementos de malha de nuvem estiverem em vigor, veja como criar nuvens privadas em [Criando uma nuvem privada no VMM](http://go.microsoft.com/fwlink/?LinkId=324953) e [Passo a passo: criando nuvens privadas com o System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954).

### Pré-requisitos do Hyper-V

- Os servidores Hyper-V host e de destino devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.
- Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereços IP estáticos. Você precisará configurar o agente de cluster manualmente. Para obter instruções, consulte [Configurar o agente de réplica do Hyper-V](http://go.microsoft.com/fwlink/?LinkId=403937).
- Qualquer cluster ou servidor host Hyper-V para o qual você desejar gerenciar a proteção deverá ser incluído em uma nuvem VMM.
 
### Pré-requisitos de mapeamento de rede
O mapeamento de rede garante que as máquinas virtuais de réplica sejam colocadas idealmente em servidores host Hyper-V depois do failover e que elas possam se conectar a redes VM apropriadas. Se você não configurar o mapeamento de rede, as máquinas virtuais de réplica não serão conectadas às redes VM após o failover. Se desejar implantar o mapeamento de rede, você precisará do seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem devem estar conectadas a uma rede VM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
- A nuvem de destino no servidor VMM secundário que você usar para recuperação deverá ter uma rede VM correspondente configurada, que, por sua vez, deverá estar vinculada a uma rede lógica correspondente associada à nuvem de destino.

Saiba mais sobre o mapeamento de rede:

- [Configurando a rede lógica no VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
- [Configurando redes VM e gateways no VMM](http://go.microsoft.com/fwlink/?LinkId=386308)

### Pré-requisitos de mapeamento de armazenamento
Por padrão, quando você replica uma máquina virtual em um servidor host Hyper-V de origem para um servidor host Hyper-V de destino, os dados replicados são armazenados no local padrão indicado para o host Hyper-V de destino no Gerenciador Hyper-V. Para obter mais controle sobre o local em que os dados replicados são armazenados, você pode configurar o mapeamento de armazenamento. Para fazer isso, você precisará configurar as classificações de armazenamento nos servidores VMM de origem e de destino antes de começar a implantação.
Para obter instruções, consulte [Como criar classificações de armazenamento no VMM](http://go.microsoft.com/fwlink/?LinkId=400937).


## Etapa 1: Criar um cofre do o Site Recovery

1. Entre no [Portal de Gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar.

2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre do Site Recovery**.


3. Clique em **Criar Novo** > **Criação Rápida**.
	
4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=389880).</a>

6. Clique em **Criar cofre**.

	![Create vault](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CreateVault.png)

Na barra de status, verifique se o cofre foi criado. O cofre será listado como **Ativo** na página de Serviços de Recuperação.

## Etapa 2: Gerar uma chave de registro do cofre

Gere uma chave de registro no cofre. Após baixar o Provedor do Azure Site Recovery e instalá-lo no servidor VMM, você usará essa chave para registrar o servidor VMM no cofre.

1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre dois sites Hyper-V no local**.
3. Em **Preparar Servidores VMM**, clique em **Gerar arquivo de chave de registro**. O arquivo de chave é gerado automaticamente e é válido por cinco dias após ter sido gerado. Se não estiver acessando o portal do Azure por meio do servidor VMM, você precisará copiar esse arquivo para o servidor. 

	![Registration key](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_E2ERegisterKey.png)
	
## Etapa 3: Instalar o Provedor de Recuperação do Site do Azure	

1. Na página <b>Início Rápido</b>, em **Preparar servidores VMM**, clique <b>Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM</b> para obter a versão mais recente do arquivo de instalação do Provedor.

2. Execute esse arquivo nos servidores VMM de início e de destino. Se o VMM for implantado em um cluster e você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós. Observe que, se estiver atualizando o Provedor você precisará fazer a atualização em todos os nós porque todos eles devem estar executando a mesma versão do Provedor.

3. Na **Verificação dos Pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza. Se estiver instalando em um cluster do VMM, você deverá parar a função de Cluster.

	![Prerequisites](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderPrereq.png)

4. Em **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderUpdate.png)

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

5. Na página Conexão com a Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione <b>Usar configurações do proxy do sistema padrão</b> para usar as configurações de conexão à Internet padrão configuradas no servidor.

	![Internet Settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderProxy.png)

	- Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o provedor. Quando você definir as configurações personalizadas de proxy, será executado um teste para verificar a conexão proxy.
	- Se usar um proxy personalizado ou se seu proxy padrão exigir autenticação, você precisará inserir os detalhes do proxy, incluindo a porta e o endereço do proxy.
	- Você deve isentar os seguintes endereços do roteamento por meio do proxy:
		- A URL para se conectar ao Azure Site Recovery: *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net 
		- *.store.core.windows.net 
	- Se for necessário permitir conexões de saída para um controlador de domínio do Azure, permita os endereços IP descritos em [Intervalos de IP do Data Center do Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e permita os protocolos HTTP (80) e HTTPS (443). 
	- Se você usar um proxy personalizado, uma conta RunAs VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações da conta RunAs VMM podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho Configurações, expanda Segurança, clique em contas Executar como e modifique a senha de DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração entre em vigor.
6. Em **Chave de Registro**, selecione a chave que você baixou do Azure Site Recovery e copiou para o servidor VMM.
7. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM. 

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderRegKeyServerName.png)


9. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.


7. A opção **Criptografia de Dados** não é relevante para a proteção de local para local.

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderSyncEncrypt.png)

8. Clique em <b>Registrar</b> para concluir o processo. Os metadados do servidor VMM são recuperados pelo Azure Site Recovery. O servidor é exibido no final da guia **Recursos** da página **Servidores** no cofre.

Após o registro, você pode alterar as configurações do provedor no console do VMM ou na linha de comando.

## Etapa 4: Definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Se você tiver habilitado a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, todas as nuvens no servidor VMM serão mostradas na guia <b>Itens Protegidos</b> no cofre. Se não a tiver habilitado, você poderá sincronizar uma nuvem específica com o Azure Site Recovery na guia **Geral** da página de propriedades de nuvem no console do VMM.

![Published Cloud](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudsList.png)

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Nuvens VMM**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. 
3. Em <b>Destino</b>, selecione <b>VMM</b>.
4. Em <b>Local de destino</b>, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para a recuperação.
4. Em <b>Nuvem de destino</b>, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:
	- É recomendável selecionar uma nuvem de destino que atenda aos requisitos de recuperação para máquinas virtuais que você protegerá.
	- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
6. Em <b>Frequência de cópia</b>, especifique com que frequência os dados devem ser sincronizados entre os locais de origem e de destino. Observe que essa configuração só é relevante quando o host Hyper-V está executando o Windows Server 2012 R2. Para outros servidores, é usada uma configuração padrão de cinco minutos.
7. Em <b>Pontos de recuperação adicionais</b>, especifique se deseja criar pontos de recuperação adicionais. O valor padrão de zero indica que somente o último ponto de recuperação para uma máquina virtual primária é armazenado em um servidor host de réplica. Observe que a habilitação de vários pontos de recuperação exige armazenamento adicional para os instantâneos que são armazenados em cada ponto de recuperação. Por padrão, os pontos de recuperação são criados a cada hora, assim, cada ponto de recuperação contém dados válidos equivalentes a uma hora. O valor de ponto de recuperação que você atribui à máquina virtual no console do VMM não deve ser menor do que o valor atribuído no console do Azure Site Recovery.
8. Em <b>Frequência de instantâneos consistentes com aplicativos</b>, especifique a frequência para criar instantâneos consistentes com aplicativos. O Hyper-V usa dois tipos de backup: um backup padrão que fornece um backup incremental de toda a máquina virtual e um instantâneo consistente com aplicativo que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Observe que, se você habilitar instantâneos consistentes com aplicativos, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.

	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettings.png)

9. Em <b>Compactação de transferência de dados</b>, especifique se os dados replicados transferidos devem ser compactados. 
10. Em <b>Autenticação</b>, especifique como o tráfego é autenticado entre os servidores de host Hyper-V primário e de recuperação. Selecione HTTPS, a menos que você tenha um ambiente Kerberos configurado em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Nenhuma configuração manual é necessária. Se você selecionar Kerberos, será usado um tíquete Kerberos para autenticação mútua dos servidores host. Por padrão, as portas 8083 e 8084 (para certificados) serão abertas no Firewall do Windows nos servidores host Hyper-V. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
11. Em <b>Porta</b>, modifique o número da porta em que os computadores host de origem e de destino ouvem o tráfego de replicação. Por exemplo, você poderá modificar a configuração se desejar aplicar a limitação de largura de banda de rede de QoS (Qualidade de Serviço) ao tráfego de replicação. Verifique se a porta não é usada por outro aplicativo e se ela está aberta nas configurações do firewall.
12. Em <b>Método de replicação</b>, especifique como a replicação inicial de dados de locais de origem para destino será tratada, antes de iniciar a replicação normal. 
	- <b>Pela rede</b>- A cópia de dados pela rede pode ser demorada e consumir muitos recursos. Recomendamos usar essa opção se a nuvem contiver máquinas virtuais com discos rígidos virtuais relativamente pequenos e se o servidor VMM primário estiver conectado ao servidor VMM secundário por meio de uma conexão com banda larga ampla. Você pode especificar que a cópia deve iniciar imediatamente ou selecionar uma hora. Se você usar a replicação de rede, recomendamos que você agende fora dos horários de pico.
	- <b>Offline</b>- Esse método especifica que a replicação inicial será executada usando mídia externa. É útil se você deseja evitar a degradação no desempenho da rede ou para locais geograficamente remotos. Para usar esse método, você especifica o local de exportação na nuvem de origem e o local de importação na nuvem de destino. Ao ativar a proteção de uma máquina virtual, o disco rígido virtual é copiado para o local de exportação especificado. Você envia para o local de destino e copia para o local de importação. O sistema copia as informações importadas para as máquinas virtuais de réplica. Para uma lista completa dos pré-requisitos da replicação offline, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Etapa 3: Definir configurações de proteção para nuvens VMM</a> no Guia de implantação.
13. Selecione **Excluir Máquina Virtual de Réplica** para especificar que a máquina virtual de réplica deve ser excluída se você parar de proteger a máquina virtual ao selecionar a opção **Excluir proteção para a máquina virtual** na guia Máquinas Virtuais das propriedades da nuvem. Com esta configuração habilitada, quando você desabilita a proteção, a máquina virtual é removida da Recuperação de Site do Azure, as configurações da Recuperação de Site para a máquina virtual são removidas no console VMM, e a réplica é excluída.
	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettingsRep.png)

<p>Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia <b>Trabalhos</b>. Todos os servidores do host Hyper-V na nuvem de origem do VMM serão configurados para replicação. As configurações de nuvem podem ser modificadas na guia <b>Configurar</b>. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.</p>

### Preparar a replicação inicial offline

Você precisará realizar uma série de ações para preparar a replicação inicial offline:

- No servidor de origem, você especificará um local do caminho do qual será realizada a exportação de dados. Atribua Controle Total para permissões de NTFS e de Compartilhamento ao serviço VMM no caminho de exportação. No servidor de destino, você especificará um local do caminho do qual será feita a importação de dados. Atribua as mesmas permissões nesse caminho de importação. 
- Se o caminho de importação ou de exportação for compartilhado, atribua a associação de grupo de Administrador, Usuário Avançado, Operador de Impressão ou Operador de Servidor à conta de serviço VMM no computador remoto no qual o caminho compartilhado está localizado.
- Se você estiver usando contas Executar como para adicionar hosts, nos caminhos de importação e de exportação, atribua permissões de leitura e gravação às contas Executar como no VMM.
- Os compartilhamentos de importação e de exportação não devem estar localizados em um computador usado como um servidor host Hyper-V porque o Hyper-V não dá suporte à configuração de loopback.
- No Active Directory, em cada servidor host Hyper-V que contém máquinas virtuais que deseja proteger, habilite e configure a delegação restrita para confiar nos computadores remotos nos quais os caminhos de importação e de exportação estão localizados, da seguinte maneira:
	1. No controlador de domínio, abra **Usuários e Computadores do Active Directory**.
	2. Na árvore de console, clique em **DomainName** > **Computadores**.
	3. Clique no nome do servidor host Hyper-V > **Propriedades**.
	4. Na guia **Delegação**, clique em C**onfiar no computador para delegação apenas a serviços especificados**.
	5. Clique em **Usar qualquer protocolo de autenticação**.
	6. Clique em **Adicionar** > **Usuários e Computadores**.
	7. Digite o nome do computador que hospeda o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **OK**. Repita o procedimento para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para servidores host Hyper-V adicionais.
	
## Etapa 5: Configurar o mapeamento de rede
1. Na página Início Rápido, clique em **Mapear redes**.
2. Selecione o servidor VMM de origem do qual você deseja mapear redes e o servidor VMM de destino para o qual as redes serão mapeadas. A lista de redes de origem e suas redes de destino associadas é exibida. Um valor vazio é mostrado para redes que não estão mapeadas no momento. Clique no ícone de informações ao lado dos nomes de rede de origem e de destino para exibir as sub-redes de cada rede.
3. Selecione uma rede em **Rede na origem** > **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe. 

	![Configure network mapping](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping1.png)

4. Na caixa de diálogo, selecione uma das redes VM do servidor VMM de destino.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping2.png)

5. Quando você seleciona uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. As redes de destino disponíveis que estão associados às nuvens usadas para proteção também são exibidas. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens que você está usando para proteção.
6. Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho é iniciado para rastrear o progresso do mapeamento. Você pode exibi-lo na Guia **Trabalhos**.


## Etapa 6: Configurar o mapeamento de armazenamento
Por padrão, quando você replica uma máquina virtual em um servidor host Hyper-V de origem para um servidor host Hyper-V de destino, os dados replicados são armazenados no local padrão indicado para o host Hyper-V de destino no Gerenciador Hyper-V. Para obter mais controle sobre onde os dados replicados são armazenados, você pode configurar mapeamentos de armazenamento da seguinte maneira:

- Defina classificações de armazenamento nos servidores VMM de origem e de destino. Para obter instruções, consulte [Como criar classificações de armazenamento no VMM](http://go.microsoft.com/fwlink/?LinkId=400937). As classificações devem estar disponíveis para os servidores host Hyper-V nas nuvens de origem e de destino. As classificações não precisam ter o mesmo tipo de armazenamento. Por exemplo, você pode mapear uma classificação de origem que contenha compartilhamentos SMB para uma classificação de destino que contenha CSVs.
- Depois que classificações estiverem em vigor, você poderá criar mapeamentos.
1. Na página **Início Rápido** > **Mapear armazenamento**.
1. Clique na guia **Armazenamento** > **Mapear classificações de armazenamento**.
1. Na guia **Mapear classificações de armazenamento**, selecione as classificações nos servidores VMM de origem e de destino. Salve suas configurações.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_StorageMapping1.png)


## Etapa 7: Habilitar a proteção da máquina virtual
Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem.

1. Na guia **Máquinas Virtuais** na nuvem em que a máquina virtual está localizada, clique em **Habilitar proteção** > **Adicionar máquinas virtuais**. 
2. Na lista de máquinas virtuais na nuvem, selecione uma que você quer proteger.


![Enable virtual machine protection](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_EnableProtectionVM.png)

Acompanhe o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover. Após a proteção estar habilitada e as máquinas virtuais serem replicadas, você será capaz de visualizá-los no Azure.

Observe que você também pode habilitar a proteção para máquinas virtuais no console do VMM. Clique em Habilitar Proteção na barra de ferramentas na guia do Azure Site Recovery nas propriedades da máquina virtual,


![Virtual machine protection job](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_VMJobs.png)

### Integrar máquinas virtuais existentes
Se tiver máquinas virtuais existentes no VMM que estão sendo replicadas usando a Réplica do Hyper-V, você precisará integrá-las para a proteção do Azure Site Recovery, da seguinte maneira:
1. Verifique se que você tem nuvens primárias e secundárias. Verifique se o servidor Hyper-V que hospeda a máquina virtual existente está localizado em nuvem primária e o servidor Hyper-V que hospeda a máquina virtual de réplica está localizado na nuvem secundária. Verifique se você definiu as configurações de proteção para as nuvens. As configurações devem corresponder àquelas atualmente definidas para a Réplica do Hyper-V. Caso contrário, a replicação de máquina virtual poderá não funcionar conforme o esperado.
2. Em seguida, habilite a proteção para a máquina virtual primária. O Azure Site Recovery e o VMM garantirão que o mesmo host de réplica e a mesma máquina virtual sejam detectados, e o Azure Site Recovery reutilizará e restabelecerá a replicação usando as configurações definidas durante a configuração de nuvem.


## Testar a implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano.  O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

### Criar um plano de recuperação

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação. Selecione **Hyper-V** para exibir somente as nuvens que estão configuradas para replicação do Hyper-V.

	![Create recovery plan](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP1.png)

3. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo padrão do plano de recuperação - Grupo 1. Você pode adicionar mais grupos se necessário. Observe que, depois da replicação, as máquinas virtuais serão inicializadas de acordo com a ordem dos grupos de plano de recuperação.

	![Add virtual machines](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP2.png)	

Depois que um plano de recuperação ter sido criado, ele aparece na lista da guia **Planos de Recuperação**. 

###Execute um teste de failover

1. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
2. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que com essa opção habilitada a falha em máquinas virtuais de réplica não será conectada a nenhuma rede. Isso irá testar se a máquina virtual falha conforme o esperado, mas não testa seu ambiente de rede de replicação. Se você quiser executar um failover de teste mais abrangente, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testar uma implantação local no MSDN</a>.

	![Select test network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_TestFailover1.png)


7. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.

### Executar um plano de recuperação
Após a replicação a máquina virtual de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, o DNS será atualizado automaticamente. Se você não estiver usando o DHCP e quiser verificar se os endereços são os mesmos, você precisará executar alguns scripts.

#### Script para recuperar o endereço IP
Execute este script de exemplo para recuperar o endereço IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  

#### Script para atualizar o DNS
Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**



<a name="privacy"></a><h2>Informações de privacidade para Site Recovery</h2>

Esta seção fornece informações adicionais de privacidade para o serviço Microsoft Azure Site Recovery ("Serviço"). Para exibir a política de privacidade para serviços do Microsoft Azure, consulte a
[Política de Privacidade do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Recurso: Registro**

- **O que ele faz**: Registra o servidor no serviço para que as máquinas virtuais possam ser protegidas
- **Informações coletadas**: Após o registro, o Serviço coleta, processa e transmite as informações de certificado de gerenciamento do servidor VMM designadas para fornecer recuperação de desastres usando o nome do Serviço do servidor VMM e o nome de nuvens de máquinas virtuais no servidor VMM.
- **Uso de informações**: 
	- Certificado de gerenciamento: usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o Serviço. O Serviço usa a parte da chave pública do certificado para proteger um token que somente o servidor VMM registrado pode acessar. O servidor precisa usar esse token para obter acesso aos recursos do Serviço.
	- Nome do servidor VMM: o nome do servidor VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas.
	- Nomes de nuvem do servidor VMM: o nome de nuvem é necessário ao se usar o recurso de emparelhamento/desemparelhamento da nuvem do Serviço descrito abaixo. Quando você decide emparelhar sua nuvem de um data center primário com outra nuvem no data center de recuperação, os nomes de todas as nuvens do data center de recuperação são apresentados.

- **Opção**: Essa informação é uma parte essencial do processo de registro do Serviço porque ajuda você e o Serviço a identificar o servidor VMM para o qual você deseja fornecer a proteção do Azure Site Recovery, além de identificar o servidor VMM registrado correto. Se você não quiser enviar essas informações ao Serviço, não use esse Serviço. Se registrar seu servidor e, mais tarde, quiser cancelar o registro, você poderá fazer isso excluindo as informações do servidor VMM do portal do Serviço (que é o portal do Azure).

**Recurso: Habilitar a proteção do Azure Site Recovery**

- **O que ele faz**: O Provedor do Azure Site Recovery instalado no servidor VMM é o canal de comunicação com o serviço. O Provedor é uma DLL (biblioteca de vínculo dinâmico) hospedada no processo do VMM. Depois que o Provedor é instalado, o recurso "Recuperação do Data Center" é habilitado no console do administrador do VMM. Máquinas virtuais novas ou existentes em uma nuvem podem habilitar uma propriedade chamada "Recuperação do Data Center" para ajudar a proteger a máquina virtual. Quando essa propriedade é definida, o Provedor envia o nome e a ID da máquina virtual ao Serviço. A proteção virtual é habilitada pela tecnologia de replicação do Hyper-V do Windows Server 2012 ou Windows Server 2012 R2. Os dados da máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center de "recuperação" diferente).

- **Informações coletadas**: O serviço coleta, processa e transmite metadados para a máquina virtual, que incluem o nome, a ID, a rede virtual e o nome da nuvem à 
qual ele pertence.

- **Uso de informações**: O serviço usa as informações acima para popular as informações da máquina virtual no portal do Serviço.

- **Opção**: Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao serviço, não habilite a proteção do Azure Site Recovery para máquinas virtuais. Observe que todos os dados enviados pelo Provedor ao Serviço são enviados via HTTPS.

**Recurso: Plano de recuperação**

- **O que ele faz**: Esse recurso o ajuda a criar um plano de orquestração para o data center de "recuperação". Você pode definir a ordem na qual as máquinas virtuais ou um grupo de máquinas virtuais devem ser iniciados no local de recuperação. Você também pode especificar scripts automatizados para execução ou qualquer ação manual a ser executada no momento da recuperação para cada máquina virtual. O failover (abordado na próxima seção) costuma ser disparado no nível do Plano de Recuperação para a recuperação coordenada.

- **Informações coletadas**: O serviço coleta, processa e transmite metadados para o plano de recuperação, incluindo metadados de máquina virtual e metadados de scripts de automação e anotações de ações manuais.

- **Uso de informações**: Os metadados descritos acima são usados para criar o plano de recuperação no portal do Serviço.

- **Opção**: Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao serviço, não crie Planos de Recuperação nesse Serviço.

**Recurso: Mapeamento de rede**

- **O que ele faz**: Esse recurso permite que você mapeie as informações de rede do data center principal para o data center de recuperação. Quando as máquinas virtuais são recuperadas no site de recuperação, esse mapeamento ajuda a estabelecer a conectividade de rede para elas.

- **Informações coletadas**: Como parte do recurso de mapeamento de rede, o Serviço coleta, processa e transmite os metadados das redes lógicas para cada site (primário e data center).

- **Uso de informações**: o serviço usa os metadados para popular o portal do Serviço, em que você pode mapear as informações de rede.

- **Opção**: Essa é uma parte essencial do Serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao Serviço, não use o recurso de mapeamento de rede.

**Recurso: Failover: planejado, não planejado, teste**

- **O que ele faz**: Esse recurso ajuda o failover de uma máquina virtual de um data center gerenciado do VMM para outro data center gerenciado do VMM. A ação de failover é disparada pelo usuário em seu portal do Serviço. As possíveis razões para um failover incluem um evento não planejado (por exemplo, no caso de desastre natural; um evento planejado (por exemplo, o balanceamento de carga do data center); um failover de teste (por exemplo, um ensaio de plano de recuperação).

O Provedor no servidor VMM é notificado do evento pelo Serviço e executa uma ação de failover no host Hyper-V por meio das interfaces do VMM. O failover real da máquina virtual de um host Hyper-V para outro (normalmente em execução em um data center de "recuperação" diferente) é tratado pela tecnologia de replicação do Hyper-V do Windows Server 2012 ou Windows Server 2012 R2. Depois que o failover for concluído, o Provedor instalado no servidor VMM do data center de "recuperação" enviará as informações de êxito ao serviço.

- **Informações coletadas**: O Serviço usa as informações acima para popular o status das informações de ação de failover no portal do Serviço.

- **Uso de informações**: O Serviço usa as informações acima da seguinte forma:

	- Certificado de gerenciamento: usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o Serviço. O Serviço usa a parte da chave pública do certificado para proteger um token que somente o servidor VMM registrado pode acessar. O servidor precisa usar esse token para obter acesso aos recursos do Serviço.
	- Nome do servidor VMM: o nome do servidor VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas.
	- Nomes de nuvem do servidor VMM: o nome de nuvem é necessário ao se usar o recurso de emparelhamento/desemparelhamento da nuvem do Serviço descrito abaixo. Quando você decide emparelhar sua nuvem de um data center primário com outra nuvem no data center de recuperação, os nomes de todas as nuvens do data center de recuperação são apresentados.

- **Opção**: Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao Serviço, não use esse Serviço.


<!--HONumber=49--> 