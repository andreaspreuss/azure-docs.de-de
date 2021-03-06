---
title: Verwenden von GitHub Actions mit der Azure App Configuration-Synchronisierung
description: Verwenden von GitHub Actions, um ein Update Ihrer App Configuration-Instanz zu initiieren, wenn definierte Aktionen in einem GitHub-Repository ausgeführt werden
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523712"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchronisieren Ihrer App Configuration-Instanz mithilfe von GitHub Actions
Azure App Configuration verwendet GitHub Actions zum Auslösen von Updates einer App Configuration-Instanz basierend auf Aktionen, die in einem GitHub-Repository ausgeführt werden. GitHub-Workflows lösen Konfigurationsupdates aus und ermöglichen damit die Integration dieser Updates in denselben Workflow, der zum Aktualisieren des App-Codes verwendet wird.

Ein GitHub Actions-[Workflow](https://help.github.com/articles/about-github-actions#workflow) definiert einen automatisierten Prozess in einem GitHub-Repository. Dieser Prozess informiert GitHub darüber, wie Ihr GitHub-Projekt erstellt und bereitgestellt werden soll. Azure App Configuration stellt die Aktion *Azure App Configuration-Synchronisierung* bereit, um Updates für eine App Configuration-Instanz zu aktivieren, wenn Änderungen am Quellrepository vorgenommen werden. 

Eine YAML-Datei (.yml) im Pfad `/.github/workflows/` in Ihrem Repository definiert Ihren Workflow. Diese Definition enthält die Schritte und Parameter des Workflows.

GitHub-Ereignisse, z. B. ein Push in ein Repository, können einen GitHub Actions-Workflow auslösen.  Die Aktion *Azure App Configuration-Synchronisierung* ermöglicht das Auslösen eines Updates einer App Configuration-Instanz, wenn eine bestimmte GitHub-Aktion auftritt. Sie können Konfigurationsupdates beim Pushen, Überprüfen oder Branchen von App-Konfigurationsdateien auslösen – genauso wie beim App-Code.

Die GitHub-[Dokumentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) bietet ausführliche Informationen zu GitHub-Workflows und GitHub Actions. 

## <a name="enable-github-actions-in-your-repository"></a>Aktivieren von GitHub Actions in Ihrem Repository
Um mit der Verwendung dieser GitHub-Aktion zu beginnen, navigieren Sie zu Ihrem Repository, und wählen Sie die Registerkarte **Aktionen** aus. Klicken Sie auf **Neuer Workflow** und dann auf **Eigenen Workflow einrichten**. Suchen Sie zum Schluss im Marketplace nach „Azure App Configuration-Synchronisierung“.
> [!div class="mx-imgBorder"]
> ![Auswählen der Registerkarte „Aktionen“](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Auswählen der Aktion „App Configuration-Synchronisierung“](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronisieren von Konfigurationsdateien nach einem Push
Mit dieser Aktion werden Azure App Configuration-Dateien synchronisiert, wenn eine Änderung an `appsettings.json` gepusht wird. Wenn ein Entwickler eine Änderung an `appsettings.json` pusht, aktualisiert die Aktion „App Configuration-Synchronisierung“ die App Configuration-Instanz mit den neuen Werten.

Der erste Abschnitt dieses Workflows gibt an, dass die Aktion *bei* einem *Push* mit `appsettings.json` zum *Masterbranch* ausgelöst wird. Im zweiten Abschnitt werden die Aufträge aufgelistet, die nach dem Auslösen der Aktion ausgeführt werden. Die Aktion prüft die relevanten Dateien und aktualisiert die App Configuration-Instanz mithilfe der als Geheimnis im Repository gespeicherten Verbindungszeichenfolge.  Weitere Informationen zur Verwendung von Geheimnissen in GitHub finden Sie in diesem [GitHub-Artikel](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) zum Erstellen und Verwenden verschlüsselter Geheimnisse.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Verwenden einer dynamischen Bezeichnung bei Synchronisierung
Die vorherige Aktion aktualisiert die App Configuration-Instanz immer dann, wenn `appsettings.json` aktualisiert wird. Diese Aktion fügt bei jeder Synchronisierung eine dynamische Bezeichnung ein, um sicherzustellen, dass jede Synchronisierung eindeutig identifiziert werden kann, und die Zuordnung von Codeänderungen zu Konfigurationsänderungen zu ermöglichen.

Der erste Abschnitt dieses Workflows gibt an, dass die Aktion *bei* einem *Push* mit `appsettings.json` zum *Masterbranch* ausgelöst wird. Der zweite Abschnitt führt einen Auftrag aus, der basierend auf dem Commithash eine eindeutige Bezeichnung für das Konfigurationsupdate erstellt. Der Auftrag aktualisiert dann die App Configuration-Instanz mit den neuen Werten und der eindeutigen Bezeichnung für dieses Update.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Verwenden der strikten Synchronisierung
Wenn der Strict-Modus aktiviert ist, stellt die Synchronisierung sicher, dass die App Configuration-Instanz genau mit der Konfigurationsdatei für das angegebene Präfix und die Bezeichnung übereinstimmt. Schlüssel-Wert-Paare mit demselben Präfix und derselben Bezeichnung, die nicht in der Konfigurationsdatei enthalten sind, werden gelöscht. 
 
Wenn der Strict-Modus nicht aktiviert ist, werden bei der Synchronisierung nur Schlüssel-Wert-Paare aus der Konfigurationsdatei festgelegt. Es werden keine Schlüssel-Wert-Paare gelöscht. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Verwenden der maximalen Tiefe zum Einschränken der GitHub-Aktion
Das Standardverhalten für geschachtelte JSON-Attribute besteht darin, das gesamte Objekt zu vereinfachen.  Der folgende JSON-Code definiert dieses Schlüssel-Wert-Paar:

| Key | value |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Wenn das geschachtelte-Objekt der an die App Configuration-Instanz gepushte Wert sein soll, können Sie den Wert *depth* verwenden, um die Vereinfachung ab der entsprechenden Tiefe zu verhindern. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Bei einer Tiefe von 2 gibt das obige Beispiel nun das folgende Schlüssel-Wert-Paar zurück:

| Key | value |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Grundlegendes zu Aktionseingaben
Eingabeparameter geben Daten an, die von der Aktion während der Laufzeit verwendet werden.  In der folgenden Tabelle sind die von der App Configuration-Synchronisierung akzeptierten Eingabeparameter sowie die jeweils erwarteten Werte enthalten.  Weitere Informationen zu Aktionseingaben für GitHub Actions finden Sie in der GitHub-[Dokumentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Bei den Eingabe-IDs wird die Groß-/Kleinschreibung nicht berücksichtigt.


| Eingabename | Erforderlich? | value |
|----|----|----|
| configurationFile | Ja | Relativer Pfad zur Konfigurationsdatei im Repository.  Globmuster werden unterstützt und können mehrere Dateien enthalten. |
| format | Ja | Dateiformat der Konfigurationsdatei.  Gültige Formate sind: JSON, YAML, Eigenschaften. |
| connectionString | Ja | Verbindungszeichenfolge für die App Configuration-Instanz. Die Verbindungszeichenfolge muss als Geheimnis im GitHub-Repository gespeichert werden. Im Workflow sollte nur der Geheimnisname verwendet werden. |
| Trennzeichen | Ja | Trennzeichen bei der Vereinfachung der Konfigurationsdatei für Schlüssel-Wert-Paare.  Gültige Werte sind: . , ; : - _ __ / |
| prefix | Nein | Das Präfix, das am Anfang der Schlüssel hinzugefügt werden soll. |
| label | Nein | Bezeichnung, die beim Festlegen von Schlüssel-Wert-Paaren verwendet werden soll. Wenn keine Angabe erfolgt, wird eine NULL-Bezeichnung verwendet. |
| strict | Nein | Ein boolescher Wert, der bestimmt, ob der Strict-Modus aktiviert ist. Der Standardwert ist „FALSE“. |
| depth | Nein | Maximale Tiefe für das Vereinfachen der Konfigurationsdatei.  Die Tiefe muss eine positive Zahl sein.  Beim Standardwert wird keine maximale Tiefe angegeben. |
| tags | Nein | Gibt den Tagsatz für Schlüssel-Wert-Paare an.  Das erwartete Format ist eine Zeichenfolgenform eines JSON-Objekts in folgendem Format: { [Eigenschaftenname: Zeichenfolge]: Zeichenfolge; }. Jeder Eigenschaftennamenswert wird zu einem Tag. |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über die GitHub-Aktion „App Configuration-Synchronisierung“ und ihre Verwendung zum Automatisieren von Updates für Ihre App Configuration-Instanz erfahren. Um zu erfahren, wie Azure App Configuration auf Änderungen an Schlüssel-Wert-Paaren reagiert, fahren Sie mit dem nächsten [Artikel](./concept-app-configuration-event.md) fort.
