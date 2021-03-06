<properties 
	pageTitle="Perguntas de banco de dados sobre o Banco de Dados de Documentos — perguntas frequentes | Microsoft Azure" 
	description="Obtenha respostas para perguntas frequentes sobre o Banco de Dados de Documentos, um serviço de banco de dados de documentos NoSql para o JSON. Responda perguntas de banco de dados sobre capacidade, níveis de desempenho e dimensionamento." 
	keywords="Perguntas de banco de dados, perguntas frequentes, banco de dados de documentos, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="mimig"/>


#Perguntas frequentes sobre o Banco de Dados de Documentos

## Perguntas de banco de dados sobre as noções básicas do Banco de Dados de Documentos do Microsoft Azure

### O que é o Banco de Dados de Documentos do Microsoft Azure? 
O Banco de Dados de Documentos do Microsoft Azure é um serviço de banco de dados de documentos NoSQL altamente escalonável que oferece consulta sofisticada de dados sem esquemas, ajuda a oferecer um desempenho confiável e configurável e habilita um desenvolvimento rápido – tudo isso por meio de uma plataforma gerenciada com o suporte e o alcance do Microsoft Azure. O Banco de Dados de Documentos é a solução certa para aplicativos Web e dispositivos móveis, nos quais taxa de transferência previsível, baixa latência e modelo de dados sem esquema são requisitos essenciais. O Banco de Dados de Documentos fornece flexibilidade de esquemas e indexação sofisticada por meio de um modelo de dados JSON nativo e inclui suporte de transações com diversos documentos com JavaScript integrado.
  
Para obter mais perguntas, respostas e instruções de banco de dados sobre como implantar e usar esse serviço, consulte a [página de documentação do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/services/documentdb/).

### Que tipo de banco de dados é o Banco de Dados de Documentos?
O Banco de Dados de Documentos é um banco de dados voltado a documentos NoSQL que armazena dados no formato JSON. O Banco de Dados de Documentos dá suporte a estruturas de dados autocontidos aninhados, que podem ser consultados com uma sofisticada [gramática de consulta SQL](documentdb-sql-query.md) do Banco de Dados de Documentos. O Banco de Dados de Documentos fornece processamento de transações de alto desempenho de JavaScript por parte do servidor com [procedimentos armazenados, gatilhos e funções definidas pelo usuário](documentdb-programming.md). O banco de dados dá suporte também a níveis de consistência ajustáveis pelo desenvolvedor, com [níveis de desempenho](documentdb-performance-levels.md) associados.
 
### Os bancos de dados do Banco de Dados de Documentos têm tabelas como um banco de dados relacional (RDBMS)?
Não, o Banco de Dados de Documentos armazena dados em coleções de documentos JSON. Para saber mais sobre os recursos do Banco de Dados de Documentos, consulte o [Modelo e conceitos de recursos do Banco de Dados de Documentos](documentdb-resources.md).

### Os bancos de dados do Banco de Dados de Documentos têm suporte para dados sem esquema?
Sim, o Banco de Dados de Documentos permite que os aplicativos armazenem documentos JSON arbitrários sem definições ou dicas de esquema. Os dados ficam disponíveis imediatamente para consulta com a interface de consulta SQL do Banco de Dados de Documentos.

### O Banco de Dados de Documentos tem suporte para transações ACID?
Sim, o Banco de Dados de Documentos suporta transações entre documentos expressas como procedimentos e gatilhos armazenados de JavaScript. As transações têm como escopo uma coleção única e são executados com semântica ACID como tudo ou nada, isolados de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida.

### Quais são os casos de uso típicos do Banco de Dados de Documentos?  
O Banco de Dados de Documentos é uma boa opção para novos aplicativos Web e dispositivos móveis nos quais escala, desempenho e a capacidade de consultar dados sem esquema são importantes. O Banco de Dados de Documentos funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Banco de Dados de Documentos](documentdb-use-cases.md).

### O Banco de Dados de Documentos é compatível com HIPAA?
No momento, o Banco de Dados de Documentos não é compatível com HIPAA; no entanto, há planos de torná-lo um serviço do Azure compatível com HIPAA. Para saber mais sobre a Microsoft e a HIPAA, veja [HIPAA e o HITECH Act](https://www.microsoft.com/pt-BR/TrustCenter/Compliance/HIPAA).

### Quais são os limites de dimensionamento do Banco de Dados de Documentos?
Contas do Banco de Dados de Documentos podem ser dimensionadas em termos de armazenamento e taxa de transferência com a adição de coleções. Veja [Limites do Banco de Dados de Documentos](documentdb-limits.md) para ver as cotas de serviço para o número de coleções. Se precisar de coleções adicionais, [entre em contato com o suporte](documentdb-increase-limits.md) para que a cota de sua conta seja aumentada.

### Quanto custa o Banco de Dados de Documentos do Microsoft Azure?
Consulte a página [Detalhes de preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317) para obter mais informações. Os encargos pelo uso do Banco de Dados de Documentos são determinados pelo número de coleções em uso, o número de horas em que as coleções estiveram online e o [nível de desempenho](documentdb-performance-levels.md) de cada coleção.

### Existe uma avaliação gratuita disponível?
Se for novo no Azure, você pode se inscrever para uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/), que dá a você 30 dias e US$ 200 para experimentar todos os serviços do Azure. Ou então, se tiver uma assinatura do Visual Studio, você estará qualificado para receber [US$ 150 em créditos Azure gratuitos por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) a serem usados em qualquer serviço do Azure.

### Como obter ajuda adicional com o Banco de Dados de Documentos?
Caso você precise de ajuda, entre em contato conosco no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), nos [Fóruns de desenvolvedores do MSDN do Banco de Dados de Documentos do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) ou agende um [chat individual com a equipe de engenharia do Banco de Dados de Documentos](http://www.askdocdb.com/). Para se manter atualizado quanto às últimas notícias e recursos do Banco de Dados de Documentos, siga-nos no [Twitter](https://twitter.com/DocumentDB).

## Configurar o Banco de Dados de Documentos do Microsoft Azure

### Como me inscrevo no Banco de Dados de Documentos do Microsoft Azure?
O Banco de Dados de Documentos do Microsoft Azure está disponível no [Portal do Azure][azure-portal]. Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure. Após se inscrever para uma assinatura do Microsoft Azure, e possível adicionar uma conta do Banco de Dados de Documentos à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Banco de Dados de Documentos, consulte [Criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

### O que é uma chave mestra?
Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que possuem a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestra. A chave mestra primária e a secundária estão disponíveis na folha **Chaves** do [Portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](documentdb-manage-account.md#keys).

### Como crio um banco de dados?
Você pode criar bancos de dados usando o [Portal do Azure](), como descrito em [Criar um banco de dados do Banco de Dados de Documentos](documentdb-create-database.md), um dos [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### O que é uma coleção?
Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. As consultas e transações estão dentro do escopo das coleções. Você pode armazenar um conjunto de documentos JSON heterogêneos em uma única coleção, sendo todos eles indexados automaticamente.

As coleções também são as entidades de cobrança para o Banco de Dados de Documentos. Os encargos mensais pelo uso do Banco de Dados de Documentos são determinados pelo número de coleções em uso, o número de horas em que as coleções estiveram online e o [nível de desempenho](documentdb-performance-levels.md) de cada coleção. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/).

### Há limites para os bancos de dados e coleções?
Cada coleção vem com uma alocação de armazenamento do banco de dados e uma taxa de transferência em um dos [níveis de desempenho](documentdb-performance-levels.md) com suporte. Também há cotas para cada recurso gerenciado pelo serviço. Para obter uma lista de todos os limites, consulte [Limites do Banco de Dados de Documentos](documentdb-limits.md). Para solicitar uma alteração em seus limites de conta, consulte [Solicitar maiores limites de conta do Banco de Dados de Documentos](documentdb-increase-limits.md).

### Como configuro usuários e permissões?
Você pode criar usuários e permissões usando um dos [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Perguntas de banco de dados sobre como desenvolver no Banco de Dados de Documentos do Microsoft Azure

### Como começo a desenvolver no Banco de Dados de Documentos?
Os [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem aproveitar as [APIs HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com os recursos do Banco de Dados de Documentos em uma série de plataformas e linguagens.

Exemplos dos SDKs [.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples) e [Python](https://github.com/Azure/azure-documentdb-python) do Banco de Dados de Documentos estão disponíveis no GitHub.

### O Banco de Dados de Documentos dá suporte ao SQL?
A linguagem de consulta SQL do Banco de Dados de Documentos fornece extensibilidade e operadores hierárquicos e relacionais por meio de JavaScript com base em UDFs (funções definidas por usuário). A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore, o que é usado pelas técnicas de indexação automática do Banco de Dados de Documentos, bem como pelo dialeto de consulta SQL do Banco de Dados de Documentos. Para obter detalhes sobre como usar a gramática SQL, veja o artigo [Consultar o Banco de Dados de Documentos][query].

### Quais são os tipos de dados com suporte pelo Banco de Dados de Documentos?
Os tipos de dados primitivos suportados no Banco de Dados de Documentos são os mesmos que os do JSON. O JSON tem um sistema de tipo simples que consiste em cadeias de caracteres, números (precisão dupla IEEE754) e boolianos – verdadeiro e falso e nulos. Os tipos de dados mais complexos como DateTime, Guid, Int64 e Geometria podem ser representados tanto no JSON quanto no Banco de Dados de Documentos com a criação de objetos aninhados usando o operador { } e matrizes usando o operador [ ].

### Como o Banco de Dados de Documentos fornece simultaneidade?
O Banco de Dados de Documentos suporta o controle de simultaneidade otimista (OCC) por meio de marcas de entidade HTTP ou ETags. Cada recurso do Banco de Dados de Documentos tem uma ETag, e os clientes do Banco de Dados de Documentos incluem sua versão de leitura mais recente em solicitações de gravação. Se a ETag for atual, a alteração é aplicada. Se o valor tiver sido alterado externamente, o servidor rejeita a gravação com um código de resposta "Falha de pré-condição HTTP 412". Os clientes devem ler a versão mais recente do recurso e repetir a solicitação.

### Como executo transações no Banco de Dados de Documentos?
O Banco de Dados de Documentos suporta transações integradas de linguagens com procedimentos e gatilhos armazenados de JavaScript. Todas as operações de bancos de dados dentro de scripts são executadas em isolamento instantâneo cujo escopo é a coleção. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Consulte [Programação do Banco de Dados de Documentos no servidor](documentdb-programming.md).

### Como posso inserir documentos em massa no Banco de Dados de Documentos? 
Há três maneiras de inserir documentos em massa no Banco de Dados de Documentos:

- A ferramenta de migração de dados, como descrito em [Importar dados para o Banco de Dados de Documentos](documentdb-import-data.md).
- O Gerenciador de Documentos no Portal do Azure, como descrito em [Adicionar documentos em massa com o Gerenciador de Documentos](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimentos armazenados, como descrito em [Programação de servidor do Banco de Dados de Documentos](documentdb-programming.md).

### O Banco de Dados de Documentos tem suporte para cache de link?
Sim, como o Banco de Dados de Documentos é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Clientes do Banco de Dados de Documentos podem especificar um cabeçalho "If-None-Match" para leituras em qualquer recurso, como documento e coleção, e atualizar suas cópias locais somente quando a versão do servidor for alterada.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0128_2016-->