---
title: 'Schnellstart: Extrahieren von Verkaufsbelegdaten mithilfe von Python – Formularerkennung'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart verwenden Sie die REST-API für die Formularerkennung mit Python, um Daten aus Bildern von USA-Verkaufsbelegen zu extrahieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2224ec64712ff9d1745231f39a1521ae941304ff
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118767"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Schnellstart: Extrahieren von Verkaufsbelegdaten mithilfe der Formularerkennungs-REST-API mit Python

In diesem Schnellstart verwenden Sie die REST-API der Azure-Formularerkennung mit Python, um relevante Informationen in USA-Verkaufsbelegen zu extrahieren und zu identifizieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Schnellstart benötigen Sie Folgendes:
- Wenn Sie das Beispiel lokal ausführen möchten, muss [Python](https://www.python.org/downloads/) installiert sein.
- Eine URL für ein Bild eines Verkaufsbelegs. Für diesen Schnellstart können Sie ein [Beispielbild](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg?raw=true) verwenden.

## <a name="create-a-form-recognizer-resource"></a>Erstellen einer Formularerkennungsressource

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analysieren eines Verkaufsbelegs

Um mit der Analyse eines Verkaufsbelegs zu beginnen, rufen Sie die **[Analyze Receipt](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)** -API mit dem folgenden Python-Skript auf. Nehmen Sie die folgenden Änderungen vor, bevor Sie das Skript ausführen:

1. Ersetzen Sie `<Endpoint>` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `<your receipt URL>` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `<subscription key>` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/prebuilt/receipt/analyze"
    source = r"<path to your receipt>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Speichern Sie den Code in einer Datei mit der Erweiterung „.py“. Beispiel: *form-recognizer-receipts.py*.
1. Öffnen Sie ein Eingabeaufforderungsfenster.
1. Verwenden Sie an der Eingabeaufforderung den Befehl `python`, um das Beispiel auszuführen. Beispiel: `python form-recognizer-receipts.py`.

Sie erhalten eine `202 (Success)`-Antwort, die einen **Operation-Location**-Header umfasst, den das Skript an der Konsole ausgibt. Dieser Header enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können. Im folgenden Beispielwert ist die Zeichenfolge nach `operations/` die Vorgangs-ID.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Abrufen der Verkaufsbelegergebnisse

Nachdem Sie die **Analyze Receipt**-API aufgerufen haben, rufen Sie die **[Get Analyze Receipt Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Fügen Sie am Ende des Python-Skripts den folgenden Code hinzu. Hierbei wird der Vorgangs-ID-Wert in einem neuen API-Aufruf verwendet. Dieses Skript ruft die API in regelmäßigen Abständen auf, bis die Ergebnisse vorliegen. Ein Intervall von mindestens einer Sekunde wird empfohlen.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Speichern Sie das Skript.
1. Verwenden Sie erneut den Befehl `python`, um das Beispiel auszuführen. Beispiel: `python form-recognizer-receipts.py`.

### <a name="examine-the-response"></a>Untersuchen der Antwort

Das Skript gibt Antworten an der Konsole aus, bis der **Analyze Receipt**-Vorgang abgeschlossen ist. Anschließend werden die extrahierten Textdaten im JSON-Format ausgegeben. Das Feld `"recognitionResults"` enthält jede Textzeile, die aus dem Beleg extrahiert wurde, und das Feld `"understandingResults"` enthält Schlüssel-/Wertinformationen für die wichtigsten Teile des Belegs.

Vergleichen Sie das folgende Bild des Belegs mit der entsprechenden JSON-Ausgabe. Die Ausgabe wurde zur besseren Lesbarkeit gekürzt.

![Ein Verkaufsbeleg vom Contoso-Laden](../media/contoso-allinone.jpg)

Der Knoten `"recognitionResults"` enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"understandingResults"` enthält die belegspezifischen Werte, die vom Modell erkannt wurden. Hier finden Sie nützliche Schlüssel-Wert-Paare wie Steuern, Gesamtsumme, Händleranschrift usw.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie die REST-API für die Formularerkennung mit Python verwendet, um den Inhalt eines Verkaufsbelegs zu extrahieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender zu erkunden.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Rest-API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)
