<properties
   pageTitle="Visão Geral do Gerenciador de Tráfego"
   description="Este artigo contém a visão geral técnica do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/23/2015"
   ms.author="joaoma" />

# Visão Geral do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para seus pontos de extremidade especificados, o que pode incluir serviços de nuvem do Azure, sites e outros pontos de extremidade. O Gerenciador de Tráfego funciona aplicando um mecanismo de políticas inteligente às consultas DNS (Domain Name System) para os nomes de domínio de seus recursos da Internet. Seus serviços de nuvem ou sites do Azure podem ser executados em datacenters diferentes em todo o mundo.

O Gerenciador de Tráfego pode ajudá-lo a:

- **Aumentar a disponibilidade de aplicativos críticos:** O Gerenciador de Tráfego permite aumentar a disponibilidade de seus aplicativos críticos monitorando seus pontos de extremidade no Azure e fornecendo recursos de failover automático quando um serviço de nuvem do Azure, um site do Azure ou outro local é desativado.

- **Melhorar a capacidade de resposta de aplicativos de alto desempenho:** O Azure permite executar serviços de nuvem ou sites em data centers espalhados pelo mundo inteiro. O Gerenciador de Tráfego pode melhorar a capacidade de resposta de seus aplicativos e os tempos de entrega de conteúdo direcionando os usuários finais para o ponto de extremidade com a menor latência de rede do cliente.

- **Atualizar e realizar a manutenção de serviço sem tempo de inatividade:** O Gerenciador de Tráfego dá suporte a cenários estendidos para implantações locais e de nuvem híbrida, incluindo os cenários "disparar para a nuvem", "migrar para a nuvem" e "failover para a nuvem". Para manutenção planejada, você desabilita o ponto de extremidade no Gerenciador de Tráfego e aguarda até que o ponto de extremidade conclua a manutenção de conexões existentes. Quando não houver mais tráfego para o ponto de extremidade, atualize o serviço nesse ponto de extremidade, teste-o e reative-o no Gerenciador de Tráfego. Isso o ajuda a manter e atualizar seus serviços sem tempo de inatividade para clientes.

- **Distribuição de tráfego para implantações grandes e complexas:** Com perfis do Gerenciador de Tráfego aninhados, em que um perfil do Gerenciador de Tráfego pode ter outro perfil do Gerenciador de Tráfego como um ponto de extremidade, você pode criar configurações para otimizar o desempenho e a distribuição para implantações maiores e mais complexas. Para obter mais informações, consulte [Perfis aninhados](#Nested).

## Como funciona o Gerenciador de Tráfego

Quando você configura um perfil do Gerenciador de Tráfego, as configurações que você especifica fornecem ao Gerenciador de Tráfego as informações necessárias para determinar qual ponto de extremidade deve atender à solicitação com base em uma consulta DNS. Nenhum tráfego real de ponto de extremidade é roteado por meio do Gerenciador de Tráfego.

A *Figura 1* mostra como o Gerenciador de Tráfego direciona os usuários para um de um conjunto de pontos de extremidade. Os números na Figura 1 correspondem às descrições numeradas abaixo:

![How Traffic Manager works](./media/traffic-manager/IC740854.jpg)

Figura 1

1. **Tráfego de usuário para nome de domínio da empresa:** o cliente solicita informações usando o nome de domínio da empresa. A meta é resolver um nome DNS para um endereço IP. Os domínios da empresa devem ser reservados por meio de registros de nome de domínio da Internet normais que são mantidos fora do Gerenciador de Tráfego. Na Figura 1, o domínio da empresa de exemplo é www.contoso.com.
1. **Nome de domínio de empresa para o nome de domínio do Gerenciador de Tráfego:** o registro de recurso DNS para o domínio da empresa aponta para um nome de domínio do Gerenciador de Tráfego mantido no Gerenciador de Tráfego do Azure. Isso é obtido por meio de um registro de recurso CNAME que mapeia o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego. No exemplo, o nome de domínio do Gerenciador de Tráfego é contoso.trafficmanager.net.
1. **Nome de domínio e perfil do Gerenciador de Tráfego:** o nome de domínio do Gerenciador de Tráfego faz parte do perfil do Gerenciador de Tráfego. O servidor DNS do usuário envia uma nova consulta DNS ao nome de domínio do Gerenciador de Tráfego (em nosso exemplo, contoso.trafficmanager.net), que é recebida pelos servidores de nomes DNS do Gerenciador de Tráfego.
1. **Regras de perfil do Gerenciador de Tráfego processadas:** o Gerenciador de Tráfego usa o método de balanceamento de carga especificado e o monitoramento de status para determinar qual ponto de extremidade do Azure ou outro ponto de extremidade deve atender à solicitação.
1. **Nome de domínio de ponto de extremidade enviado ao usuário:** o Gerenciador de Tráfego retorna um registro CNAME que mapeia o nome de domínio do Gerenciador de Tráfego para o nome de domínio do ponto de extremidade. O servidor DNS do usuário resolve o nome de domínio do ponto de extremidade para seu endereço IP e o envia ao usuário.
1. **Usuário chama o ponto de extremidade:** o usuário chama o ponto de extremidade retornado diretamente usando o seu endereço IP.

Como o domínio da empresa e o endereço IP resolvido são armazenados em cache no computador cliente, o usuário continua a interagir com o ponto de extremidade escolhido até que sua entrada de cache DNS local expire. É importante observar que o cliente DNS armazena em cache as entradas do host DNS pela duração de sua TTL (vida útil). A recuperação de entradas de host do cache do cliente DNS ignora o perfil do Gerenciador de Tráfego e poderá sofrer atrasos na conexão se o ponto de extremidade se tornar indisponível antes que a TTL expire. Se a TTL de uma entrada de host DNS no cache expirar e o computador cliente precisar resolver o nome de domínio da empresa novamente, ele enviará uma nova consulta DNS. O computador cliente pode receber o endereço IP de um ponto de extremidade diferente dependendo do método de balanceamento de carga aplicado e da integridade dos pontos de extremidade no momento da solicitação.

## Como implementar o Gerenciador de Tráfego


A *Figura 2* mostra as etapas, em ordem, que são necessárias para implementar o Gerenciador de Tráfego. Essas etapas poderão ser executadas em uma ordem ligeiramente diferente depois que você tiver uma compreensão sólida das práticas recomendadas e da configuração do Gerenciador de Tráfego. Os números na Figura 2 correspondem às descrições numeradas abaixo:

![How to configure Traffic Manager](./media/traffic-manager/IC740855.jpg)

Figura 2

1. **Implante os serviços de nuvem do Azure, os sites do Azure ou outros pontos de extremidade em seu ambiente de produção.** Quando você cria um perfil do Gerenciador de Tráfego, ele deve ser associado a uma assinatura. Em seguida, você adiciona pontos de extremidade para serviços de nuvem e sites de camada Padrão em produção que fazem parte da mesma assinatura. Se um ponto de extremidade estiver em preparo e não estiver em um ambiente de produção do Azure ou não estiver na mesma assinatura, ele poderá ser adicionado como um ponto de extremidade externo. Para obter mais informações sobre serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
1. **Escolha um nome para o domínio do Gerenciador de Tráfego.** Considere um nome para seu domínio com um prefixo exclusivo. A última parte do domínio, trafficmanager.net, é fixa. Para obter mais informações, consulte [Práticas recomendadas](#bkmk_TrafficManagerBestPracticesProfile).
1. **Decida qual configuração de monitoramento você deseja usar.** Gerenciador de Tráfego monitora pontos de extremidade para garantir que eles estejam online, independentemente do método de balanceamento de carga. Depois que você definir as configurações de monitoramento, o Gerenciador de Tráfego não direcionará o tráfego para pontos de extremidade que estejam offline de acordo com o sistema de monitoramento, a menos que ele detecte que todos os pontos de extremidade estão offline ou não possa detectar o status de nenhum ponto de extremidade contido no perfil. Para obter mais informações sobre monitoramento, consulte [Sobre monitoramento do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/dn339013.aspx).
1. **Decida qual método de balanceamento de carga você deseja usar.** Três métodos de balanceamento de carga diferentes estão disponíveis. Reserve algum tempo para entender o método que atende melhor às suas necessidades. Se precisar alterar o método posteriormente, você poderá fazer isso a qualquer momento. Observe também que cada método requer etapas de configuração ligeiramente diferentes. Para obter informações sobre os métodos de balanceamento de carga, consulte [Sobre os métodos de balanceamento de carga do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/dn339010.aspx).
1. **Crie seu perfil e defina as configurações.** Você pode usar APIs REST, o Windows PowerShell ou o Portal de Gerenciamento para criar o perfil do Gerenciador de Tráfego e definir as configurações. Para obter mais informações, consulte [Como definir as configurações do Gerenciador de Tráfego](#Configure). As etapas a seguir supõem que você usará a Criação Rápida no Portal de Gerenciamento.
	- 	**Crie seu perfil do Gerenciador de Tráfego -** para criar um perfil usando a Criação Rápida no Portal de Gerenciamento, consulte [Criar um perfil do Gerenciador de Tráfego usando a Criação Rápida](https://msdn.microsoft.com/library/azure/dn339012.aspx).
	- 	**Defina as configurações do método balanceamento de carga -** na Criação Rápida, você deve selecionar o método de balanceamento de carga para seu perfil. Essa configuração pode ser alterada a qualquer momento após a conclusão das etapas de Criação Rápida. Para etapas de configuração, consulte o tópico que corresponde a seu método de balanceamento de carga: [Configurar o Balanceamento de Carga de Desempenho](https://msdn.microsoft.com/library/azure/hh744835.aspx)
	
	[AZURE.NOTE]**O método Round Robin de balanceamento de carga agora dá suporte à distribuição ponderada do tráfego de rede. No entanto, neste momento você deve usar APIs REST ou o Windows PowerShell para configurar o peso. Para obter mais informações e um exemplo de configuração, consulte [Pontos de extremidade externos do Gerenciador de Tráfego do Azure e Round Robin ponderado por meio do PowerShell](https://msdn.microsoft.com/library/azure/hh744829.aspx) no blog do Azure.**
	- 	**Configure os pontos de extremidade -** os pontos de extremidade não são configurados durante a Criação Rápida. Depois de criar seu perfil e especificar o método de balanceamento de carga, você deve informar ao Gerenciador de Tráfego os pontos de extremidade.
	- 	**Defina as configurações de monitoramento -** as configurações de monitoramento não são definidas durante a Criação Rápida. Após criar o perfil e especificar o método de balanceamento de carga, você deve informar ao Gerenciador de Tráfego o que monitorar. Para obter as etapas configurar o monitoramento, consulte [Configurar o monitoramento do Gerenciador de Tráfego](configure-traffic-manager-monitoring.md).
1. **Teste o perfil do Gerenciador de Tráfego.** Teste se o perfil e o domínio estão funcionando conforme o esperado. Para obter informações sobre como fazer isso, consulte [Testando as configurações do Gerenciador de Tráfego](testing-traffic-manager-settings.md).
1. **Aponte o registro de recurso DNS do nome domínio da empresa para o perfil para ativá-lo.** Para obter mais informações, consulte [Apontar um domínio de Internet de empresa para um domínio do Gerenciador de Tráfego](point-a-company-internet-domain-to-a-traffic-manager-domain.md).

Usando o exemplo na Figura 1, você alteraria o registro de recurso DNS em seus servidores para o seguinte a fim de apontar o nome de domínio da empresa para o nome de domínio do Gerenciador de Tráfego:

    www.contoso.com IN CNAME contoso.trafficmanager.net


## Como definir as configurações do Gerenciador de Tráfego


Você pode definir as configurações do Gerenciador de Tráfego usando o Portal de Gerenciamento, com APIs REST e cmdlets do Windows PowerShell.

Embora cada elemento de API REST não esteja visível no Portal de Gerenciamento, muitas configurações estão disponíveis usando qualquer um dos métodos. Para obter mais informações sobre o uso de APIs REST, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).


Para obter mais informações sobre os cmdlets do Windows PowerShell para o Gerenciador de Tráfego, consulte [Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).


[AZURE.NOTE] **Atualmente, não há suporte para configurar pontos de extremidade externos (tipo = 'Qualquer'), pesos para o método de balanceamento de carga Round Robin e perfis aninhados com o Portal de Gerenciamento. Você deve usar o REST (consulte Criar definição) ou o Windows PowerShell (consulte Add-AzureTrafficManagerEndpoint).**

### Definindo configurações no Portal de Gerenciamento


No Portal de Gerenciamento, você pode criar o perfil do Gerenciador de Tráfego usando a Criação Rápida. A Criação Rápida permite criar um perfil básico. Depois de criar seu perfil, você pode definir configurações adicionais ou editar as configurações que definiu anteriormente. Para obter mais informações sobre a criação do perfil do Gerenciador de Tráfego usando a Criação Rápida, consulte Criar um perfil do Gerenciador de Tráfego usando a Criação Rápida.

[Criar um perfil do Gerenciador de Tráfego usando a Criação Rápida](https://msdn.microsoft.com/library/azure/dn339012.aspx)

Você pode definir as seguintes configurações no Portal de Gerenciamento:

- **Prefixo DNS -** um prefixo exclusivo criado por você. Os perfis são exibidos no Portal de gerenciamento por prefixo.

- **TTL do DNS -** o valor do DNS TTL (vida útil) controla com que frequência o servidor de nomes de cache local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas.

- **Assinatura -** selecione a assinatura à qual seu perfil corresponderá. Observe que essa opção só aparecerá se você tiver várias assinaturas.

- **Método de balanceamento de carga -** a maneira como você deseja que o Gerenciador de Tráfego trate o balanceamento de carga.

- **Ordem de Failover -** ao ser usado o método de balanceamento de carga de failover, a ordem dos pontos de extremidade.

- **Monitoramento -** as configurações de monitoramento contêm o protocolo (HTTP ou HTTPS), a porta e o caminho relativo e nome de arquivo.

### Definindo configurações usando APIs REST


Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando APIs REST. Para obter mais informações, consulte [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Perfil -** um perfil contém um prefixo de nome de domínio que você cria. Cada perfil corresponde à sua assinatura. Você pode criar vários perfis por assinatura. O nome do perfil está visível no Portal de Gerenciamento. O nome que você cria, que está contido no perfil, é conhecido como o Domínio do Gerenciador de Tráfego.

- **Definição -** uma definição contém configurações de política e de monitor. Uma definição corresponde a um perfil. Você pode ter apenas uma definição por perfil. A definição em si não é visível no Portal de Gerenciamento, embora muitas das configurações contidas na definição sejam visíveis e possam ser configuradas no Portal de Gerenciamento.

- **Opções de DNS -** em cada definição, há opções de DNS. É onde a TTL do DNS é configurada.

- **Monitores -** em cada definição, há configurações de monitor. É onde o protocolo, a porta e o caminho relativo e nome de arquivo são configurados. As configurações de monitor são visíveis e podem ser definidas no Portal de Gerenciamento. Para obter mais informações, consulte [Sobre monitoramento do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/dn339013.aspx).

- **Política -** em cada definição, há configurações de política. A política é onde os métodos de balanceamento de carga e pontos de extremidade são especificados. A política em si não é visível no Portal de Gerenciamento, embora algumas das configurações de política sejam visíveis e possam ser definidas no Portal de Gerenciamento. Para obter mais informações, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](about-traffic-manager-balancing-methods.md)..

## Definindo configurações usando o Windows PowerShell

Você pode criar e configurar seu perfil do Gerenciador de Tráfego usando o Windows PowerShell. Para obter mais informações, consulte [Cmdlets do Gerenciador de Recursos do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## <a name=bkmk_TrafficManagerBestPracticesProfile>Best practices</a>

- **Torne os prefixos exclusivos e fáceis de entender -** o nome DNS de seu perfil do Gerenciador de Tráfego deve ser exclusivo. Você pode controlar somente a primeira parte do nome DNS. O nome de domínio do Gerenciador de Tráfego é usado apenas a fins de para identificação e direcionamento de solicitações de cliente. Os computadores cliente nunca exibirão esses nomes para o usuário final. No entanto, os perfis são identificados por esse nome de domínio. Portanto, é importante que você possa identificá-lo rapidamente diferenciando-o de outros nomes de domínio listados no Portal de Gerenciamento.
- **Use pontos para adicionar exclusividade ou tornar os nomes de domínio legíveis -** Você também pode usar pontos para separar partes de seu prefixo de nome de domínio.  Se estiver planejando criar várias políticas no Gerenciador de Tráfego, use uma hierarquia consistente para diferenciar um serviço do outro. Por exemplo, a Contoso tem serviços globais para Web, cobrança e gerenciamento de serviços públicos. As três políticas seriam web.contoso.trafficmanager.net, bill.contoso.trafficmanager.net e util.contoso.trafficmanager.net. Ao configurar serviços de nuvem ou sites, use nomes que incluam o local. Por exemplo, web-us-contoso.cloudapp.net e web-asia-contoso.cloudapp.net. As limitações são aquelas impostas pelo DNS. Pressuponha que um nome de domínio é uma sequência de rótulos separados por pontos (label.label.label.label.etc.). No momento desta documentação, os limites para nomes de domínio no Gerenciador de Tráfego são:
	- Cada rótulo pode ter no máximo 63 caracteres.
	- Você não pode ter mais de 40 rótulos no total. Como dois rótulos são usados por trafficmanager.net, isso deixa 38 para seu prefixo.
	- O nome de domínio inteiro pode ter no máximo 253 caracteres. Lembre-se de que trafficmanager.net usa 19 desses caracteres.
- **TTL do DNS -** o valor TTL do DNS controla com que frequência o servidor de nomes de cache local do cliente consultará o sistema DNS do Gerenciador de Tráfego do Azure para entradas DNS atualizadas. Qualquer alteração que ocorrer no Gerenciador de Tráfego, como alterações de perfil ou de disponibilidade do ponto de extremidade, levará esse período de tempo para ser atualizada em todo o sistema global de servidores DNS. Recomendamos que você mantenha a configuração com o valor padrão de 300 segundos (5 minutos). Um número maior aumenta o tempo pelo qual as respostas DNS do Gerenciador de Tráfego são armazenadas em cache por resolvedores de DNS e clientes, o que reduz a latência geral da consulta DNS. No entanto, se for necessário um failover muito rápido, convém configurar um valor menor.
- **Os pontos de extremidade devem estar em uma única assinatura -** todos os pontos de extremidade devem estar na mesma assinatura em que você está criando o perfil. Você pode adicionar pontos de extremidade de assinaturas diferentes a um perfil como pontos de extremidade externos, mas o Azure não os removerá automaticamente se você desabilitar ou excluir o serviço associado. Como resultado, pontos de extremidade externos permanecem no perfil do Gerenciador de Tráfego, e você continuará a ser cobrado por eles, a menos que os remova manualmente.

-**Apenas serviços de produção -** apenas pontos de extremidade em um ambiente de produção estão disponíveis. Não é possível direcionar para pontos de extremidade em execução em um ambiente de preparo. Observe que, se você executar uma troca de endereço VIP (IP virtual) enquanto um perfil estiver direcionando tráfego, o tráfego usará o ponto de extremidade permutado apenas no ambiente de produção.

-**Nomeie seus pontos de extremidade para que eles possam ser facilmente identificados -** considere o prefixo DNS que você deseja usar. O nome DNS é usado porque é garantido que ele seja exclusivo em uma assinatura, enquanto o nome do serviço de nuvem ou site pode não ser. Para evitar confusão, dê a um serviço de nuvem ou site um nome e prefixo DNS que sejam iguais ou semelhantes. Se você tiver mais de 20 sites e serviços de nuvem, a nomeação inadequada poderá dificultar a localização do ponto de extremidade correto. Além disso, pontos de extremidade nomeados inadequadamente tornarão os perfis difíceis de manter.

-**Todos os pontos de extremidade em um perfil devem atender às mesmas operações e portas -**  se você misturar os pontos de extremidade, será mais provável que um cliente chame um ponto de extremidade que não possa atender à solicitação.

-**Todos os serviços de nuvem em um perfil devem usar as mesmas configurações de monitoramento-**  você pode escolher somente um único caminho e arquivo para monitorar todos os pontos de extremidade em determinada definição. Você pode inserir "/" na caixa de texto de Caminho relativo e nome de arquivo para que o monitoramento tente acessar o caminho e o nome de arquivo padrão.

-**Desabilite pontos de extremidade para alterações temporárias, em vez de alterar sua configuração -**  em muitos casos, talvez seja conveniente colocar um ponto de extremidade offline. Em vez de remover o ponto de extremidade de seu perfil, basta desabilitar o ponto de extremidade individual em seu perfil em vez disso. Isso mantém o ponto de extremidade como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, você poderá habilitá-lo. Para obter mais informações, consulte [Desabilitar ou habilitar um ponto de extremidade](disable-or-enable-an-endpoint.md).

-**Armazenamento -**  a maneira como você cria o local e a distribuição do armazenamento é uma consideração importante ao usar o Gerenciador de Tráfego. Considere a transação de ponta a ponta e como seus dados fluirão quando você criar e implantar seus aplicativos para o Gerenciador de Tráfego.

-**SQL Azure -** De forma semelhante ao design de armazenamento, analise suas necessidades de estado e os requisitos de dados ao estender seus pontos de extremidade a várias regiões geográficas.

## <a name="Nested">Perfis aninhados</a>

Você pode especificar o nome de outro perfil do Gerenciador de Tráfego como um ponto de extremidade, uma prática conhecida como perfis aninhados. O nome de perfil do Gerenciador de Tráfego é seu nome DNS, como contoso-europe.trafficmanager.net.

Isso permite que você configure o Gerenciador de Tráfego para que as consultas de nome DNS de entrada sejam analisadas em um conjunto de camadas a fim de garantir que o cliente solicitante seja direcionado para o conjunto correto de pontos de extremidade. A figura 3 mostra um exemplo.

![Example of nested Traffic Manager profiles](./media/traffic-manager/IC751072.png)

**Figura 3**

Você pode aninhar até 10 níveis, e cada perfil pode ser configurado com um método de balanceamento de carga diferente.

Por exemplo, você poderia criar uma configuração para o seguinte:

- Na camada superior (perfil do Gerenciador de Tráfego que é mapeado para o nome DNS externo), você poderá configurar o perfil com o método de balanceamento de carga de desempenho.

- Na camada intermediária, um conjunto de perfis do Gerenciador de Tráfego representa data centers diferentes e usa o método de balanceamento de carga Round Robin.

- Na camada inferior, um conjunto de pontos de extremidade de serviço de nuvem em cada data center atende às solicitações de tráfego do usuário.

O resultado é que os usuários são direcionados para um data center regional apropriado com base no desempenho e para um serviço de nuvem nesse data center com base na distribuição de carga igual ou ponderada. Por exemplo, o peso pode ser usado para distribuir um pequeno percentual do tráfego para uma implantação nova ou de teste para testes ou comentários de clientes.

A Figura 4 mostra a configuração.

![Example of multi-tiered Traffic Manager profiles](./media/traffic-manager/IC751073.png)

**Figura 4**

Na Figura 4, o perfil do Gerenciador de Tráfego na camada de nível superior é um perfil pai, e os perfis do Gerenciador de Tráfego na camada intermediária são perfis filhos.

Se o Gerenciador de Tráfego direcionar os usuários para um perfil filho que tenha um pequeno número de pontos de extremidade íntegros, será possível sobrecarregar os pontos de extremidade e causar problemas de desempenho. Para evitar essa situação, você pode configurar o perfil pai do Gerenciador de Tráfego com um limite de pontos de extremidade íntegros que determina se qualquer um dos pontos de extremidade nos perfis filhos desse pai pode receber tráfego. Por exemplo, para verificar se há pelo menos três pontos de extremidade íntegros nos perfis filhos, você definiria o valor de limite como 3. No exemplo na Figura 4, você configuraria o perfil do Gerenciador de Tráfego de camada de nível superior para esse limite.

Para adicionar um perfil do Gerenciador de Tráfego como um ponto de extremidade e configurar o número mínimo de pontos de extremidade íntegros, você deve usar REST (consulte [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Você não pode usar o Portal de Gerenciamento.

## Figuras do Gerenciador de Tráfego


Se você quiser obter as figuras deste tópico como slides do PowerPoint para sua própria apresentação sobre o Gerenciador de Tráfego ou para modificá-las para suas próprias finalidades, consulte [Figuras do Gerenciador de Tráfego na documentação do MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Consulte também

[Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operações no Gerenciador de Tráfego (Referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->