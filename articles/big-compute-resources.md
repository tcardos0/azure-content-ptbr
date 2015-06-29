<properties
   pageTitle="Big Compute no Azure: recursos técnicos para Lote e HPC (Computação de Alto Desempenho)."
   description="Este artigo lista os recursos técnicos para ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC no Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="04/28/2015"
   ms.author="danlep"/>

# Big Compute no Azure: recursos técnicos para Lote e HPC (Computação de Alto Desempenho).
Este é um guia para recursos técnicos para ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC no Azure. Amplie suas cargas de trabalho de lote ou HPC existentes para a nuvem do Azure ou crie novas soluções Big Compute no Azure usando diversos serviços do Azure.

## Opções de soluções


Saiba mais sobre as opções do Big Compute no Azure e escolha a abordagem certa para suas necessidades de negócios e cargas de trabalho.

* [Visão geral](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Vídeo: Big Compute na nuvem com o Azure e HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Lote do Azure

O [Lote](http://azure.microsoft.com/services/batch/) (visualização) é um serviço de plataforma que facilita a habilitação de seus aplicativos para a nuvem e a execução de trabalhos sem configurar e gerenciar um cluster e um agendador de trabalhos. Use o SDK para integrar aplicativos clientes ao Lote do Azure por meio de uma variedade de linguagens, preparar dados no Azure e criar pipelines de execução de trabalho.

* Inscreva-se para obter a [visualização](http://azure.microsoft.com/services/preview/)

* [Documentação](http://azure.microsoft.com/documentation/services/batch/)

* [Referência de API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Fórum do Lote](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Tutorial: Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md)

* [Vídeo: Introdução ao Lote do Azure](http://azure.microsoft.com/documentation/videos/introduction-to-azure-batch-with-mark-scurrell/)

* [Vídeo: Demonstração do serviço Lote do Azure por Mark Russinovich](http://azure.microsoft.com/documentation/videos/azure-batch-demo-blender/)

* [Vídeo: Criar um serviço de lote](http://azure.microsoft.com/documentation/videos/azure-batch-build-a-batch-service/)

## Soluções de cluster HPC

Migre ou amplie seu cluster existente do Windows HPC para executar cargas de trabalho com consumo intensivo de computação no Azure.

### Microsoft HPC Pack

O [HPC Pack](https://technet.microsoft.com/library/cc514029) é o gerenciador de cluster gratuito da Microsoft e a solução de agendamento de trabalhos para HPC em nuvem, híbrido e local.

* [Intermitência no Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack em VMs do Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Fóruns do Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) é uma implementação da Microsoft do padrão Message Passing Interface para desenvolver e executar aplicativos paralelos na plataforma Windows.


* [Baixe o MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referência do MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Fórum do MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instâncias de computação intensiva

O Azure oferece uma [variedade de tamanhos](https://msdn.microsoft.com/library/azure/dn197896.aspx), incluindo [instâncias A8, A9, A10 e A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), para executar suas cargas de trabalho do Lote e do HPC.


* [Instâncias A8 e A9: Início Rápido com HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Executar aplicativos MPI em instâncias A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Plano gráfico da arquitetura

* [Computação de grande escala - Serviços financeiros](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) mostra como colocar em operação e orquestrar a análise de dados e a computação de larga escala na nuvem para gerenciamento de risco, relatórios e simulações.

## Exemplos e scripts

* [Exemplos de código do Lote do Azure](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch)

* [Exemplo de misturador de aplicativos do Lote](https://github.com/Azure/azure-batch-apps-blender) e [postagem de blog](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Serviços do Azure relacionados

* [Fábrica de dados](http://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizado de máquina](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Serviços de Nuvem](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Serviços de Mídia](http://azure.microsoft.com/documentation/services/media-services/)



## Próximas etapas

* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Consulte também [o que há de novo no Lote](http://azure.microsoft.com/updates/?service=batch) ou assine o [RSS feed](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=58-->