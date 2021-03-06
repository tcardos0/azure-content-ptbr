<properties
   pageTitle="Agrupar por opções no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar o agrupamento por opções no SQL Data Warehouse do Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Agrupar por opções de SQL Data Warehouse

A cláusula [GROUP BY] é usada para agregar dados a um conjunto de linhas de resumo. Ela também tem algumas opções que ampliam sua funcionalidade que precisam ser solucionadas pois não tem suporte diretamente pelo SQL Data Warehouse do Azure.

Essas opções são - GROUP BY com ROLLUP - GROUPING SETS - GROUP BY com CUBE

## O rollup e o agrupamento definem opções
A opção mais simples é usar `UNION ALL` ao invés de executar o rollup do que contar com a sintaxe explícita. O resultado é exatamente o mesmo

Abaixo está um exemplo de um agrupamento pela instrução usando a opção `ROLLUP`:

```
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Ao usar ROLLUP solicitamos as seguintes agregações: - País e Região - País - Total

Para substituir isso, você precisará usar `UNION ALL`; especificar as agregações explicitamente necessárias para retornar os mesmos resultados:

```
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY 
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY 
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para GROUPING SETS, tudo o que precisamos fazer é adotar a mesma entidade principal, mas apenas criar seções UNION ALL para os níveis de agregação que queremos ver

## Opções Cube
É possível criar uma instrução GROUP BY WITH CUBE usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicado e difícil. Para atenuar isso, você pode usar essa abordagem mais avançada.

Vamos usar o exemplo anterior.

A primeira etapa é definir o ‘cube’ que define todos os níveis de agregação que desejamos criar. É importante que você observe o CROSS JOIN das duas tabelas derivadas. Isso gera todos os níveis. O restante do código está lá para formatação.

```
CREATE TABLE #Cube
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ',' 
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1) 
            ELSE GroupBy 
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Os resultados de CTAS podem ser vistos abaixo:

![][1]

A segunda etapa é especificar uma tabela de destino para armazenar os resultados intermediários:

```
DECLARE 
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

A terceira etapa é executar um loop sobre o cubo de colunas realizando a agregação. A consulta será executada uma vez para cada linha na tabela temporária #Cube e armazenará os resultados na tabela temporária #Results

```
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>'' 
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por fim, retornamos os resultados apenas lendo da tabela temporária #Results

```
SELECT * 
FROM #Results
ORDER BY 1,2,3
;
```

Dividir o código em seções e gerar uma construção de loop, torna o código mais gerenciável e sustentável.


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->