﻿<properties 
   pageTitle="Conectar um aplicativo Web no Serviço de Aplicativo do Azure ao Cache Redis por meio do protocolo Memcache" 
   description="Conectar um aplicativo Web no Serviço de Aplicativo do Azure ao Cache Redis por meio do protocolo Memcache" 
   services="app-service\web" 
   documentationCenter="php" 
   authors="SyntaxC4" 
   manager="wpickett" 
   editor="riande"/>
   
<tags
   ms.service="app-service-web"
   ms.devlang="php"
   ms.topic="article"
   ms.tgt_pltfrm="windows"
   ms.workload="web" 
   ms.date="03/31/2015"
   ms.author="cfowler"/>

# Conectar um aplicativo Web no Serviço de Aplicativo do Azure ao Cache Redis por meio do protocolo Memcache

Neste artigo, você verá como conectar um aplicativo Web do WordPress no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) ao [Cache Redis do Azure][12] usando o protocolo [Memcache][13]. Se tiver um aplicativo Web existente que usa um servidor Memcached para cache na memória, você poderá migrá-lo para o Serviço de Aplicativo do Azure e usar a solução de cache de terceiros no Microsoft Azure com pouca ou nenhuma alteração no código do aplicativo. Além disso, você pode tirar proveito de sua experiência com o Memcache para criar aplicativos distribuídos altamente dimensionáveis no Serviço de Aplicativo do Azure com o Cache Redis do Azure para cache na memória, usando estruturas de aplicativos populares, como .NET, PHP, Node.js, Java e Python.  

Os aplicativos Web do Serviço de Aplicativo habilitam esse cenário de aplicativo com o shim do Memcache de aplicativos Web, que é um servidor Memcached local que atua como um proxy Memcache para armazenar em cache as chamadas ao Cache Redis do Azure. Isso habilita qualquer aplicativo que se comunica usando o protocolo Memcache a armazenar dados em cache com o Cache Redis. Esse shim do Memcache funciona no nível do protocolo; portanto, ele pode ser usado por qualquer aplicativo ou estrutura de aplicativo, desde que se comunique usando o protocolo Memcache.

## Pré-requisitos

O shim do Memcache de aplicativos Web pode ser usado com qualquer aplicativo, desde que se comunique usando o protocolo Memcache. Para este exemplo específico, o aplicativo de referência é um site do WordPress escalonável que pode ser provisionado do Azure Marketplace. 

Siga as etapas descritas nestas postagens:

* [Provisionar uma instância do Serviço de Cache Redis do Azure][1]
* [Implantar um site do WordPress escalonável no Azure][0]

Depois que o site do WordPress escalonável for implantado e uma instância do Cache Redis for provisionada, você estará pronto para prosseguir com a habilitação do shim do Memcache em aplicativos Web do Serviço de Aplicativo do Azure.

## Habilitar o shim do Memcache de aplicativos Web

Para configurar o shim do Memcache, você deve criar três configurações de aplicativo. Isso pode ser feito usando diversos métodos, incluindo o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), o [Portal antigo][3], os [Cmdlets do PowerShell do Azure][5] ou as [Ferramentas de linha de comando de plataforma cruzada do Azure][5]. Para os fins desta postagem, vou usar o [Portal do Azure][4] para definir as configurações do aplicativo. Os valores a seguir podem ser recuperados da folha **Configurações** de sua instância do Cache Redis.

![Azure Redis Cache Settings Blade](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### Adicionar configurações de aplicativo REDIS_HOST

A primeira configuração de aplicativo que você precisa criar é **REDIS\_HOST**. Essa configuração define o destino para o qual o shim encaminha as informações de cache. O valor necessário para a configuração de aplicativo REDIS_HOST pode ser recuperado da folha **Propriedades** de sua instância do Cache Redis.

![Azure Redis Cache Host Name](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Defina a chave da configuração de aplicativo como **REDIS\_HOST** e o valor da configuração de aplicativo como o **nome de host** da instância do Cache Redis.

![Web App AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### Adicionar a configuração de aplicativo REDIS_KEY

A segunda configuração de aplicativo que você precisa criar é **REDIS\_KEY**. Essa configuração fornece o token de autenticação necessário para acessar com segurança a instância do Cache Redis. O valor necessário para a configuração de aplicativo REDIS_KEY pode ser recuperado da folha **Propriedades** da instância do Cache Redis.

![Azure Redis Cache Primary Key](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Defina a chave da configuração de aplicativo como **REDIS\_KEY** e o valor da configuração de aplicativo como a **Chave Primária** da instância do Cache Redis.

![Azure Website AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### Adicionar a configuração de aplicativo MEMCACHESHIM_REDIS_ENABLE

A última configuração de aplicativo é usada para habilitar o shim do Memcache em aplicativos Web, que usarão REDIS_HOST e REDIS_KEY para se conectar ao Cache Redis do Azure e encaminhar as chamadas de cache. Defina a chave da configuração de aplicativo como **MEMCACHESHIM\_REDIS\_ENABLE** e o valor como **true**.

![Web App AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Após adicionar as três (3) configurações de aplicativo, clique em **Salvar**.

## Habilitar a extensão de Memcache para PHP

Para que o aplicativo fale com o protocolo Memcache, é necessário instalar a extensão Memcache para PHP (a estrutura de linguagem para seu site do WordPress).

### Baixar a extensão php_memcache

Navegue até [PECL][6], na categoria de cache, e clique em [memcache][7]. Na coluna de downloads, clique no link de DLL.

![PHP PECL Website](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Baixe o link x86 NTS (Non-Thread Safe) para a versão do PHP habilitada nos aplicativos Web. (O padrão é PHP 5.4)

![PHP PECL Website Memcache Package](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### Habilitar a extensão php_memcache

Após baixar o arquivo, descompacte e carregue **php\_memcache.dll** no diretório **d:\\home\\site\\wwwroot\\bin\\ext\\**. Depois que php_memcache.dll for carregado no aplicativo web, a extensão precisará ser habilitada para o Tempo de Execução do PHP. Para habilitar a extensão Memcache no Portal do Azure, abra a folha **Configurações de Aplicativo** do aplicativo Web e, em seguida, adicione uma nova configuração de aplicativo com a chave **PHP\_EXTENSIONS** e o valor **bin\\ext\\php_memcache.dll**.


> Se o aplicativo Web precisar carregar várias extensões PHP, o valor de PHP_EXTENSIONS deverá ser uma lista delimitada por vírgulas de caminhos relativos para arquivos DLL.

![Web App AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Ao terminar, clique em **Salvar**.

## Instalar o plug-in do WordPress do Memcache

Na página de plug-ins do WordPress, clique no botão **Adicionar Novo**.

![WordPress Plugin Page](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

Na caixa de pesquisa, digite **memcached** e pressione a tecla **Enter**.

![WordPress Add New Plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Localize **Cache de Objeto Memcached** na lista e clique no botão **Instalar Agora**.

![WordPress Install Memcache Plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### Habilitar o plug-in do WordPress do Memcache

>[AZURE.NOTE] Siga as instruções neste blog sobre [Como habilitar uma extensão de site em aplicativos Web][8] para instalar o Visual Studio Online.

No arquivo `wp-config.php`, adicione o seguinte trecho de código acima do comentário de interrupção de edição próximo ao fim do arquivo.

```php
$memcached_servers = array(
	'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Depois que esse trecho de código for colado, monaco salvará automaticamente o documento.

A próxima etapa é habilitar o plug-in object-cache. Para isso, arraste e solte **object-cache.php** da pasta **wp-content/memcached** para a pasta **wp-content** para habilitar a funcionalidade de cache de objetos do Memcache.

![Locate the memcache object-cache.php plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Agora que o arquivo **object-cache.php** está na pasta **wp-content**, o cache de objetos Memcached agora está habilitado.

![Enable the memcache object-cache.php plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## Verificando se o plug-in de cache de objetos do Memcache está funcionando

Todas as etapas para habilitar o shim do Memcache de aplicativos Web foram concluídas. Só resta verificar se os dados estão populando sua instância do Cache Redis.

### Habilitar o suporte à porta não SSL no Cache Redis do Azure

>[AZURE.NOTE] Quando este documento foi escrito, a CLI do Redis não dava suporte à conectividade SSL; portanto, as etapas a seguir são necessárias.

No Portal do Azure, navegue até a instância do Cache Redis que você criou para o aplicativo Web. Quando a folhado cache for aberta, clique no ícone **Configurações**.

![Azure Redis Cache Settings Button](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Selecione **Portas de Acesso** na lista.

![Azure Redis Cache Access Port](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Clique em **Não** para **Permitir acesso somente via SSL**.

![Azure Redis Cache Access Port SSL Only](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Você verá que a porta não SSL agora está definida. Clique em **Salvar**.

![Azure Redis Cache Redis Access Portal Non-SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### Conectar ao Cache Redis do Azure por meio de redis-cli

>[AZURE.NOTE] Essa etapa pressupõe que o Redis esteja instalado localmente em seu computador de desenvolvimento. [Instale o Redis localmente usando estas instruções][9].

Abra o console de linha de comando de sua escolha e digite o seguinte comando:

```shell
redis-cli -h <hostname-for-redis-cache> -a <primary-key-for-redis-cache> -p 6379
```

Substitua **<nome_de_host_para_cache_redis>** pelo nome de host real xxxxx.redis.cache.windows.net e **<chave_primária_para_cache_redis>** pela chave de acesso para o cache e pressione **Enter**. Depois que a CLI for conectada à instância do Cache Redis, execute qualquer comando do Redis. Na captura de tela abaixo, optei por listar as chaves.

![Connect to Azure Redis Cache from Redis CLI in Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

A chamada para listar as chaves deve retornar um valor. Caso contrário, tente navegar até o aplicativo Web e tente novamente.

## Conclusão

Parabéns! O aplicativo do WordPress agora tem um cache na memória centralizado para ajudar a aumentar a taxa de transferência. Lembre-se: o shim do Memcache de aplicativos Web pode ser usado com qualquer cliente Memcache, independentemente da linguagem de programação ou da estrutura de aplicativo. Para fornecer comentários ou fazer perguntas sobre o shim do Memcache de aplicativos Web, poste nos [Fóruns do MSDN][10] ou no [Stackoverflow][11].

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência à navegação pelo portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)


[0]: http://bit.ly/1F0m3tw
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org


<!--HONumber=52-->