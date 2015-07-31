<properties 
	pageTitle="Uma visão geral do Apache Spark no HDInsight | Azure" 
	description="Uma introdução ao Apache Spark no HDInsight e cenários nos quais usar o Spark no HDInsight em seus aplicativos." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>

# Visão geral: Apache Spark no Azure HDInsight 
 
O <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> é uma estrutura de processamento paralelo de código-fonte aberto que dá suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina. O Spark também é compatível com o armazenamento de blob do Azure (WASB) para que os dados existentes armazenados no Azure possam ser processados facilmente via Spark.

Quando você provisiona um cluster do Spark no HDInsight, você provisiona recursos de computação do Azure com o Spark instalado e configurado. Demora apenas cerca de dez minutos para provisionar um cluster do Spark no HDInsight. Os dados que serão processados são armazenados no armazenamento de blob do Azure. Consulte [Usar o armazenamento de blob do Azure com HDInsight][hdinsight-storage].

![Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-overview/SparkArchitecture.png "Apache Spark no Azure HDInsight")


**Deseja começar com o Apache Spark no Azure HDInsight?** Consulte [Início rápido: provisionar um cluster do Spark no HDInsight e executar aplicativos de exemplo usando Jupyter e Zeppelin](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).



Assista a um vídeo de visão geral que descreve o Apache Spark no Azure HDInsight.

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## Por que usar velas no Azure HDInsight? 

O Azure HDInsight oferece um serviço de Spark totalmente gerenciado. Os benefícios do uso do Spark no HDInsight são:

| Recurso | Descrição |
|-------------------------------------|-------------------|
| Facilidade de provisionamento | Você pode provisionar um novo cluster do Spark no HDInsight em minutos usando o Portal de Gerenciamento do Azure, Azure PowerShell ou o SDK do HDInsight .NET. Consulte [Provisionar um cluster do Spark no HDInsight](hdinsight-apache-spark-provision-clusters.md) |
| Fácil de usar | Os clusters do Spark no HDInsight incluem blocos de anotações do Zeppelin e Jupyter pré-configurados. Você pode usá-los para visualização e processamento de dados interativo. Você pode iniciar esses blocos de anotações no painel de cluster para funcionar diretamente em um cluster do Spark.|
| APIs REST | O Spark no HDInsight inclui um servidor de trabalho do Spark, que é um servidor de API REST que permite aos usuários enviar e monitorar trabalhos em execução remotamente. |
| Consultas simultâneas | O Spark no HDInsight dá suporte a consultas simultâneas. Isso permite que várias consultas de um usuário ou várias consultas de vários usuários e aplicativos para compartilhar os mesmos recursos de cluster. |
| Armazenamento em cache no SSDs | Você pode escolher os dados em cache na memória ou no SSDs anexado a nós do cluster. O armazenamento em cache na memória oferece o melhor desempenho de consulta, mas pode ser caro; o armazenamento em cache no SSDs fornece uma ótima opção para melhorar o desempenho de consulta sem a necessidade de criar um cluster de um tamanho que é necessário para ajustar o conjunto de dados na memória.|
| Integração com serviços do Azure | O Spark no HDInsight é fornecido com um conector para hubs de eventos do Azure. Os clientes podem criar aplicativos de fluxo contínuo usando os hubs de eventos, além do [Kafka](http://kafka.apache.org/), que já está disponível como parte do Spark. |
| Integração com ferramentas de BI | O Spark para o HDInsight fornece conectores para ferramentas de BI populares como [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop) para análise de dados.|
| Bibliotecas Anaconda pré-carregadas | Os clusters do Spark no HDInsight são fornecidos com bibliotecas Anaconda pré-instaladas. O [Anaconda](http://docs.continuum.io/anaconda/) fornece quase 200 bibliotecas de aprendizado de máquina, análise de dados, visualização, etc.|
| Escalabilidade | Embora você possa especificar o número de nós no cluster durante a criação, convém aumentar ou reduzir o cluster de acordo com a carga de trabalho. Todos os clusters HDInsight permitem que você altere o número de nós no cluster. Além disso, os clusters do Spark podem ser descartados sem perda de dados, pois todos os dados são armazenados no armazenamento de blob do Azure. |
| Suporte contínuo | O Spark no HDInsight é fornecido com suporte contínuo de nível empresarial e um SLA de 99,9% de disponibilidade.|



## Quais são os casos de uso do Spark no HDInsight?

O Apache Spark no HDInsight permite os seguintes cenários principais.

### Análise de dados interativa e BI

[Examinar um tutorial](hdinsight-apache-spark-use-bi-tools.md)

O Apache Spark no HDInsight armazena dados em blobs do Azure. Especialistas de negócios e tomadores de decisões principais podem analisar e criar relatórios com esses dados e usar o Microsoft Power BI para criar relatórios interativos dos dados analisados. Analistas podem iniciar por meio de dados não estruturados/semiestruturados no armazenamento do Azure, definir um esquema para os dados usando blocos de anotações e, em seguida, criar modelos de dados usando o Microsoft Power BI. O Sparks no HDInsight também dá suporte a várias ferramentas de BI de terceiros, como Tableau, Qlikview e SAP Lumira, tornando-o uma plataforma ideal para analistas de dados, especialistas de negócios e tomadores de decisões importantes.

### Aprendizado de Máquina iterativo

[Examinar um tutorial](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

O Apache Spark vem com [MLlib](http://spark.apache.org/mllib/), uma biblioteca aprendizado de máquina criada no Spark. Além disso, o Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com uma variedade de pacotes para o aprendizado de máquina. Junte isso com um suporte interno para blocos de anotações do IPython, e você tem um ambiente de linha superior para a criação de aplicativos de aprendizado de máquina.

### Análise de dados de fluxo contínuo e em tempo real

[Examinar um tutorial](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

A análise de dados em tempo real é usada para cenários que variam de reduzir o tempo a insight de dados ao processar os dados assim que chegam, para criação de uma verdadeira solução de fluxo. O Spark no HDInsight oferece um suporte avançado para criar soluções de análise em tempo real. Embora o Spark já tenha conectores para receber dados de várias fontes, como soquetes TCP, Flume, Twitter, ZeroMQ ou Kafka, o Spark no HDInsight adiciona suporte de primeira classe para a inserção de dados de hubs de eventos do Azure. Os hubs de evento são o serviço de enfileiramento de mensagens mais usado no Azure. Ter um excelente suporte para hubs de eventos faz com que o Spark no HDInsight seja uma plataforma ideal para a criação de pipeline de análise em tempo real.

##<a name="next-steps"></a>Quais componentes estão incluídos como parte de um cluster do Spark?

O Spark no HDInsight inclui os seguintes componentes que estão disponíveis nos clusters por padrão.

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/). Vem com Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Spark Job Server](https://github.com/spark-jobserver/spark-jobserver)
- [Zeppelin Notebook](https://zeppelin.incubator.apache.org)
- [IPython Notebook](https://jupyter.org)

O Spark no HDInsight também fornece um [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para conectividade aos clusters do Spark no HDInsight de ferramentas de BI, como o Microsoft Power BI e o Tableau.

##<a name="see-also"></a>Consulte também

* [Início rápido: use o Spark no HDInsight com o Zeppelin Notebook para executar a análise de dados interativa](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Provisionar um cluster Apache Spark no HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Enviar trabalhos remotamente para um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=July15_HO3-->