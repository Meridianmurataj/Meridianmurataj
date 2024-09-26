WITH Sales AS (
    SELECT 
        OrderDate,
        OrderMonth = DATEFROMPARTS(YEAR(OrderDate), MONTH(OrderDate), 1),
        TotalDue,
        OrderRank = ROW_NUMBER() OVER (PARTITION BY DATEFROMPARTS(YEAR(OrderDate), MONTH(OrderDate), 1) ORDER BY TotalDue DESC)
    FROM AdventureWorks2019.Sales.SalesOrderHeader
),

-- CTE per le prime 10 vendite mensili
Top10Sales AS (
    SELECT
        OrderMonth,
        Top10Total = SUM(TotalDue)
    FROM Sales
    WHERE OrderRank <= 10
    GROUP BY OrderMonth
),

-- CTE per le vendite che non sono nella top 10
SalesMinusTop10 AS (
    SELECT
        OrderMonth,
        TotalSales = SUM(TotalDue)
    FROM Sales
    WHERE OrderRank > 10
    GROUP BY OrderMonth
),


 CTE Ricorsiva: Generazione di Date Giornaliere





-- CTE per gli acquisti mensili
Purchases AS ( 
    SELECT
        OrderDate,
        OrderMonth = DATEFROMPARTS(YEAR(OrderDate), MONTH(OrderDate), 1),
        TotalDue,
        OrderRank = ROW_NUMBER() OVER (PARTITION BY DATEFROMPARTS(YEAR(OrderDate), MONTH(OrderDate), 1) ORDER BY TotalDue DESC)
    FROM AdventureWorks2019.Purchasing.PurchaseOrderHeader
),

-- CTE per gli acquisti che non sono nella top 10
PurchasesMinusTop10 AS (
    SELECT
        OrderMonth,
        TotalPurchases = SUM(TotalDue)
    FROM Purchases
    WHERE OrderRank > 10
    GROUP BY OrderMonth
)

-- Query finale: confronto tra vendite e acquisti oltre la top 10
SELECT 
    A.OrderMonth,
    A.TotalSales,
    B.TotalPurchases
FROM SalesMinusTop10 A
JOIN PurchasesMinusTop10 B
    ON A.OrderMonth = B.OrderMonth
ORDER BY A.OrderMonth;


**-- CTE ricorsiva per generare una serie di date giornaliere dal 1° gennaio 2021**


WITH Dates AS (
    SELECT CAST('2021-01-01' AS DATE) AS MyDate
    UNION ALL
    SELECT DATEADD(DAY, 1, MyDate)
    FROM Dates
    WHERE MyDate < CAST(GETDATE() AS DATE)
)
SELECT MyDate
FROM Dates
OPTION (MAXRECURSION 365); -- Limita la ricorsione a un anno


-- CTE ricorsiva per generare una serie di date mensili dal 2020 al 2029
WITH Dates AS (
    SELECT CAST('2020-01-01' AS DATE) AS MyDate
    UNION ALL
    SELECT DATEADD(Month, 1, MyDate)
    FROM Dates
    WHERE MyDate < CAST('2029-12-01' AS DATE)
)
SELECT MyDate
FROM Dates
OPTION (MAXRECURSION 0); -- Imposta ricorsione a 120 per coprire 10 anni



crea una tabella dal 01 01 2024 fine Oggi() -1

-- Dichiarazione delle variabili
DECLARE @Today DATE = CAST(GETDATE() - 1 AS DATE);
DECLARE @InizioAnno DATE = CAST('2024-01-01' AS DATE);

-- Creazione della tabella RollingDate (se non esiste già)
IF OBJECT_ID('RollingDate', 'U') IS NOT NULL
    DROP TABLE RollingDate;

CREATE TABLE RollingDate (
    C_Date DATE
);

-- Ciclo per popolare la tabella con le date giornaliere
WHILE @InizioAnno <= @Today
BEGIN
    INSERT INTO RollingDate (C_Date) VALUES (@InizioAnno);
    SET @InizioAnno = DATEADD(DAY, 1, @InizioAnno);
END

-- Selezione delle date dalla tabella RollingDate
SELECT * FROM RollingDate;




###à##



IF OBJECT_ID('RollingDate', 'U') IS NOT NULL
    DROP TABLE RollingDate;

OBJECT_ID('RollingDate', 'U'):

OBJECT_ID() è una funzione di SQL Server che restituisce l'identificatore univoco (ID) di un oggetto nel database (come tabelle, viste, indici, ecc.).
'RollingDate' è il nome dell'oggetto (in questo caso, una tabella) di cui vuoi verificare l'esistenza.
'U' è il tipo di oggetto. 'U' sta per "User Table" (Tabella Utente), quindi stai chiedendo se esiste una tabella utente con il nome RollingDate.
Se la tabella esiste, OBJECT_ID() restituisce l'ID della tabella. Se non esiste, restituisce NULL.
IS NOT NULL: Questo controllo serve a verificare se l'ID della tabella è diverso da NULL, il che significa che la tabella esiste. Se è diverso da NULL, significa che l'oggetto (in questo caso la tabella) esiste nel database.

DROP TABLE RollingDate;: Se la condizione è vera (la tabella esiste), esegue il comando DROP TABLE, che elimina la tabella RollingDate dal database, compresi tutti i suoi dati.



In sintesi:
Verifica se la tabella RollingDate esiste.
Se esiste, la elimina, liberando lo spazio e permettendo la creazione di una nuova tabella con lo stesso nome, evitando errori di creazione duplicata o conflitti.
