<properties
   pageTitle="Descrição de cluster do Balanceador do Recursos | Microsoft Azure"
   description="Descrevendo um cluster do Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó do Balanceador de Recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Descrevendo um cluster do Service Fabric

O Balanceador de Recursos no Service Fabric do Azure fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Balanceador de Recursos usa essa informação para posicionar os serviços de forma a assegurar alta disponibilidade dos serviços em execução no cluster, ao mesmo tempo assegurando a máxima utilização dos recursos do cluster. As características do Balanceador de Recursos que descrevem um cluster são domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó. Além disso, o Balanceador de Recursos tem algumas opções de configuração que podem melhorar seu desempenho.

## Principais conceitos

### Domínios de falha

Os domínios de falha permitem que os administradores de cluster definam os nós físicos que têm probabilidade de apresentar falha ao mesmo tempo devido às dependências físicas compartilhadas, como fontes de energia e rede. Normalmente, os domínios de falha representam hierarquias relacionadas a essas dependências compartilhadas, com nós mais propensos a falhar juntos em um ponto mais alto na árvore do domínio de falha. A figura a seguir mostra vários nós organizados por meio de domínios de falha hierárquicos na ordem de datacenter, rack e folha.

![Nós organizados por meio de domínios de falha][Image1]

 Durante o tempo de execução, o Gerenciador de Recursos do Azure considera os domínios de falha no cluster e tenta distribuir as réplicas de um determinado serviço para que elas fiquem em domínios de falha separados. Esse processo ajuda a garantir que, em caso de falha de qualquer domínio de falha, a disponibilidade do serviço e seu estado não sejam comprometidos. A figura a seguir mostra as réplicas de um serviço que são distribuídas entre vários domínios de falha, embora haja espaço suficiente para concentrá-las em apenas um ou dois domínios.

![Réplicas de um serviço distribuídas em domínios de falha][Image2]

Os domínios de falha são configurados no manifesto do cluster. Cada nó é definido para estar dentro de um domínio de falha específico. Durante o tempo de execução, o Gerenciador de Recursos combina os relatórios de todos os nós para apresentar uma visão geral completa de todos os domínios de falha no sistema.

### Domínios de atualização

Os domínios de atualização são outro tipo de informação consumido pelo Gerenciador de Recursos. Assim como os domínios de falha, os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações quase ao mesmo tempo. Os domínios de atualização não são hierárquicos e você pode pensar neles como marcas.

Enquanto os domínios de falha são definidos pelo layout físico dos nós no cluster, os domínios de atualização são determinados pelos administradores de cluster com base em políticas. As políticas são relacionadas às atualizações no cluster. Quanto mais domínios de atualização, mais granulares são as atualizações, o que limita o impacto sobre o cluster e os serviços em execução e impede que as falhas afetem um grande número de serviços. Dependendo de outras políticas, como o tempo que o Service Fabric deve aguardar após a atualização de um domínio antes de passar para o próximo domínio, mais domínios de atualização também podem aumentar o tempo para concluir uma atualização no cluster.

Por esses motivos, o Gerenciador de Recursos coleta informações do domínio de atualização e distribui réplicas entre os domínios de atualização em um cluster, assim como os domínios de falha. Os domínios de atualização podem ou não corresponder um a um aos domínios de falha, mas, geralmente, essa correspondência individual não é esperada. A figura a seguir mostra uma camada de vários domínios de atualização sobre domínios de falha definidos anteriormente. O Gerenciador de Recursos ainda distribui as réplicas entre domínios para que nenhum domínio de atualização ou falha se concentre com réplicas, a fim de garantir alta disponibilidade do serviço, apesar das atualizações ou falhas no cluster.

![Domínios de atualização][Image3]

Os domínios de atualização e os domínios de falha são configurados como parte da definição de nó no manifesto do cluster, conforme mostrado abaixo:

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
> [AZURE.NOTE]Em implantações do Azure, domínios de falha e domínios de atualização são atribuídos pelo Azure. Portanto, a definição dos seus nós e funções na opção infraestrutura do Azure não inclui informações sobre o domínio de falha ou o domínio de atualização.

### Propriedades de nó
As propriedades de nó são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó incluem se o nó tem um disco rígido ou placa de vídeo, o número de eixos no seu disco rígido, núcleos e outras propriedades físicas.

As propriedades de nó também podem ser usadas para especificar propriedades mais abstratas que auxiliam na tomada de decisões sobre política. Por exemplo, vários nós em um cluster podem ser atribuídos a uma “cor”, como meio de segmentar o cluster em diferentes seções. O exemplo de código a seguir mostra que propriedades de nó são definidas para nós por meio do manifesto do cluster como parte das definições de tipo de nó. As propriedades de nó então podem ser aplicadas a vários nós no cluster.

As propriedades de posicionamento NodeName, NodeType, FaultDomain e UpgradeDomain têm valores padrão. O Service Fabric fornece automaticamente valores padrão para que você possa usá-los na criação do serviço. Os usuários não devem especificar suas próprias propriedades de posicionamento com esses mesmos nomes.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

Durante o tempo de execução, o Balanceador de Recursos usa informações da propriedade de nó para assegurar que os serviços que exigem recursos específicos sejam colocados em nós apropriados.

### Capacidades de nó
As capacidades de nó são pares de chave/valor que definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. O exemplo de código a seguir mostra um nó que tem capacidade para uma métrica chamada "MemoryInMb" e possui 2.048 MB de memória disponível por padrão. As capacidades são definidas por meio do manifesto do cluster, bem como as propriedades de nó.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Capacidade de nó][Image4]

Uma vez que os serviços que são executados em um nó podem atualizar seus requisitos de capacidade usando a Carga de Relatório, o Balanceador de Recursos também verifica periodicamente se um nó está em sua capacidade normal ou acima dela em relação a qualquer uma de suas métricas. Se estiver, o Balanceador de Recursos poderá mover serviços para nós menos carregados para diminuir a contenção de recursos e aprimorar o desempenho e a utilização gerais.

Observe que, embora uma determinada métrica também possa ser listada na seção de propriedade do tipo de nó, será melhor listá-la como uma capacidade se ela for uma propriedade do nó que possa ser consumida durante o tempo de execução. Listá-la também como uma propriedade permite que um serviço que depende de "requisitos mínimos de hardware" consulte o nó com restrições de posicionamento. Isso pode ser necessário se o recurso for consumido por outros serviços durante o tempo de execução. É recomendável incluí-la também como uma capacidade para que o Balanceador de Recursos possa tomar medidas adicionais.

Quando novos serviços são criados, o Balanceador de Recursos do Cluster do Service Fabric usa as informações sobre a capacidade de nós existentes e o consumo de serviços existentes para determinar se há capacidade disponível suficiente para colocar todo o novo serviço. Se não houver capacidade suficiente, a solicitação Criar Serviços será rejeitada com uma mensagem de erro explicando o problema de capacidade.


### Configurações do Balanceador de Recursos

No manifesto do cluster, os valores de configuração a seguir definem o comportamento geral do Balanceador de Recursos.

*Limites de balanceamento* controlam quão desequilibrado o cluster pode se tornar com relação a uma métrica específica antes de o Balanceador de Recursos reagir. Um limite de balanceamento é a taxa máxima entre os nós usados de modo máximo e mínimo que o Balanceador de Recursos permite que existam antes de reequilibrar os clusters.

A figura a seguir mostra dois exemplos em que o limite de balanceamento para a métrica fornecida é 10.

![Limite de balanceamento][Image5]

Observe que, neste momento, a "utilização" em um nó não leva em consideração o tamanho do nó, conforme determinado pela capacidade do nó. Refere-se apenas ao uso absoluto atualmente relatado no nó para a métrica especificada.

O exemplo de código a seguir mostra que os limites de balanceamento das métricas são configurados por métrica pelo elemento FabricSettings no manifesto do cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

Os *limites de atividade* atuam como uma barreira de frequência com que o Balanceador de Recursos é executado ao limitar os casos em que o Balanceador de Recursos reage quando uma quantidade absoluta significativa de carga está presente. Dessa forma, se o cluster não estiver muito ocupado para uma métrica específica, o Balanceador de Recursos não será executado, mesmo que essa pequena quantidade de métrica esteja muito desequilibrada no cluster. Essa medida impede o desperdício de recursos reequilibrando o cluster para ganho substancialmente pequeno. A figura a seguir mostra que o limite de balanceamento para a métrica é 4 e o limite de atividade é 1.536.

![Limite de atividade][Image6]

Observe também que os limites de atividade e balanceamento devem ser excedidos para a mesma métrica para que o Balanceador de Recursos seja executado. O disparo para duas métricas separadas não faz com que o Balanceador de Recursos seja executado.

O exemplo de código a seguir mostra que, assim como os limites de balanceamento, os limites de atividade são configurados por métricas pelo elemento FabricSettings no manifesto do cluster.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – controla com que frequência o Balanceador de Recursos examina as informações que ele deve verificar em busca de violações de restrição. Violações de restrição incluem restrições de posicionamento não atendidas, serviços com número inferior ao necessário de instâncias ou réplicas, nós acima da capacidade para alguma métrica, domínios de atualização ou falha sobrecarregados ou desequilíbrios de cluster. O balanceador de recursos identifica as violações de restrição usando os limites de balanceamento e atividade e sua exibição atual da carga nos nós no cluster. O intervalo de atualização é definido em segundos, e o padrão é 1. Frequência de verificação de restrição e posicionamento também podem ser controlados por dois novos intervalos (MinConstraintCheckInterval e MinPlacementInterval). Se os novos parâmetros estiverem definidos, PLBRefreshInterval não será usado e não poderá ser definido.

- MinConstraintCheckInterval – controla com que frequência o Balanceador de Recursos examina as informações que deve verificar em busca de violações de restrição. Violações de restrição incluem restrições de posicionamento não atendidas, serviços com número inferior ao necessário de instâncias ou réplicas, nós acima da capacidade para alguma métrica, domínios de atualização ou falha sobrecarregados ou desequilíbrios de cluster. O balanceador de recursos identifica as violações de restrição usando os limites de balanceamento e atividade e sua exibição atual da carga nos nós no cluster. O intervalo da verificação de restrição é definido em segundos. Se o intervalo de verificação de restrição não estiver definido, seu valor padrão será igual ao valor de PLBRefreshInterval. (Os dois valores não podem ser especificados ao mesmo tempo.)

- MinPlacementInterval – controla com que frequência o Balanceador de Recursos verifica se há novas instâncias ou réplicas que precisam ser posicionadas e tenta posicioná-las. O intervalo de posicionamento é definido em segundos. Se o intervalo de posicionamento não estiver definido, o valor padrão será igual ao valor de PLBRefreshInterval. (Os dois valores não podem ser especificados ao mesmo tempo.)

- MinLoadBalancingInterval – estabelece a quantidade mínima de tempo entre as sessões do balanceamento de recursos. Se o Balanceador de Recursos tiver realizado alguma ação com base nas informações da verificação executada durante o último intervalo de PLBRefreshInterval, ele não será executado novamente por pelo menos o mesmo período de tempo. O intervalo é definido em segundos, e o padrão é 5.

Observe que esses valores são agressivos, mas esse balanceamento de carga geral ocorrerá no cluster apenas se os limites de balanceamento e atividade forem atendidos para uma determinada métrica. O exemplo de código a seguir mostra que, se o balanceamento de carga ativo e preciso não for exigido para um cluster específico, será possível tornar esses valores menos agressivos por meio da configuração no elemento FabricSettings. Nesta configuração de exemplo, o tempo mínimo entre duas verificações de restrição é de 10 segundos e o tempo mínimo para o posicionamento é de 5 segundos, enquanto o balanceamento de carga ocorrerá a cada 5 minutos. Observe que, nesse caso, PLBRefreshInterval não está definido.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

No segundo exemplo de configuração abaixo, temos PLBRefreshInterval e MinLoadBalancingInterval definidos. Como PLBRefreshInterval está definido para 2 segundos, MinPlacementInterval e MinConstraintCheckInterval terão seu valor definido para 2 segundos também.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png

<!---HONumber=AcomDC_1223_2015-->