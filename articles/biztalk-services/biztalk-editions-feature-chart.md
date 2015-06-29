<properties 
	pageTitle="Saiba mais sobre recursos em edições de Serviços BizTalk | Azure" 
	description="Compare os recursos das edições dos serviços BizTalk: Gratuito, Developer, Basic, Standard e Premium. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# Serviços do BizTalk: Tabela de edições

Os Serviços BizTalk do Azure oferecem as seguintes edições: Free, Developer, Basic, Standard e Premium:

**Free (Visualização)**: Inclui a habilidade de criar e gerenciar Conexões Híbridas. Uma Conexão Híbrida é uma maneira fácil de conectar um Site do Azure a um sistema local, como o SQL Server. 

**Developer**: Inclui Conexões Híbridas, processamento de mensagens EAI & EDI com um portal de gerenciamento de parceiro comercial fácil de usar, e oferece suporte para esquemas EDI comuns e processamento sofisticado de EDI por x12 e AS2. Pode criar cenários EAI comuns conectando serviços na nuvem com qualquer protocolo HTTP/S, REST, FTP, WCF e SFTP para ler e escrever mensagens.  Utilize a conectividade com sistemas LOB locais com adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel e SQL Server prontos para uso. Tudo em um ambiente centralizado no desenvolvedor com ferramentas do Visual Studio para permitir desenvolvimento e implantação fáceis. Limitado para fins de desenvolvimento e teste apenas sem nenhum Contrato de Nível de Serviço (SLA).

**Basic**: Inclui a maioria dos recursos do Developer com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também oferece alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).

**Standard**: Inclui todos os recursos do Basic com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também oferece alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).


**Premium**: Inclui todos os recursos do Standard com aumentos em Conexões Híbridas, pontes EAI, Contratos EDI, e conexões do BizTalk Adapter Pack. Também inclui arquivamento, alta disponibilidade, e a opção de dimensionar com um Contrato de Nível de Serviço (SLA).


A tabela a seguir lista as diferenças:

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Visualização)</th>
        <th>Desenvolvedores</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>Preço inicial</strong></td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
</tr>
<tr>
<td><strong>Configuração mínima padrão</strong></td>
<td>1 Unidade Free</td>
<td>1 unidade Developer</td>
<td>1 unidade Basic</td>
<td>1 unidade Standard</td>
<td>1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Sim, em incrementos de 1 unidade Basic</td>
<td>Sim, em incrementos de 1 unidade Standard</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala máxima permitida</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
</tr>
<tr>
<td><strong>EAI Bridges por unidade</strong></td>
<td>Não incluso</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclui Contratos TPM</td>
<td>Não incluso</td>
<td>Inclusa. 10 contratos por unidade.</td>
<td>Inclusa. 50 contratos por unidade.</td>
<td>Inclusa. 250 contratos por unidade.</td>
<td>Inclusa. 1000 contratos por unidade.</td>
</tr>
<tr>
<td><strong>Conexões Híbridas por Unidade</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferência de dados por conexões híbridas (GB) por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexões do Serviço do Adaptador do BizTalk para sistemas de LOB no local</strong></td>
<td>Não incluso</td>
<td>1 conexão</td>
<td>2 conexões</td>
<td>5 conexões</td>
<td>25 conexões</td>
</tr>
<tr>
<td align="left"><strong>Protocolos suportados/Sistemas:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Blob do Azure</li>
<li>APIs REST</li>
</ul>
</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Alta Disponibilidade</strong>
<br/><br/>
Para obter o Contrato de Nível de Serviço (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.
</td>
<td>Não incluso</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Backup e restauração</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Acompanhamento</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Arquivamento</strong><br/><br/>
Inclui NRR (não repúdio de recebimento) e o download de mensagens rastreadas</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Não incluso</td>
<td>Não incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de transformações, incluindo XSLT personalizado</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
</table>

**Observação**
<br/>Para resiliência contra falhas de hardware, a Alta Disponibilidade implica em ter várias VMs dentro de uma única Unidade do BizTalk.

## Perguntas frequentes
#### O que é uma Unidade do BizTalk?
Uma "unidade" é o nível atômico de uma implantação dos Serviços BizTalk do Azure. Cada edição é fornecida com uma unidade que tem capacidade e memória de computação diferentes. Por exemplo, uma Unidade Básica tem mais computação do que a Developer, a Standard tem mais computação do que a Basic e assim por diante. Quando você dimensiona um Serviço BizTalk, você o faz em termos de Unidades.

#### Qual é a diferença entre os Serviços do BizTalk e a VM do BizTalk do Azure?
Os Serviços BizTalk fornecem uma arquitetura de PaaS (Plataforma como Serviço) verdadeira para criar soluções de integração na nuvem. Com o modelo de PaaS, você focaliza completamente na lógica do aplicativo e deixa toda a infraestrutura de gerenciamento para a Microsoft, incluindo:

- Sem necessidade de gerenciar ou aplicar patches em máquinas virtuais
- A Microsoft garante a disponibilidade
- Você controla a escala sob demanda simplesmente solicitando mais ou menos capacidade por meio do Portal de Gerenciamento do Azure

O BizTalk Server em Máquinas Virtuais do Azure fornece uma arquitetura de IaaS (Infraestrutura como Serviço). Você cria máquinas virtuais e as configura exatamente como seu ambiente local, facilitando a execução dos aplicativos existentes na nuvem sem alterações no código. Com a IaaS, você ainda é responsável por configurar as máquinas virtuais, gerenciá-las (por exemplo, instalando software e patches do sistema operacional) e  pela criação da arquitetura do aplicativo para alta disponibilidade. 

Se você estiver planejando criar novas soluções de integração que minimizem seu esforço de gerenciamento da infraestrutura, use os Serviços BizTalk. Se estiver planejando migrar rapidamente suas soluções existentes do BizTalk ou procurando um ambiente sob demanda para desenvolver e testar aplicativos do BizTalk Server, use o BizTalk Server em uma Máquina Virtual do Azure.

#### Qual é a diferença entre o Serviço do Adaptador do BizTalk e as Conexões Híbridas?
O Serviço do Adaptador do BizTalk é usado por um Serviço do BizTalk do Azure. O Serviço do Adaptador do BizTalk usa o BizTalk Adapter Pack para conectar-se a um sistema de Linha de Negócios (LOB) local. Uma conexão híbrida fornece uma maneira fácil e conveniente para conectar aplicativos do Azure, como Sites e Serviços Móveis, para um recurso local. 

#### Quando crio um contrato nos Serviços do BizTalk, por que o número de pontes é aumentado em dois, em vez de apenas um? 

Cada contrato é composto de duas pontes diferentes, uma ponte de comunicação do lado do envio e outra do lado do recebimento.

####  O que acontecerá quando eu atingir o limite de cota do número de pontes ou contratos? 

Você não poderá implantar nenhuma ponte nova, nem criar novos contratos. Para implantar mais, será necessário aumentar o número de unidades do Serviço BizTalk ou atualizar para uma Edição mais alta.

#### Como faço para migrar de uma camada dos Serviços do BizTalk para outra? 

Use o fluxo de backup e restauração para migrar de uma camada para outra. Apenas alguns caminhos de migração têm suporte. Consulte os [Serviços BizTalk: Backup e Restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873) para obter mais detalhes sobre os caminhos de migração com suporte.

####  O Serviço do Adaptador do BizTalk está incluído no serviço? Como recebo o software?

Sim, o Serviço do Adaptador do BizTalk com o BizTalk Adapter Pack estão incluídos com o [download](http://www.microsoft.com/download/details.aspx?id=39087) do SDK dos Serviços BizTalk do Azure.

## Avançar

Para provisionar Serviços BizTalk do Azure no Portal de Gerenciamento do Azure, consulte [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197) (a página pode estar em inglês).

## Consulte também
- [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: Gráfico de status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços do BizTalk: guias Painel, Monitoramento e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços do BizTalk: Backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços do BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços do BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!--HONumber=46--> 
 