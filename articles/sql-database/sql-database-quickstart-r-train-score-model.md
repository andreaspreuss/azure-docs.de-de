---
title: Erstellen und Trainieren eines Vorhersagemodells in R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Erstellen Sie ein einfaches Vorhersagemodell in R mithilfe von Machine Learning Services für Azure SQL-Datenbank (Vorschauversion), und sagen Sie dann mithilfe neuer Daten ein Ergebnis vorher.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768462"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Schnellstart: Erstellen und Trainieren eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank

In dieser Schnellstartanleitung erstellen und trainieren Sie ein Vorhersagemodell mithilfe von R, speichern das Modell in einer Tabelle in Ihrer Datenbank und verwenden es dann zum Vorhersagen von Werten auf der Grundlage neuer Daten unter Verwendung von Machine Learning Services (mit R) in Azure SQL-Datenbank.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Eine [Azure SQL-Datenbank](sql-database-single-database-get-started.md) mit einer [Firewallregel auf Serverebene](sql-database-server-level-firewall-rule.md).
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) mit aktiviertem R. [Registrieren für die Vorschauversion](sql-database-machine-learning-services-overview.md#signup)
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Während der öffentlichen Vorschauphase führt Microsoft für Sie das Onboarding durch und aktiviert das maschinelle Lernen für Ihre vorhandene oder neue Datenbank.

In diesem Beispiel wird ein einfaches Regressionsmodell verwendet, um unter Verwendung des in R enthaltenen Datasets **cars** den Anhalteweg eines Fahrzeugs anhand der Geschwindigkeit vorherzusagen.

> [!TIP]
> Die R-Runtime enthält zahlreiche Datasets. Geben Sie an der R-Eingabeaufforderung `library(help="datasets")` ein, um eine Liste der installierten Datasets zu erhalten.

## <a name="create-and-train-a-predictive-model"></a>Erstellen und Trainieren eines Vorhersagemodells

Die Daten zur PKW-Geschwindigkeit im Dataset **cars** befinden sich in zwei numerischen Spalten: **dist** und **speed**. Die Daten umfassen mehrere Beobachtungen des Anhaltens bei unterschiedlichen Geschwindigkeiten. Aus diesen Daten erstellen Sie ein lineares Regressionsmodell, mit dem die Beziehung zwischen der PKW-Geschwindigkeit und dem benötigten Anhalteweg des PKW beschrieben wird.

Die Anforderungen eines linearen Modells sind einfach:
- Definieren Sie eine Formel, mit der die Beziehung zwischen der abhängigen Variablen *speed* und der unabhängigen Variablen *distance* beschrieben wird.
- Stellen Sie die Eingabedaten für die Verwendung beim Trainieren des Modells bereit.

> [!TIP]
> Mit dem folgenden Tutorial, in dem der Prozess zum Anpassen eines Modells mithilfe von rxLinMod beschrieben wird, können Sie Ihr Wissen über lineare Modelle auffrischen: [Fitting Linear Models](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) (Anpassen von linearen Modellen).

In den folgenden Schritten richten Sie die Trainingsdaten ein, erstellen ein Regressionsmodell, trainieren es mithilfe von Trainingsdaten und speichern dann das Modell in einer SQL-Tabelle.

1. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit Ihrer SQL-Datenbank her.

   Wenn Sie Unterstützung beim Herstellen der Verbindung benötigen, nutzen Sie die Informationen in [Schnellstart: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung mit einer Instanz von Azure SQL-Datenbank und deren Abfrage](sql-database-connect-query-ssms.md).

1. Erstellen Sie die Tabelle **CarSpeed**, um die Trainingsdaten zu speichern.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Erstellen Sie ein Regressionsmodell mit `rxLinMod`. 

   Zum Erstellen des Modells definieren Sie die Formel in Ihrem R-Code und übergeben die Trainingsdaten **CarSpeed** als Eingabeparameter.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Das erste Argument für rxLinMod ist der Parameter *formula*, mit dem der Anhalteweg in Abhängigkeit der Geschwindigkeit definiert wird. Die Eingabedaten werden in der Variablen `CarsData` gespeichert, die mit der SQL-Abfrage aufgefüllt wird.

1. Erstellen Sie eine Tabelle, in der Sie das Modell speichern, damit Sie es später für die Vorhersage verwenden können. 

   Die Ausgabe eines R-Pakets, das ein Modell erstellt, ist in der Regel ein **binäres Objekt**, sodass die Tabelle eine Spalte des Typs **VARBINARY(max)** enthalten muss.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Nun rufen Sie die gespeicherte Prozedur auf, generieren das Modell und speichern es in einer Tabelle.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Beachten Sie, dass Sie den folgenden Fehler erhalten, wenn Sie diesen Code ein zweites Mal ausführen:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Eine Option zur Vermeidung dieses Fehlers ist das Aktualisieren des Namens für jedes neue Modell. Sie können den Namen beispielsweise in eine aussagekräftigere Zeichenfolge ändern und den Modelltyp, den Tag der Erstellung usw. einfügen.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Anzeigen der Tabelle der Koeffizienten

Normalerweise ist die R-Ausgabe der gespeicherten Prozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) auf einen einzelnen Datenrahmen beschränkt. Sie können aber auch andere Ausgaben, z.B. Skalare, zusätzlich zum Datenrahmen zurückgeben.

Angenommen, Sie möchten ein Modell trainieren, aber dann sofort die Tabelle mit Koeffizienten des Modells anzeigen. Hierzu erstellen Sie die Tabelle mit den Koeffizienten als Hauptresultset und geben das trainierte Modell in einer SQL-Variablen aus. Sie können das Modell sofort wiederverwenden, indem Sie die Variable aufrufen, oder Sie können das Modell wie hier gezeigt in einer Tabelle speichern.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Ergebnisse**

![Trainiertes Modell mit zusätzlicher Ausgabe](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Bewerten neuer Daten mithilfe des trainierten Modells

Der Begriff *Bewertung* bezieht sich bei Data Science auf die Erstellung von Vorhersagen, Wahrscheinlichkeiten oder anderen Werten, die auf neuen Daten basieren, die einem trainierten Modell zugeführt werden. Sie verwenden das Modell, das Sie im vorherigen Abschnitt erstellt haben, um Vorhersagen für neue Daten zu bewerten.

Haben Sie bemerkt, dass die ursprünglichen Trainingsdaten nur bis 25 Meilen pro Stunde reichen? Der Grund ist, dass diese Originaldaten auf einem Experiment aus dem Jahr 1920 basieren! Sie fragen sich vielleicht, wie lange es dauert, bis ein PKW aus den Zwanziger Jahren zum Stehen kommt, wenn er mit einer Geschwindigkeit von 60 oder sogar 100 Meilen pro Stunde fahren würde. Um diese Frage zu beantworten, können Sie einige neue Geschwindigkeitswerte in Ihrem Modell angeben.

1. Erstellen Sie eine Tabelle mit neuen Daten zur Geschwindigkeit.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Sagen Sie den Anhalteweg aufgrund dieser neuen Geschwindigkeitswerte vorher.

   Weil Ihr Modell auf dem **rxLinMod**-Algorithmus basiert, der mit dem **RevoScaleR**-Paket bereitgestellt wird, rufen Sie die Funktion [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) statt der generischen R-`predict`-Funktion auf.

   Dieses Beispielskript:
   - verwendet eine SELECT-Anweisung, um ein einzelnes Modell aus der Tabelle abzurufen
   - übergibt es als Eingabeparameter
   - ruft die `unserialize`-Funktion auf dem Modell auf
   - wendet die `rxPredict`-Funktion mit den entsprechenden Argumenten auf das Modell an
   - stellt die neuen Eingabedaten bereit

   > [!TIP]
   > Informationen zur Echtzeitbewertung finden Sie in dem Artikel zu [Serialisierungsfunktionen in RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel).

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Ergebnisse**

   ![Resultset für die Vorhersage des Anhaltewegs](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In diesem Beispielskript wird die Funktion `str` während der Testphase hinzugefügt, um das Schema der Daten zu überprüfen, die von R zurückgegeben werden. Sie können die Anweisung dann später entfernen.
>
> Die im R-Skript verwendeten Spaltennamen werden nicht zwangsläufig an die Ausgabe der gespeicherten Prozedur übergeben. Hier definiert die WITH RESULTS-Klausel einige neue Spaltennamen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion) finden Sie in den folgenden Artikeln.

- [Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-overview.md)
- [Erstellen und Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)](sql-database-quickstart-r-create-script.md)
- [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
- [Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-data-issues.md)
