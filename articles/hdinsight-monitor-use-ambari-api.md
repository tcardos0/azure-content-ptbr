﻿<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitorar clusters do Hadoop no HDInsight usando a API do Ambari | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Monitorar clusters Hadoop no HDInsight usando a API da Ambari
 
Saiba como monitorar os clusters do HDInsight versões 3.1 e 2.1 usando APIs do Ambari.

**Tempo estimado para conclusão:** 15 minutos


##Neste artigo

- [O que é Ambari?](#whatisambari)
- [Pré-requisitos](#prerequisites)
- [Iniciar rapidamente](#jumpstart)
- [APIs de monitoramento da Ambari](#monitor)
- [Próximas etapas](#nextsteps)


## <a id="whatisambari"></a> O que é Ambari?

[Apache Ambari][ambari-home] serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, consulte [referência de API do Ambari][ambari-api-reference].


Atualmente, o HDInsight oferece suporte apenas ao recurso de monitoramento da Ambari. A API da Ambari v1.0 tem suporte do cluster HDInsight versão 2.1 e 3.0.  Este artigo aborda o acesso às APIs do Ambari em clusters do HDInsight versões 3.1 e 2.1.  A principal diferença entre os dois é que alguns dos componentes foram alterados com a introdução de novos recursos (como o Servidor de histórico de trabalho).


##<a id="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][powershell-script].

	[Curl][curl] é opcional. Ele pode ser instalado de [aqui][curl-download].

	>[WACOM.NOTE] Quando usar o comando curl no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

- **Um cluster Azure HDInsight**. Para obter instruções sobre a provisão do cluster, consulte [Introdução ao uso do Azure HDInsight][hdinsight-get-started] ou [Provisão de clusters HDInsight][hdinsight-provision]. Você precisará dos seguintes dados para percorrer o tutorial:

	<table border="1">
	<tr><th>Propriedade do cluster</th><th>Nome de variável do PowerShell</th><th>Valor</th><th>Descrição</th></tr>
	<tr><td>Nome do cluster HDInsight</td><td>$clusterName</td><td></td><td>O nome do seu cluster HDInsight.</td></tr>
	<tr><td>Nome de usuário do cluster</td><td>$clusterUsername</td><td></td><td>Nome de usuário do cluster especificado na provisão.</td></tr>
	<tr><td>Senha do cluster</td><td>$clusterPassword</td><td></td><td>Senha do usuário do cluster.</td></tr>
	</table>

	> [WACOM.NOTE] Preencha os valores nas tabelas.  Isso poderá ser útil para percorrer este tutorial.



##<a id="jumpstart"></a>Iniciar rapidamente

Há várias maneiras de usar a Ambari para monitorar os clusters HDInsight.

**Usar PowerShell do Azure**

Segue abaixo um script do PowerShell para obter as informações sobre o jobtracker do MapReduce *em um cluster 3.1.*  A principal diferença é que esses detalhes agora serão extraídos do serviço YARN (e não do MapReduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

Segue abaixo um script do PowerShell para obter as informações sobre o jobtracker do MapReduce *em um cluster 2.1*:

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'mapred.JobTracker'

A saída é:

![Jobtracker Output][img-jobtracker-output]

**Usar curl**

A seguir está um exemplo para obter informações sobre cluster usando Curl:

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A saída é:
	
	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Observação para a versão 8/10/2014:
Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* agora retorna o nome de domínio totalmente qualificado (FQDN) do nó ao invés do nome do host apenas. Antes da versão de 8/10/2014, esse exemplo retornava simplesmente "**headnode0**". Após 8/10/2014, o FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**" passou a ser retornado, como mostrado no exemplo acima. Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implementados em uma rede virtual (VNET). Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

##<a id="monitor"></a>APIs de monitoramento da Ambari

A tabela a seguir lista algumas das chamadas de API para monitoramento da Ambari. Para obter mais informações sobre a API, consulte [referência de API do Ambari][ambari-api-reference].

<table border="1">
<tr><th>Monitorar a chamada de API</th><th>URI</th><th>Descrição</th></tr>
<tr><td>Obter clusters</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Obter informações do cluster.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</tt></td><td>clusters, serviços, hosts</td></tr>
<tr><td>Obter serviços</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</tt></td><td>Os serviços incluem: hdfs, mapreduce</td></tr>
<tr><td>Obter informações dos serviços.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</tt></td><td></td></tr>
<tr><td>Obter componentes do serviço</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</tt></td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>Obter informações do componente.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</tt></td><td>ServiceComponentInfo, host-components, métricas</td></tr>
<tr><td>Obter hosts</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Obter informações do host.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td></td></tr>
<tr><td>Obter componentes do host</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Obter informações de componente do host.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</tt></td><td>HostRoles, componente, host, métrica</td></tr>
<tr><td>Obter configurações</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</tt></td><td>Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Obter informações de configuração.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&tag=&lt;VersionName&gt; 
</tt></td><td>Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Próximas etapas 

Você aprendeu como usar as chamadas de API para monitoramento da Ambari. Para obter mais informações, consulte:

- [Administrar clusters HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal]
- [Administrar clusters HDInsight usando o PowerShell do Azure][hdinsight-admin-powershell]
- [Administrar clusters HDInsight usando a interface de linha de comando][hdinsight-admin-cli]
- [Documentação do HDInsight][hdinsight-documentation]
- [Introdução ao HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[Powershell-install]: ../install-configure-powershell/
[Powershell-script]: http://technet.microsoft.com/pt-br/library/ee176949.aspx 

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-documentation]: /pt-br/documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
