<properties 
   pageTitle="Use o Apache Phoenix e o Squirrel no HDInsight | Azure" 
   description="Saiba como usar o Apache Phoenix no HDInsight e como instalar e configurar o SQuirrel na sua estação de trabalho para se conectar a um cluster HBase no HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/15/2015"
   ms.author="jgao"/>

# Use o Apache Phoenix e o SQuirrel no HDinsight  

Saiba como usar o [Apache Phoenix](http://phoenix.apache.org/) no HDInsight e como instalar e configurar o SQuirrel na sua estação de trabalho para se conectar a um cluster HBase no HDInsight. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html).

>[AZURE.NOTE]Para obter as informações de versão do Phoenix no HDInsight, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][hdinsight-versions].

## Usar o SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

### Pré-requisitos
Antes de poder usar o SQLLine, você deve ter o seguinte:

- **Um cluster HBase no HDInsight**. Para obter informações sobre como provisionar o cluster HBase, consulte [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started].
- **Conectar-se ao cluster HBase por meio do protocolo de área de trabalho remoto**. Para obter instruções, consulte [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure][hdinsight-manage-portal].

**Para descobrir o nome do host**

1. Abra **Linha de comando do Hadoop** na área de trabalho.
2. Execute o comando a seguir

		ipconfig

	Execute **Connection-specific DNS Suffix**. Por exemplo, *myhbasecluster.f5.internal.cloudapp.net*. Quando se conectar a um cluster HBase, você precisará se conectar a um dos Zookeepers usando o FQDN. Cada cluster HDInsight possui 3 Zookeepers. São eles: *zookeeper0*, *zookeeper1* e *zookeeper2*. O FQDN será algo como *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Usar SQLLine**

1. Abra **Linha de comando do Hadoop** na área de trabalho.
2. Execute os seguintes comandos:

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

Para obter mais informações, consulte o [Manual SQLLine](http://sqlline.sourceforge.net/#manual).


















## Usar o SQuirrel

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) é um programa Java gráfico que permite exibir a estrutura de um banco de dados compatível com JDBC, procurar os dados nas tabelas, emitir comandos SQL, etc.

Esta seção mostra como instalar e configurar um SQuirrel em sua estação de trabalho para se conectar a um cluster HBase no HDInsight via VPN.

### Pré-requisitos

Antes de seguir os procedimentos, você deve ter o seguinte:

- Um cluster HBase implantado em uma rede virtual do Azure com uma máquina virtual DNS. Para obter instruções, consulte [Provisionar clusters HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]. 

	>[AZURE.IMPORTANT]Você deve instalar um servidor DNS na rede virtual.

- Obtenha o sufixo DNS específico da Conexão do cluster de cluster HBase. Para obtê-lo, faça RDP no cluster e, em seguida, execute IPConfig. O sufixo DNS é semelhante a:

		myhbase.b7.internal.cloudapp.net
- Baixe e instale o [Microsoft Visual Studio Express 2013 para Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) em sua estação de trabalho. Você precisará garantir o pacote para criar seu certificado.  
- Baixe e instale o [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) em sua estação de trabalho. O SQuirrel SQL client versão 3.0 e superior requer JRE versão 1.6 ou superior.  


### Configurar uma conexão VPN site a ponto para a rede virtual do Azure

Há três etapas envolvidas na configuração de uma conexão VPN ponto a site:

1. [Configurar uma rede virtual e um gateway de roteamento dinâmico](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Criar seus certificados](#Create-your-certificates)
3. [Configurar o seu cliente VPN](#Configure-your-VPN-client)

Consulte [Configurar uma conexão VPN site a ponto para uma Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/dn133792.aspx) para obter mais informações.

#### Configurar uma rede virtual e um gateway de roteamento dinâmico

Garanta que você possua um cluster HBase em uma rede virtual do Azure (consulte os pré-requisitos para esta seção). A próxima etapa é configurar uma conexão ponto a site.

**Para configurar a conectividade ponto a site**

1. Entre no [Portal do Azure][azure-portal].
2. À esquerda, clique em **REDES**.
3. Clique na rede virtual que você criou (consulte [Provisionar clusters HBase na Rede Virtual Azure][hdinsight-hbase-provision-vnet]).
4. Clique em **CONFIGURAR** na parte superior.
5. Na seção **Conectividade ponto a site**, selecione **Configurar conectividade ponto a site**. 
6. Configurar **IP INICIAL** e **CIDR** para especificar o intervalo de endereços IP do qual os clientes VPN receberão um endereço IP quando conectados. O intervalo não coincide com nenhum dos intervalos localizados na sua rede local e na rede virtual do Azure, à qual você será conectado. Por exemplo, se você selecionou 10.0.0.0/20 para a rede virtual, é possível selecionar 10.1.0.0/24 para o espaço de endereço do cliente. Consulte a página [Conectividade ponto a site][vnet-point-to-site-connectivity] para obter mais informações.
7. Na seção de espaços de endereço de rede virtual, clique em **Adicionar sub-rede de gateway**.
7. Na parte inferior da página, clique em **SALVAR**.
8. Clique em **SIM** para confirmar a alteração. Aguarde até que o sistema tenha terminado de fazer a alteração antes de prosseguir para o próximo procedimento.


**Para criar um gateway de roteamento dinâmico**

1. No portal do Azure, clique em **PAINEL** na parte superior da página.
2. Clique em **CRIAR GATEWAY** na parte inferior da página.
3. Clique em **SIM** para confirmar. Aguarde até que o gateway seja criado.
4. Clique em **PAINEL** na parte superior. Você verá um diagrama visual da rede virtual:

	![Diagrama virtual de ponto para site de rede virtual do Azure][img-vnet-diagram]

	O diagrama mostra 0 conexões de cliente. Depois de fazer uma conexão com a rede virtual, o número será atualizado para um.

#### Criar seus certificados

Uma maneira de criar um certificado X.509 é usando a ferramenta de criação de certificado (makecert.exe) que acompanha o [Microsoft Visual Studio Express 2013 para Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).


**Para criar um certificado autoassinado**

1. Na estação de trabalho, abra uma janela de prompt de comando.
2. Navegue até a pasta de ferramentas do Visual Studio. 
3. O comando a seguir no exemplo abaixo criará e instalará um certificado raiz no repositório de certificados Pessoais em sua estação de trabalho e também criará um arquivo .cer correspondente que você posteriormente carregará no portal do Azure. 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Vá para o diretório no qual deseja que o arquivo .cer esteja localizado, onde HBaseVnetVPNRootCertificate é o nome que você deseja usar para o certificado.

	Não feche o prompt de comando. Você precisará dele no próximo procedimento.

	>[AZURE.NOTE]Como você criou um certificado raiz do qual serão gerados certificados de cliente, você pode querer exportar o certificado juntamente com a sua chave privada e salvá-lo em um local seguro onde possa ser recuperado.

**Para criar um certificado de cliente**

- No mesmo prompt de comando (ele deve estar no mesmo computador em que você criou o certificado raiz. O certificado do cliente deve ser gerado a partir do certificado raiz), execute o seguinte comando:

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate é o nome do certificado raiz. Ele deve corresponder ao nome do certificado raiz.  

	O certificado raiz e o certificado cliente são armazenados no seu repositório pessoal de certificados no seu computador. Use certmgr.msc para verificar.

	![Azure virtual network point-to-site vpn certificate][img-certificate]

	Um certificado cliente deve ser instalado em cada computador que você deseja conectar à rede virtual. Recomendamos que você crie certificados cliente exclusivos para cada computador que deseja conectar à rede virtual. Para exportar os certificados cliente, use certmgr.msc. 

**Para carregar o certificado raiz no portal do Azure**

1. No portal do Azure, clique em **REDE** à esquerda.
2. Clique na rede virtual onde o cluster HBase é implantado.
3. Clique em **CERTIFICADOS** na parte superior.
4. Clique em **CARREGAR** na parte inferior e especifique o arquivo de certificado raiz que você criou no procedimento anterior. Aguarde até que o certificado seja importado.
5. Clique em **PAINEL** na parte superior. O diagrama virtual mostra o status.


#### Configurar o seu cliente VPN



**Para baixar e instalar o pacote do cliente VPN**

1. Na página PAINEL da rede virtual, na seção visão rápida, clique em **Baixar o pacote de VPN cliente de 64 bits** ou **Baixar o pacote de VPN cliente de 32 bits** com base em sua versão do sistema operacional da estação de trabalho.
2. Clique em **Executar** para instalar o pacote.
3. No prompt de segurança, clique em **Mais informações** e, em seguida, clique em **Executar mesmo assim**.
4. Clique em **Sim** duas vezes.

**Para se conectar à VPN**

1. Na área de trabalho da sua estação de trabalho, clique no ícone Redes na barra de tarefas. Você deverá ver uma conexão VPN com o seu nome de rede virtual.
2. Clique no nome da conexão VPN.
3. Clique em **Conectar**.

**Para testar a resolução de nome de domínio e a conexão VPN**

- Na estação de trabalho, abra um prompt de comando e toque em um dos seguintes nomes considerando que o sufixo DNS do cluster HBase é myhbase.b7.internal.cloudapp.net:

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

### Instalar e configurar o SQuirrel em sua estação de trabalho

**Para instalar o SQuirrel**

1. Baixe o arquivo jar cliente do SQuirrel SQL em [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Abra/execute o arquivo jar. Ele requer o [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Clique em **Avançar** duas vezes.
4. Especifique um caminho onde você tenha permissão de gravação e, em seguida, clique em **Avançar**.
	>[AZURE.NOTE]A pasta de instalação padrão é C:\\Program Files\\squirrel-sql-3.6. Para gravar nesse caminho, o instalador deve receber o privilégio de administrador. Abra um prompt de comando como administrador, navegue até a pasta bin do Java e, em seguida, execute
	>
	>     java.exe -jar [the path of the SQuirrel jar file] 
5. Clique em **OK** para confirmar a criação do diretório de destino.
6. A configuração padrão é para instalar os pacotes Base e Padrão. Clique em **Próximo**.
7. Clique em **Próximo** duas vezes e, em seguida, clique em **Pronto**.


**Para instalar o driver do Phoenix**

O arquivo jar do driver do Phoenix está localizado no cluster HBase. O caminho é semelhante ao seguinte com base nas versões:

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Você precisa copiá-lo para a sua estação de trabalho no caminho [SQuirrel installation folder]/lib. A maneira mais fácil é usar o RDP no cluster e, em seguida, usar copiar/colar arquivo (CTRL+C e CTRL+V) para copiá-lo para a sua estação de trabalho.

**Para adicionar um driver do Phoenix ao SQuirrel**

1. Abra o SQuirrel SQL Client em sua estação de trabalho.
2. Clique na guia **Driver** à esquerda.
2. No menu **Drivers**, clique em **Novo Driver**.
3. Insira as seguintes informações:

	- **Nome**: Phoenix
	- **URL de exemplo**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Nome da classe**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING]Usuário com todas as letras minúsculas na URL de exemplo.

	![HDInsight HBase Phoenix SQuirrel driver][img-squirrel-driver]
4. Clique em **OK**.

**Para criar um alias para o cluster HBase**

1. No SQuirrel, clique na guia **Aliases** à esquerda.
2. No menu **Aliases**, clique em **Novo Alias**.
3. Insira as seguintes informações:

	- **Nome**: O nome do cluster HBase ou qualquer nome que você preferir.
	- **Driver**: Phoenix. Ele deve corresponder ao nome do driver que você criou no último procedimento.
	- **URL**: A URL é copiada da configuração do seu driver. Certifique-se de usar todas as letras minúsculas.
	- **Nome de usuário**: nome de usuário HTTP de cluster HBase
	- **Senha**: senha de usuário HTTP de cluster HBase

	![HDInsight HBase Phoenix SQuirrel driver][img-squirrel-alias]
4. Clique em **Testar**. 
5. Clique em **Conectar**. Quando fizer a conexão, o SQuirrel ficará assim:

	![HBase Phoenix SQuirrel][img-squirrel]

**Para executar um teste**

1. Clique na guia **SQL**, próxima à guia **Objetos**.
2. Copie e cole o seguinte código:

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Clique no botão de execução.

	![HBase Phoenix SQuirrel][img-squirrel-sql]
4. Volte para a guia **Objetos**.
5. Expanda o nome do alias e a **TABELA**. Você deverá ver a nova tabela listada abaixo.
 
## Próximas etapas
Neste artigo, você aprendeu a usar o Apache Phoenix no HDInsight. Para obter mais informações, consulte:

- [Visão geral do HBase do HDInsight][hdinsight-hbase-overview]: o HBase é um banco de dados NoSQL de software livre Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
- [Provisionar clusters do HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]: com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
- [Configurar replicação HBase no HDInsight](hdinsight-hbase-geo-replication.md): saiba como configurar a replicação do HBase entre dois datacenters do Azure. 
- [Analisar sentimentos do Twitter com o HBase no HDInsight][hbase-twitter-sentiment]: saiba como fazer a [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de grandes volumes de dados usando o HBase em um cluster do Hadoop no HDInsight.

[azure-portal]: https://manage.windowsazure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png



<!--HONumber=52-->
 