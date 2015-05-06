﻿<properties 
	pageTitle="Conectores de Protocolo de Aplicativos de API do Microsoft Azure | Microsserviço de Aplicativos de API" 
	description="Aprenda a criar Aplicativos de API do Conector de Protocolo do Microsoft Azure e adicionar o Aplicativo de API ao aplicativo lógico; microsserviços" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="Deonhe" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 


# Conectores de protocolo no Serviço de Aplicativo do Microsoft Azure


## O que é um conector de protocolo
Conectores de protocolo são Aplicativos de API* que você pode usar para criar aplicativos que se comuniquem usando uma ampla variedade de protocolos, como HTTP, SMTP, FTP e assim por diante. 

Você pode encontrar os conectores de protocolo no portal do Microsoft Azure clicando em **Novo** > **Web + móvel** > **Azure Marketplace** > insira a palavra-chave de pesquisa **protocolo** > pressione enter

Os seguintes conectores de protocolo estão disponíveis no Azure Marketplace:

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- Arquivo

Aqui está uma breve descrição de cada um dos conectores de protocolo. 

<table>
<tr>
<th> Nome</th>
<th> Descrição</th>
<th> Gatilhos</th>
<th> Ações</th>

<tr>
<td>SFTP
<td>Um conector SFTP permite baixar arquivos ou carregar arquivos para um servidor SFTP.
<td>Novo arquivo disponível na pasta SFTP

<td>Arquivos: carregar, excluir, listar e obter 

</tr>

<tr>
<td>POP3
<td>Um conector POP3 permite baixar o email de um servidor POP3.
<td>Nova mensagem chega
<td>N/D
</tr>

<tr>
<td>FTP
<td>Um conector de FTP permite baixar ou carregar arquivos de um servidor FTP.
<td>Novo arquivo disponível na pasta FTP
<td>Arquivo: carregar, obter, excluir e listar
</tr>

<tr>
<td>HTTP
<td>Um conector HTTP permite enviar dados para servidores HTTP com o protocolo HTTP ou HTTPS.
<td>Nenhum
<td>Postar, obter, excluir, colocar
</tr>

<tr>
<td>SMTP
<td>Um conector SMTP permite enviar email de um servidor SMTP.
<td>N/D
<td>Enviar email
</tr>

<tr>
<td>Arquivo
<td>Um Conector de arquivo permite que você se conecte ao servidor de arquivos local e execute ações como carregar, obter, excluir e listar os arquivos.  Também há um gatilho para recuperar arquivos.
<td>Novo arquivo adicionado ao dispositivo
<td>Arquivo: carregar, obter, excluir e listar
</tr>


</table>

Agora que você tem uma ideia do que os nossos conectores de protocolo podem fazer, vamos analisar alguns casos de uso simples para esses conectores.

### Monitoramento de feedback do cliente ###
Imagine que sua empresa recentemente lançou um novo aplicativo e a equipe quer saber o que os clientes estão dizendo sobre o aplicativo na mídia social.  Cada membro da equipe pode verificar periodicamente os vários sites de mídia social e estimar que palavras-chave os clientes podem usar para discutir seu aplicativo.  Mas, de maneira mais elegante, você pode simplesmente criar um conector do Twitter, configurá-lo para monitorar o Twitter para palavras-chave, menções e hashtags específicas.  Você pode usar o conector SMTP para criar um email que contenha o conteúdo dos tweets correspondentes e enviá-lo para os membros da equipe.  Você pode usar a um conector HTTP para postar esses tweets para um site interno da empresa.  Você pode fazer tudo isso sem escrever uma única linha de código.  

Vamos começar. 

## Criar um conector

Um conector pode ser criado usando o portal do Azure ou usando APIs REST [Criar conectores com APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

### Criar um Conector no Portal do Azure

Vamos examinar a criação de um conector SMTP usando o Azure Marketplace

1. Faça logon no [portal](https://manage.windowsazure.com) do Microsoft Azure
2. Selecione **NOVO** > **Web + móvel** > **Azure Marketplace** >
3. Na **caixa de pesquisa**, digite **protocolo** e pressione **Enter**.  Você também pode selecioná-lo da lista.  Quando selecionado, uma nova folha ou janela se abre.  Selecione **Criar**. 
4. Insira as seguintes propriedades:

<table>
<tr><th>Propriedade</th> <th>Descrição</th> </tr>
<tr><td>Nome</td> <td>Descrição</td> </tr>
<tr><td>Configurações do pacote</td> <td>Use a propriedade de configurações do pacote para inserir todos os detalhes de autenticação, como nome de usuário, senha, endereço de servidor número da porta, e assim por diante, para o servidor SMTP. </td> </tr>
<tr><td>Plano de Serviço de Aplicativo</td> <td>Lista seu plano de pagamento.  Você pode alterá-lo se precisar de mais ou menos recursos</th> </td>
<tr><td>Camada de Preços</td> <td>Propriedade somente leitura que lista a categoria de preços dentro de sua assinatura do Azure.</td> </tr>
<tr><td>Grupo de Recursos</td> <td>Crie um novo ou use um grupo existente.  O uso de grupos de recursos explica esta propriedade</td> </tr>
<tr><td>Assinatura</td> <td>Propriedade somente leitura que lista sua assinatura atual</td> </tr>
<tr><td>Local</td> <td>A localização geográfica que hospeda o serviço do Azure</th> </td>
<tr><td>Adicionar ao quadro inicial</td> <td>Selecione essa opção para adicionar o Aplicativo de API ao seu quadro inicial</td></tr>
</table> 

### Criar um conector usando APIs REST
[Criar conectores com APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Adicionar um conector ao seu aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe diferentes tipos de aplicativos que podem usar esses conectores.  Por exemplo, você pode criar um aplicativo *Logic* combinando um ou mais dos seus conectores *logically* em um único aplicativo.

Para usar os conectores no seu aplicativo *Logic*, selecione um conector pré-configurado na lista, adicione-o ao fluxo de trabalho de design, faça as alterações de configuração necessárias e ele estará pronto para uso. 

Para executar essas etapas, você precisa de um aplicativo Web, aplicativos móveis ou aplicativo lógico.  Consulte <> para obter as etapas específicas.  Quando seu aplicativo estiver disponível, adicione os conectores.  Faça assim:

Use as etapas a seguir para adicionar um conector a um aplicativo lógico: 

1. No quadro inicial do portal do Azure (página inicial), vá até **Marketplace** e pesquise o aplicativo lógico, móvel ou Web. 

	Se estiver criando um novo aplicativo, pesquise pelo aplicativo lógico, móvel ou Web.  Selecione o aplicativo e, na nova folha, selecione **Criar**.  [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas. 

2. Abra seu aplicativo e selecione **Gatilhos e Ações**. 
3. Na **Galeria**, selecione o conector.  Ele será adicionado ao seu aplicativo.
4. Configure o conector:
5. Cada conector tem propriedades que são específicas para o serviço e o ambiente ao qual ele está se conectando.  Insira os detalhes das propriedades.  Tenha em mente que algumas propriedades são opcionais.
6. Selecione **OK** para salvar suas alterações.


## Segurança
Conectores usam OAuth ou nomes de usuário e senhas.

## Leia sobre os aplicativos lógicos
[O que são aplicativos lógicos??](app-service-logic-what-are-logic-apps.md) |
[Sites e aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md) |


## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) |
[Conectores empresariais](app-service-logic-enterprise-connectors.md) |
[Conectores Business-to-Business](app-service-logic-b2b-connectors.md) |
[Conectores sociais](app-service-logic-social-connectors.md) |
[Conectores de aplicativos e serviços de dados](app-service-logic-data-connectors.md)
[Lista de Aplicativos de API e conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->