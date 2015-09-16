<properties
   pageTitle="Otimize as consultas do Hive para execução mais rápida no HDInsight | Microsoft Azure"
   description="Aprenda a otimizar suas consultas do Hive no HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# Otimizar consultas do Hive para Hadoop no HDInsight

Por padrão, os clusters do Hadoop não são otimizados para desempenho. Este artigo aborda alguns dos métodos de otimização de desempenho do Hive mais comuns que você pode aplicar a nossas consultas.

[AZURE.INCLUDE [portal](../../includes/hdinsight-azure-portal.md)]

* [Otimizar consultas do Hive para Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md).

##Escalar nós de trabalho horizontalmente

O aumento do número de nós de trabalho em um cluster pode aproveitar mais mapeadores e redutores para execução paralela. Há duas maneiras de aumentar a escalabilidade horizontal no HDInsight:

- No momento do provisionamento, você pode especificar o número de nós de trabalho usando o Portal do Azure, o PowerShell do Azure ou a interface de linha de comando entre plataformas. Para saber mais, confira [Provisionar clusters HDInsight](hdinsight-provision-clusters.md). A tela a seguir mostra a configuração de nó de trabalho no Portal do Azure:

	![scaleout\_1][image-hdi-optimize-hive-scaleout_1]
- Em tempo de execução, você também pode escalar um cluster horizontalmente sem recriar um. Isso é mostrado abaixo.![scaleout\_1][image-hdi-optimize-hive-scaleout_2]

Para obter mais detalhes sobre as diferentes máquinas virtuais com suporte no HDInsight, consulte [preços do HDInsight](http://azure.microsoft.com/pricing/details/hdinsight/).

##Habilitar o Tez

O [Apache Tez](http://hortonworks.com/hadoop/tez/) é um mecanismo de execução alternativo ao mecanismo MapReduce:

![tez\_1][image-hdi-optimize-hive-tez_1]


O Tez é mais rápido porque:

- Executa DAG como um único trabalho no mecanismo MapReduce; o DAG expresso exige que cada conjunto de mapeadores seja seguido por um conjunto de redutores. Isso faz com que vários trabalhos do MapReduce sejam gerados para cada consulta do Hive. O Tez não tem essa restrição e pode processar DAG complexo como um único trabalho, minimizando a sobrecarga de inicialização de trabalho.
- **Evita gravações desnecessárias** Devido a vários trabalhos que estão sendo gerados para a mesma consulta do Hive no mecanismo MapReduce, a saída de cada trabalho é gravada no HDFS para dados intermediários. Como o Tez minimiza o número de trabalhos para cada consulta do Hive, ele pode evitar gravação desnecessária.
- **Minimiza atrasos de inicialização** O Tez é mais capaz de minimizar o atraso de inicialização, reduzindo o número de mapeadores de que precisa para ser iniciado, além de aumentar a otimização de maneira geral.
- **Reutiliza contêineres** Sempre que possível, o Tez é capaz de reutilizar contêineres para garantir que a latência devido à inicialização de contêineres seja reduzida.
- **Técnicas de otimização contínua** Tradicionalmente, a otimização ocorria durante a fase de compilação. No entanto, há disponibilidade de mais informações sobre as entradas que permitem maior otimização durante o tempo de execução. O Tez usa técnicas de otimização contínua que permitem otimizar ainda mais o plano mais adiante na fase de tempo de execução.

Para obter mais detalhes sobre esses conceitos, clique [aqui](http://hortonworks.com/hadoop/tez/).

Você pode fazer qualquer consulta do Hive habilitada pelo Tez prefixando a consulta com a configuração abaixo:

	set hive.execution.engine=tez;

O Tez deve estar habilitado no momento do provisionamento. Este é um exemplo de script do PowerShell do Azure para provisionar um cluster do Hadoop com o Tez habilitado:


	$clusterName = "[HDInsightClusterName]"
	$location = "[AzureDataCenter]" #i.e. West US
	$dataNodes = 32 # number of worker nodes in the cluster

	$defaultStorageAccountName = "[DefaultStorageAccountName]"
	$defaultStorageContainerName = "[DefaultBlobContainerName]"
	$defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

	$hdiUserName = "[HTTPUserName]"
	$hdiPassword = "[HTTPUserPassword]"

	$hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
	$hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

	$hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

	New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
	Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
	Add-AzureHDInsightConfigValues -Hive $hiveConfig |
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

## Particionamento do Hive

A operação de E/S é o principal gargalo de desempenho para executar consultas do Hive. O desempenho pode ser melhorado se a quantidade de dados que precisam ser lidos puder ser reduzida. Por padrão, consultas do Hive examinam tabelas inteiras do Hive. Isso é ótimo para consultas, como verificações de tabela, porém, para consultas que só precisam verificar uma pequena quantidade de dados (por exemplo, consultas com filtragem), isso cria uma sobrecarga desnecessária. O particionamento do Hive permite que as consultas do Hive acessem somente a quantidade necessária de dados nas tabelas do Hive.

O particionamento do Hive é implementado reorganizando os dados brutos em novos diretórios, em que cada partição tem seu próprio diretório - onde a partição é definida pelo usuário. O diagrama a seguir ilustra o particionamento de uma tabela do Hive pela coluna *Ano*. Um novo diretório é criado para cada ano.

![particionamento][image-hdi-optimize-hive-partitioning_1]

Algumas considerações sobre particionamento:

- **Não particione pouco demais** - O particionamento em colunas com apenas alguns valores pode causar muito poucas partições. Por exemplo, o particionamento por gênero só criará duas partições (“masculino” e “feminino”), reduzindo a latência no máximo pela metade.

- **Não particionam excessivamente** - No outro extremo, a criação de uma partição em uma coluna com um valor exclusivo (por exemplo, ID de usuário) causará várias partições, provocando muita carga no namenode do cluster, pois ele terá que lidar com a grande quantidade de diretórios.

- **Evite distorção de dados** -Escolha com bom senso sua chave de particionamento para que todas as partições tenham o mesmo tamanho. Um exemplo: o particionamento em *Estado* pode fazer com que o número de registros em “Califórnia” seja quase 30 vezes o número em “Vermont” devido à diferença de população.

Para criar uma tabela de partição, use a cláusula *Particionado por*:

    CREATE TABLE lineitem_part
    	(L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE 	  	 STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
    	 L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Depois de criar a tabela particionada, você pode criar particionamento estático ou dinâmico.

- **Particionamento estático** significa que você já fragmentou os dados nos diretórios apropriados e pode solicitar partições do Hive manualmente com base no local do diretório. Isso é mostrado no trecho de código abaixo.

	    INSERT OVERWRITE TABLE lineitem_part
	    PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
	    SELECT * FROM lineitem 
	    WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

	    ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
	    LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Particionamento dinâmico** significa que você deseja que o Hive crie partições automaticamente para você. Como já criamos a tabela de partição a partir da tabela de preparo, só precisamos inserir dados na tabela particionada, conforme mostrado abaixo:

	    SET hive.exec.dynamic.partition = true;
	    SET hive.exec.dynamic.partition.mode = nonstrict;
	    INSERT INTO TABLE lineitem_part
	    PARTITION (L_SHIPDATE)
	    SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
	    	 L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
	     	 L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
	    	 L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as 	 	 L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as 	 	 L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as 	 L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as 	 L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obter mais detalhes, consulte [Tabelas particionadas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##Use o formato ORCFile

O Hive dá suporte a vários formatos de arquivo. Por exemplo:

- **Texto**: esse é o formato de arquivo padrão e funciona com a maioria dos cenários
- **Avro**: funciona bem para cenários de interoperabilidade
- **ORC/Parquet**: mais adequada para desempenho

O formato ORC é uma maneira altamente eficiente para armazenar dados do Hive. Comparado a outros formatos, o ORC tem as seguintes vantagens:

- suporte para tipos complexos, incluindo a data e hora e tipos complexos e semiestruturados
- até 70% de compactação
- indexa a cada 10.000 linhas, o que permite ignorar linhas de índices
- uma redução significativa no tempo de execução

Para habilitar o formato ORC, primeiro você deve criar uma tabela com a cláusula *Armazenado como ORC*:

    CREATE TABLE lineitem_orc_part
    	(L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
		 L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT 	 STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Em seguida, insira dados na tabela ORC a partir da tabela de preparo. Por exemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
    	   L_SUPPKEY as L_SUPPKEY,
		   L_LINENUMBER as L_LINENUMBER,
     	   L_QUANTITY as L_QUANTITY, 
		   L_EXTENDEDPRICE as L_EXTENDEDPRICE,
    	   L_DISCOUNT as L_DISCOUNT,
		   L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
		   L_LINESTATUS as L_LINESTATUS,
		   L_SHIPDATE as L_SHIPDATE,
		   L_COMMITDATE as L_COMMITDATE,
		   L_RECEIPTDATE as L_RECEIPTDATE, 
		   L_SHIPINSTRUCT as L_SHIPINSTRUCT,
		   L_SHIPMODE as L_SHIPMODE,
		   L_COMMENT as L_COMMENT
    FROM lineitem;

Você pode ler mais sobre o formato ORC [aqui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##Vetorização

A vetorização permite que o Hive processe um lote de 1.024 linhas juntas em vez de processar uma linha por vez. Isso significa que operações simples são concluídas mais rapidamente porque menos código interno precisa ser executado.

Para habilitar a vetorização, prefixe sua consulta do Hive com a seguinte configuração:

    set hive.vectorized.execution.enabled = true;

Para obter mais informações, consulte [Execução de consultas vetorizadas](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##Outros métodos de otimização

Há mais métodos de otimização que você pode considerar, por exemplo:

- **Bucketing do Hive:** uma técnica que permite clusterizar ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
- **Otimização de junção:** otimização do planejamento da execução de consultas do Hive para melhorar a eficiência de junções e reduzir a necessidade de dicas de usuário. Para obter mais informações, consulte [Otimização de junção](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **Aumentar redutores**

##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu a vários métodos comuns de otimização de consultas do Hive. Para saber mais, consulte os seguintes artigos:

- [Usar o Apache Hive no HDInsight](../hdinsight-using-hive.md)
- [Analisar dados de atrasos de voos usando o Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analisar dados do Twitter usando o Hive no HDInsight](hdinsight-analyze-twitter-data.md)
- [Analisar dados de sensor usando o Console de Consulta do Hive no Hadoop no HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Usar o Hive com o HDInsight para analisar logs de sites](../hdinsight-hive-analyze-web-site-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query-v1/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/partitioning_1.png

<!---HONumber=August15_HO8-->