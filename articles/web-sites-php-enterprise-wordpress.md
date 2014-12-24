﻿<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress edição corporativa nos sites do Azure" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="01/01/1900" ms.author="cephalin" />

#WordPress edição corporativa nos Sites do Azure

Os sites do Azure oferecem um ambiente escalonável, seguro e fácil de usar para sites do [WordPress][wordpress] críticos e de larga escala. A própria Microsoft executa sites de edição corporativa como os blogs do [Office][officeblog] e [Bing][bingblog]. Este documento mostra como é possível usar os sites do Azure para estabelecer e manter um site do WordPress de edição corporativa e baseado em nuvem que possa identificar um grande volume de visitantes.

##Neste artigo 

* [Arquitetura e planejamento](#planning) - Saiba mais sobre arquiteturas, requisitos e considerações de desempenho antes de criar o site

* [Como](#howto) - Criar, implantar e configurar o site

* [Mais recursos](#resources) - Recursos adicionais e informações

##<a id="plan"></a>Arquitetura e planejamento

Uma instalação básica do WordPress tem apenas dois requisitos.

* **Banco de Dados MySQL** - disponível por meio do [ClearDB na Azure Store][cdbnstore] ou é possível gerenciar a própria instalação do MySQL nas Máquinas Virtuais do Azure que usam [Windows][mysqlwindows] ou [Linux][mysqllinux].

    > [WACOM.NOTE] O ClearDB fornece diversas configurações do MySQL, com características de desempenho diferentes para cada configuração. Consulte a [Azure Store][cdbnstore] for para obter informações sobre ofertas oferecidas por meio da Azure Store ou o [preço do ClearDB](http://www.cleardb.com/pricing.view) para ofertas diretamente no ClearDB.
    
* **PHP 5.2.4 ou superior** - Os sites do Azure atualmente fornecem [PHP versões 5.3, 5.4 e 5.5][phpwebsite].

	> [WACOM.NOTE] Recomendamos sempre executar a versão mais recente do PHP para garantir que você tenha as correções de segurança mais recentes.

###Implantação básica

Usando apenas os requisitos básicos, você poderia criar uma solução básica dentro de uma região do Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Embora isso permita que você escale o aplicativo horizontalmente criando várias instâncias do site, tudo é hospedado dentro dos datacenters em uma determinada região geográfica. Os visitantes de fora dessa região podem notar tempos de resposta longos ao usar o site e, se os datacenters nessa região desligarem, o aplicativo desligará também.


###Implantação em várias regiões

Usando o [Gerenciador de Tráfego][trafficmanager] do Azure, é possível dimensionar o site do WordPress em várias regiões geográficas e, ao mesmo tempo, fornecer apenas uma URL para os visitantes. Todos os visitantes chegam por meio do Gerenciador de Tráfego e são encaminhados para uma região com base na configuração do balanceamento de carga.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Dentro de cada região, o site do WordPress ainda seria dimensionado em várias instâncias do Site, mas essa colocação em escala é específica da região. As regiões de alto tráfego podem ser dimensionadas de maneira diferente do que as de baixo tráfego.

A replicação e o roteamento para vários Bancos de Dados MySQL podem ser feitos usando o [CDBR High Availability Router][cleardbscale] do ClearDB (mostrado à esquerda) ou o [MySQL Cluster CGE][cge]. 

###Implantação em várias regiões com armazenamento e cache de mídia

Se o site aceitar uploads ou hospedar arquivos de mídia, use o armazenamento de Blob do Azure. Se você precisar de cache, leve em consideração [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/pt-br/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/pt-br/gallery/store/memcachier/memcachier/) ou uma das outras ofertas de cache na [Azure Store](http://azure.microsoft.com/pt-br/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

Como o armazenamento de blob é distribuído geograficamente entre várias regiões por padrão, você não precisa se preocupar com a replicação de arquivos em todos os sites. Também é possível habilitar a [Rede de Distribuição de Conteúdo][cdn] (CDN) do Azure para armazenamento de Blob, que distribui arquivos para nós finais mais próximos dos visitantes.

###Planejamento

####Requisitos adicionais

Para fazer isto... | Use isto...
------------------------|-----------
**Carregar ou armazenar arquivos grandes** | [Plug-in do WordPress para uso de armazenamento de Blob][storageplugin]
**Enviar email** | [SendGrid][storesendgrid] e [plug-in do WordPress para usar o SendGrid][sendgridplugin]
**Nomes de domínio personalizados** | [Nomes de domínio personalizado com sites do Azure][customdomain]
**HTTPS** | [Suporte a HTTPS em sites do Azure][httpscustomdomain]
**Validação de pré-produção** | [Suporte à publicação para sites do Azure][staging] <p>Observe que alternar de um site de preparo para produção também move a configuração do WordPress. Você deverá verificar se todas as configurações estão atualizadas segundo os requisitos do site em produção antes de alternar o site em etapas para em produção.</p> 
**Monitoramento e solução de problemas** | [Log de diagnóstico com sites do Azure][log] e [Monitoramento de sites do Azure][monitor]
**Implantar seu site** | [Implantar um site do Azure][implantar]

####Disponibilidade e recuperação de desastre

Para fazer isto... | Use isto...
------------------------|-----------
**Balancear carga de sites** ou **geo-distribuir sites** | [Rotear o tráfego com o Gerenciador de Tráfego do Azure][trafficmanager]
**Backup e restauração** | [Backup de sites do Azure][backup] e [Restauração de um site do Azure][restore]

####Desempenho

O desempenho na nuvem é obtido principalmente por meio do cache e da expansão, mas a memória, a largura de banda e outros atributos da hospedagem do Site também devem ser levados em consideração.

Para fazer isto... | Use isto...
------------------------|-----------
**Compreender os recursos de instância do site** |  [Detalhes de preços, incluindo recursos de tamanhos e modos de site][websitepricing]
**Cache resources** | [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/pt-br/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/pt-br/gallery/store/memcachier/memcachier/), ou uma das outras opções de cache na [Azure Store](http://azure.microsoft.com/pt-br/gallery/store/)
**Dimensionar seu aplicativo** | [Dimensione um site do Azure][websitescale] e [Roteamento de alta disponibilidade do ClearDB][cleardbscale]. Se optar por hospedar e gerenciar a própria instalação do MySQL, você deverá levar em consideração o [MySQL Cluster CGE][cge] para escalar horizontalmente

####Migração

Existem dois métodos de migrar um site do WordPress existente para Sites do Azure.

* **[Exportação do WordPress][export]** - Exporta o conteúdo do blog, que poderá ser importado em um novo site do WordPress no Azure usando o [plug-in de importação do WordPress][import].

	> [WACOM.NOTE] Embora permita migrar o conteúdo, esse processo não migra nenhum plug-in, tema ou outra personalização. Eles devem ser reinstalados manualmente.

* **Migração manual** - [Faça backup do site][wordpressbackup] e do [banco de dados][wordpressdbbackup] e restaure-o manualmente em um site do Azure e no banco de dados MySQL para migrar sites altamente personalizados e evitar o tédio de instalar manualmente plug-ins, temas e outras personalizações.

##Como

###<a id="create"></a>Cria um novo site do WordPress

1. Use a [Azure Store][cdbnstore] para criar um banco de dados MySQL do tamanho identificado na seção [Arquitetura e planejamento](#planning) nas regiões em que você hospedará o site.

2. Siga as etapas em [Criar um site do WordPress da galeria no Azure][createwordpress] para criar um novo site do WordPress. Ao criar o site, selecione **Usar um banco de dados MySQL existente** e o banco de dados criado na etapa 1.

Se você estiver migrando um site do WordPress existente, consulte [Migrar um site do WordPress existente](#migrate) depois de criar um novo site.

###<a id="migrate"></a>Migrar um site do WordPress existente para o Azure

Conforme mencionado na seção [Arquitetura e planejamento](#planning), existem duas maneiras de migrar um site do WordPress.

* **exportar e importar** - para sites sem muita personalização ou no qual você apenas deseja mover o conteúdo.

* **fazer backup e restaurar** - para sites com muita personalização nos quais você deseja mover tudo.

Use uma das seções a seguir para migrar o site.

####O método exportar e importar

1. Use a [Exportação do WordPress][export] para exportar seu site existente.

2. Crie um novo site usando as etapas na seção [Criar um novo site do WordPress](#create).

3. Faça logon no site do WordPress nos sites do Azure e clique em **Plug-ins** -> **Adicionar novo**. Pesquise e instale o plug-in **WordPress Importer**.

4. Depois que o plug-in de importação tiver sido instalado, clique em **Ferramentas** -> **Importar** e selecione **WordPress** para usar o plug-in de importação do WordPress.

5. Na página **Importar WordPress**, clique em **Escolher Arquivo**. Procure o arquivo WXR exportado do site do WordPress existente e escolha **Carregar arquivo e importar**.

6. Clique em **Enviar**. Você será avisado de que a importação foi bem-sucedida.

8. Depois de concluir todas essas etapas, reinicie o site no painel do site no [Portal de Gerenciamento do Azure][mgmtportal].

Depois de importar o site, você precisará realizar as etapas a seguir para habilitar as configurações não contidas no arquivo de importação.

Se você estava usando isto... | Faça isso...
------------------ | ----------
**Permalinks** | No painel do WordPress do novo site, clique em **Configurações** -> **Permalinks** e atualize a estrutura Permalinks
**links de mídia/imagem** | Para atualizar links para o novo local, use o [plug-in Velvet Blues Update URLs][velvet], uma ferramenta de pesquisa e substituição, ou faça isso manualmente no banco de dados
**Temas** | Vá para **Aparência** -> **Tema** e atualize o tema do site conforme necessário
**Menus** | Se o tema permitir menus, os links para a home page ainda poderão ter o subdiretório anterior inserido. Vá até **Aparência** -> **Menus** e os atualize

####O método fazer backup e restaurar

1. Faça backup do site do WordPress usando as informações nos [backups do WordPress][wordpressbackup].

2. Faça backup do banco de dados existente usando as informações em [Fazendo backup do banco de dados][wordpressdbbackup].

3. Crie um novo banco de dados e restaure o backup.

	1. Compre um novo banco de dados na [Azure Store][cdbnstore], ou configure um banco de dados MySQL em uma VM do [Windows][mysqlwindows] ou [Linux][mysqllinux].

	2. Usando um cliente do MySQL como [MySQL Workbench][workbench], se conecte ao novo banco de dados e importe o banco de dados do WordPress.

	3. Atualize o banco de dados para alterar as entradas de domínio para o novo domínio do site do Azure. Por exemplo, mywordpress.azurewebsites.net. Use [Pesquisar e substituir o script dos bancos de dados do WordPress][searchandreplace] para alterar todas as instâncias com segurança.

4. Crie um novo site e publique o backup do WordPress.

	1. Crie um novo site no [Portal de Gerenciamento do Azure][mgmtportal] com um banco de dados usando **Novo** -> **Site** -> **Criação personalizada**. Isso criará um site vazio

	2. No backup do WordPress, localize o arquivo **wp-config.php** e o abra em um editor. Substitua as entradas a seguir pelas informações do novo banco de dados MySQL.

		* **DB_NAME** - o nome de usuário do banco de dados

		* **DB_USER** - o nome de usuário usado para acessar o banco de dados

		* **DB_PASSWORD** - a senha do usuário

		Depois de alterar essas entradas, salve e feche o arquivo **wp-config.php**.

	3. Use as informações em [Como implantar um site do Azure][implantar] para habilitar o método de implantação que você deseja usar e implante o backup do WordPress no site do Azure.

5. Depois que o site do WordPress for implantado, você deverá ser capaz de acessar o novo site usando a URL *.azurewebsite.net do site.

###Configurar o site

Depois que o site do WordPress for criado ou migrado, use as informações a seguir para melhorar o desempenho ou habilitar a funcionalidade adicional.

Para fazer isto... | Use isto...
------------- | -----------
**Defina o modo e tamanho do site e habilite o dimensionamento** | [Como dimensionar sites][websitescale]
**Habilitar conexões de banco de dados persistente** <p>Por padrão, o WordPress não usa conexões de banco de dados persistente, que podem fazer a conexão com o banco de dados se tornar limitada após várias conexões.</p>  | <ol><li><p>Edit the <strong>wp-includes/wp-db.php</strong> file.</p></li><li><p>Find the following line.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Replace the previous line with the following.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Find the following line.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Replace the above line with the following.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Save the file <strong>wp-includes/wp-db.php</strong> file and redeploy the site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>OBSERVAÇÃO:</h5><p>Essas alterações poderão ser substituídas quando WordPress for atualizado.</p><p>O WordPress é padronizado para atualizações automáticas, que podem ser desabilitadas, editando o arquivo <strong>wp-config.php</strong> e adicionando <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>. Outra maneira de direcionar atualizações seria usar um WebJob que monitora o arquivo <strong>wp-db.php</strong> e realiza as modificações acima toda vez que o arquivo é atualizado. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introdução a WebJobs</a> para obter mais informações.</p></div>
**Melhorar o desempenho** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desabilitar o cookie ARR</a> - pode melhorar o desempenho ao executar WordPress em várias instâncias de site</p></li><li><p>Habilitar cache. <a href="http://msdn.microsoft.com/pt-br/library/azure/dn690470.aspx">O Redis cache</a> (visualização) pode ser usado com o <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in do Wordpress de cache de objeto do Redis</a>, ou use uma das outras ofertas de cache da <a href="http://azure.microsoft.com/pt-br/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Como tornar o WordPress mais rápido com o Wincach</a> - O Wincache é habilitado por padrão para sites </p></li><li><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/">Dimensionar seu site do Azure</a> e use <a href="http://www.cleardb.com/developers/cdbr/introduction">Roteamento de alta disponibilidade do ClearDB</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Usar os blobs de armazenamento** | <ol><li><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/">Criar uma conta de Armazenamento do Azure</a></p></li><li><p>Saiba como <a href="http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/">Usar a rede de distribuição de conteúdo (CDN)</a> para gep-distribuir dados armazenados em blobs.</p></li><li><p> Instale e configure o <a href="https://wordpress.org/plugins/windows-azure-storage/">armazenamento do Azure para o plug-in dp WordPress</a>.</p><p>Para informações detalhadas de configuração e instalação do plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guia do usuário</a>.</p> </li></ol>
**Permitir email** | <ol><li><p><a href="http://azure.microsoft.com/pt-br/gallery/store/sendgrid/sendgrid-azure/">Habilitar SendGrid usando o Armazenamento do Azure </a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalar o plug-in do SendGrid para WordPress</a></p></li></ol>
**Configurar um nome de domínio personalizado** | [Usar um nome de domínio personalizado com um site do Azure][customdomain]
**Habilitar HTTPS para um nome de domínio personalizado** | [Usar HTTPS com um site do Azure][httpscustomdomain]
**Balancear carga ou geo-distribuir seu site** | [Rotear o tráfego com o Gerenciador de Tráfego do Azure][trafficmanager]. Se você estiver usando um domínio personalizado, consulte [Usar nome de domínio personalizado com um Site do Azure][customdomain] para obter informações sobre como usar o Gerenciador de Tráfego com nomes de domínio personalizado
**Habilitar backups automatizados de site** | [Backup de sites do Azure][backup]
**Habilitar o log de diagnóstico** | [Habilitar o log de diagnóstico para sites][log]

##<a href="resources"></a>Recursos adicionais

* [Otimização do WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Converter um site do WordPress em um multissite](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Assistente de atualização do ClearDB para o Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedando o WordPress em uma subpasta do Site do Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Passo a passo: Criar um site do WordPress usando o Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedar o blog do WordPress existente no Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Habilitando formatação de permalinks no WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Como migrar e executar o blog do WordPress nos Sites do Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Como executar o WordPress nos Sites do Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress no Azure em 2 minutos ou menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Migrando um blog do WordPress para o Azure - Parte 1: Criando um blog do WordPress no Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Migrando um blog do WordPress para o Azure - Parte 2: Transferindo o conteúdo](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Migrando um blog do WordPress para o Azure - Parte 3: Configurando o domínio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Migrando um blog do WordPress para o Azure - Parte 4: Formatação de permalinks e regras para reescrever URLs](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Migrando um blog do WordPress para o Azure - Parte 5: Migrando de uma subpasta para a raiz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Como configurar um site do WordPress na conta do Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Preparando o WordPress no Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Dicas para o WordPress no Azure](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/pt-br/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/pt-br/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/pt-br/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/pt-br/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[importar]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: (http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-web-site-gallery/)
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/pt-br/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/