<properties
	pageTitle="O que é o Banco de Dados SQL? Introdução ao Banco de Dados SQL | Microsoft Azure"
	description="Obtenha uma introdução ao Banco de Dados SQL: detalhes e recursos técnicos do RDBMS (sistema de gerenciamento de banco de dado relacional) da Microsoft na nuvem."
	keywords="introdução ao sql, introdução ao sql, o que é o banco de dados sql, DTU"
	services="sql-database"
	documentationCenter=""
	authors="shontnew"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="12/22/2015"
   ms.author="shkurhek"/>

# O que é o Banco de Dados SQL? Introdução ao Banco de Dados SQL, detalhes técnicos e uma explicação sobre DTUs

O Banco de Dados SQL é um serviço de banco de dados relacional na nuvem com base no mecanismo líder de mercado do Microsoft SQL Server que contém recursos internos críticos. O Banco de Dados SQL oferece desempenho previsível, escalabilidade sem tempo de inatividade, continuidade dos negócios e proteção de dados e quase não exige administração. Você pode se concentrar no desenvolvimento rápido de aplicativos e acelerar seu tempo de colocação no mercado, em vez de gerenciar máquinas virtuais e infraestrutura. Como ele se baseia no mecanismo [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), o Banco de Dados SQL oferece suporte a ferramentas do SQL Server, bibliotecas e APIs, o que torna mais fácil mover e estender para a nuvem.

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados SQL relacionados ao desempenho, escalabilidade e gerenciabilidade, com links para explorar os detalhes. Se você estiver pronto para entrar, poderá [Criar seu primeiro Banco de Dados SQL](sql-database-get-started.md) ou [Criar um pool de banco de dados elástico](sql-database-elastic-pool-portal.md) em minutos. Se você quiser se aprofundar, assista a este vídeo de 30 minutos.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## Ajuste de desempenho e dimensionamento sem tempo de inatividade
Os banco de dados SQL estão disponíveis nas *camadas de serviço* Básica, Standard e Premium. Cada camada de serviço oferece [diferentes níveis de desempenho e recursos](sql-database-service-tiers.md) para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Você pode compilar seu primeiro aplicativo em um banco de dados pequeno com alguns dólares por mês, e, em seguida, [alterar a camada de serviço](sql-database-scale-up.md) manualmente ou de forma programática a qualquer momento à medida que o seu aplicativo torna-se viral em todo o mundo, sem tempo de inatividade para seu aplicativo ou para seus clientes.

Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho do banco de dados individual sob demanda é o suficiente, especialmente se os padrões de uso são relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios.

Os [pools de banco de dados elástico](sql-database-elastic-pool.md) no Banco de Dados SQL resolvem esse problema. O conceito é simples. Você alocar desempenho a um pool e paga pelo desempenho coletivo do pool em vez do desempenho do banco de dados individual. Você não precisa ajustar o desempenho de banco de dados. Os bancos de dados no pool, chamados *bancos de dados elásticos*, são automaticamente escalados e reduzidos verticalmente para atender à demanda. Os bancos de dados elásticos consomem, mas não excedem os limites do pool, então o custo permanece previsível, mesmo que o uso do banco de dados não. Além disso, é possível [adicionar e remover bancos de dados do pool](sql-database-elastic-pool-portal.md), escalar seu aplicativo por meio de alguns bancos de dados para milhares, tudo dentro de um orçamento que você pode controlar.

Seja qual for a forma escolhida por você, individual ou elástico, você não fica limitado. Você pode misturar os bancos de dados individuais com pools de banco de dados elástico e alterar as camadas de serviço dos bancos de dados individuais e pools para criar designs inovadores. E mais, com a potência e o alcance do Azure, você pode combinar e corresponder serviços do Azure com o Banco de Dados SQL para atender às suas necessidades exclusivas de design de aplicativo moderno, direcionar as eficiências de recursos e custo, bem como descobrir novas oportunidades de negócios.

Mas como você pode comparar o desempenho relativo de bancos de dados e pools de banco de dados? Como você sabe o momento certo de parar ao fazer o ajuste? A resposta é a unidade de transação do banco de dados (DTU) para bancos de dados individuais e DTU elástica (eDTU) para bancos de dados elásticos e pools de banco de dados.

## Entenda as DTUs

[AZURE.INCLUDE [Descrição de DTU de Banco de Dados SQL](../../includes/sql-database-understanding-dtus.md)]

## Mantenha seus aplicativos e a continuidade dos negócios

Os acordos de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/) de disponibilidade de 99,99% de líderes do setor do Azure, alimentados por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução 24/7. Com cada banco de dados SQL, você tira proveito da proteção de dados, tolerância a falhas incorporadas e de uma proteção de dados que, em outras situações, você teria que projetar, comprar, criar e gerenciar. Mesmo assim, dependendo das demandas do seu negócio, você pode precisar de camadas adicionais de proteção para garantir que seu aplicativo e sua empresa possam recuperar-se rapidamente no caso de um desastre, um erro ou alguma outra situação. Com o banco de dados SQL, cada camada de serviço oferece um menu diferente dos recursos que você pode usar para colocar em funcionamento. Você pode usar a restauração para um ponto específico para retornar um banco de dados para um estado anterior, até 35 dias. Além disso, se o datacenter que hospeda os seus bancos de dados sofrer uma interrupção, você pode fazer failover para réplicas de banco de dados em uma região diferente. Ou você pode usar réplicas para upgrades ou realocação para diferentes regiões.

![Replicação geográfica do Banco de Dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Veja [Continuidade dos negócios](sql-database-business-continuity.md) para obter detalhes sobre os diferentes recursos de continuidade dos negócios disponíveis para diferentes camadas de serviço.

## Proteja seus dados
O SQL Server possui uma tradição de segurança de dados sólida que o banco de dados SQL mantém com recursos que limitam o acesso, protegem os dados e ajudam você a monitorar a atividade. Confira [Protegendo seu banco de dados SQL](sql-database-security.md) para um rápido resumo das opções de segurança em Banco de Dados SQL. Confira a [Central de Segurança para o Mecanismo do Banco de Dados do SQL Server e do Banco de Dados SQL](https://msdn.microsoft.com/library/bb510589) para uma visão mais abrangente dos recursos de segurança. E visite a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre segurança da plataforma do Azure.

## Próximas etapas
Agora que você leu uma introdução ao Banco de Dados SQL e respondeu à pergunta "O que é o Banco de Dados SQL?", está pronto para o seguinte:

- Confira a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para obter os preços e cálculos do banco de dados individual e do banco de dados elástico.

- Comece pela [criação do seu primeiro banco de dados](sql-database-get-started.md). Compile seu primeiro aplicativo em [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) ou [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=AcomDC_0128_2016-->