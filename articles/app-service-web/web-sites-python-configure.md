<properties 
	pageTitle="Configurando o Python com os Sites do Azure" 
	description="Este tutorial descreve opções para criar e configurar um aplicativo Python compatível com Web server Gateway Interface (WSGI) básica nos sites do Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Configurando o Python com os Sites do Azure

Este tutorial descreve as opções para criação e configuração de uma Web Server Gateway Interface (WSGI) básica compatível com aplicativos Python nos sites do Azure.

Ele descreve os recursos adicionais de implantação do Git, como o ambiente virtual e a instalação do pacote usando requirements.txt.


## Bottle, Django ou Flask?

A galeria do Azure contém modelos para as estruturas Bottle, Django e Flask.  Se você estiver desenvolvendo seu primeiro site do Azure ou se não estiver familiarizado com o Git, recomendamos que você siga um destes tutoriais, que incluem instruções passo a passo para criar um aplicativo funcional da galeria usando a implantação do Git do Windows ou Mac:

- [Criando sites com Bottle][]
- [Criando Sites com Django][]
- [Criando sites com Flask][]


## Criação de sites no Portal

Este tutorial assume uma assinatura existente do Azure e acesso ao Portal de Gerenciamento do Azure.

Se não tiver um site já existente, você poderá criar um por meio do Portal de Gerenciamento do Azure.  Clique no botão NOVO no canto inferior esquerdo. Uma janela será exibida. Clique em COMPUTAR, SITE e, depois, em CRIAÇÃO RÁPIDA.

![](./media/web-sites-python-configure/configure-python-create-website.png)


## Publicação Git

Use as guias INÍCIO RÁPIDO ou PAINEL DE CONTROLE para o site recém-criado para configurar a publicação Git.  Este tutorial usa Git para criar, gerenciar e publicar nosso site Python para sites do Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

Depois que a publicação Git estiver configurada, um repositório Git será criado e associado ao seu site.  A URL do repositório será exibida e poderá ser usada para enviar por push dados do ambiente de desenvolvimento local para a nuvem. Para publicar aplicativos via Git, certifique-se de que um cliente Git também esteja instalado e use as instruções fornecidas para enviar por push o conteúdo do site para sites do Azure.


## Visão geral do aplicativo

Nas próximas seções, os arquivos a seguir são criados.  Eles devem ser colocados na raiz do repositório Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## Manipulador WSGI

WSGI é um padrão de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definindo uma interface entre o servidor web e o Python. Ele fornece uma interface padronizada para escrever vários aplicativos da web e estruturas usando o Python.  As estruturas web populares Python de hoje usam a WSGI.  Os sites do Azure dão suporte para tais estruturas; Além disso, os usuários avançados podem até mesmo criar seus próprios, desde que o manipulador personalizado siga as diretrizes de especificação WSGI.

Aqui está um exemplo de um `app.py` que define um manipulador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Você pode executar esse aplicativo localmente com `python app.py` e, em seguida, navegar até `http://localhost:5555` no seu navegador da Web.


## Ambiente virtual

Embora o aplicativo de exemplo acima não exija pacotes externos, é provável que seu aplicativo exija alguns.

Para ajudar a gerenciar as dependências do pacote externo, a implantação do Git do Azure dá suporte à criação de ambientes virtuais.

Quando o Azure detecta um arquivo requirements.txt na raiz do repositório, ele cria automaticamente um ambiente virtual chamado `env`.  Isso ocorre apenas na primeira implantação ou durante qualquer implantação depois que o tempo de execução do Python selecionado é alterado.

Você provavelmente desejará criar um ambiente virtual localmente para desenvolvimento, mas não o inclua em seu repositório Git.


## Gerenciamento de pacote

Os pacotes listados no arquivo requirements.txt serão instalados automaticamente no ambiente virtual usando pip.  Isso ocorre em todas as implantações, mas pip ignorará a instalação se um pacote já estiver instalado.

Exemplo `requirements.txt`:

    azure==0.8.4


## Versão do Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemplo `runtime.txt`:

    python-2.7


## Web.config

Você precisará criar um arquivo web.config para especificar como o servidor deve manipular solicitações.

Observe que, se você tiver um arquivo web.x.y.config no repositório, em que x.y corresponde ao tempo de execução do Python selecionado, o Azure copiará automaticamente o arquivo apropriado como web.config.

Os exemplos de web.config a seguir contam com um script de proxy do ambiente virtual, que é descrito na próxima seção.  Eles funcionam com o manipulador WSGI usado no exemplo `app.py` acima.

Exemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Arquivos estáticos são manipulados pelo servidor Web diretamente, sem passar pelo código do Python, para melhorar o desempenho.

Nos exemplos acima, o local dos arquivos estáticos no disco deve corresponder ao local na URL.  Isso significa que uma solicitação para `http://pythonapp.azurewebsites.net/static/site.css` fornecerá o arquivo no disco em `\static\site.css`.

É possível configurar a regra `Static Files` para fornecer arquivos de um local no disco que seja diferente do local na URL.  Na definição da regra a seguir, uma solicitação de `http://pythonapp.azurewebsites.net/static/site.css` fornecerá o arquivo no disco em `\FlaskWebProject\static\site.css`, em vez de `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` é onde você especifica o manipulador WSGI.  Nos exemplos acima, é `app.wsgi_app` porque o manipulador é uma função chamada `wsgi_app` em `app.py` na pasta raiz.

`PYTHONPATH` pode ser personalizado, mas se você instalar todas as suas dependências no ambiente virtual especificando-as em requirements.txt, não deverá precisar alterá-lo.


## Proxy de ambiente virtual

O script a seguir é usado para recuperar o manipulador WSGI, ativar o ambiente virtual e registrar os erros em log. Foi projetado para ser genérico e ser usado sem modificações.

Conteúdo de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # Esse código-fonte está sujeito aos termos e condições da licença do Apache, versão 2.0. Uma 
     # cópia da licença pode ser encontrada no arquivo License.html na raiz da distribuição. Se 
     # não for possível localizar a licença do Apache, versão 2.0, envie um email para 
     # vspython@microsoft.com. Usando esse código-fonte de qualquer forma, você concorda em ser vinculado 
     # aos termos da licença do Apache, versão 2.0.
     #
     # Você não deve remover este ou qualquer outro aviso deste software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Personalizar a implantação do Git

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## Solução de problemas - implantação

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../../includes/web-sites-python-troubleshooting-deployment.md)]


## Solução de problemas - Instalação de pacotes

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solução de problemas - Ambiente virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Criando sites com Bottle]: web-sites-python-create-deploy-bottle-app.md
[Criando Sites com Django]: web-sites-python-create-deploy-django-app.md
[Criando sites com Flask]: web-sites-python-create-deploy-flask-app.md


<!--HONumber=52--> 