---
title: Wiederherstellen von Azure Files mit PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Files mit dem Azure Backup-Dienst und PowerShell wiederherstellen.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086830"
---
# <a name="restore-azure-files-with-powershell"></a>Wiederherstellen von Azure Files mit PowerShell

In diesem Artikel wird erläutert, wie Sie eine vollständige Dateifreigabe oder bestimmte Dateien von einem vom [Azure Backup](backup-overview.md)-Dienst erstellten Wiederherstellungspunkt mithilfe von Azure PowerShell wiederherstellten.

Sie können eine gesamte Dateifreigabe oder aber bestimmte Dateien aus dieser Freigabe wiederherstellen. Dabei haben Sie die Möglichkeit, eine Wiederherstellung am ursprünglichen oder an einem alternativen Speicherort durchzuführen.

> [!WARNING]
> Stellen Sie sicher, dass die PS-Version auf die Mindestversion für „Az.RecoveryServices 2.6.0“ für AFS-Sicherungen aktualisiert wird. Weitere Informationen finden Sie im [Abschnitt](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) zur Anforderung für diese Änderung.

## <a name="fetch-recovery-points"></a>Abrufen von Wiederherstellungspunkten

Verwenden Sie [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0), um alle Wiederherstellungspunkte für das gesicherte Element aufzulisten.

Für das unten aufgeführte Skript gilt Folgendes:

* Die Variable **$rp** ist ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage.
* Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index **0**.
* Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts.
* Im Beispiel wählt **$rp[0]** den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Die Ausgabe sieht in etwa wie folgt aus:

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Nachdem der entsprechende Wiederherstellungspunkt ausgewählt wurde, stellen Sie die Dateifreigabe oder Datei am ursprünglichen oder an einem alternativen Speicherort wieder her.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Wiederherstellen einer Azure-Dateifreigabe an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort festzulegen:

* **TargetStorageAccountName**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **TargetFileShareName**: Die Dateifreigaben in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **TargetFolder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
* **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

Führen Sie das Cmdlet mit den Parametern wie folgt aus:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Der Befehl gibt einen Auftrag mit einer ID zur Nachverfolgung zurück, wie im folgenden Beispiel gezeigt wird.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Wiederherstellen einer Azure-Datei an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort und die wiederherzustellende Datei eindeutig festzulegen:

* **TargetStorageAccountName**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **TargetFileShareName**: Die Dateifreigaben in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **TargetFolder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
* **SourceFilePath**: Der absolute Pfad der (in der Dateifreigabe wiederherzustellenden) Datei als Zeichenfolge. Dieser Pfad ist derselbe Pfad, der im PowerShell-Cmdlet **Get-AzStorageFile** verwendet wird.
* **SourceFileType**: Gibt an, ob ein Verzeichnis oder eine Datei ausgewählt wurde. Für diesen Parameter kann **Directory** oder **File** angegeben werden.
* **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

Die zusätzlichen Parameter („SourceFilePath“ und „SourceFileType“) beziehen sich nur auf die Datei, die Sie wiederherstellen möchten.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Der Befehl gibt einen Auftrag mit einer ID zur Nachverfolgung zurück, wie im vorherigen Abschnitt gezeigt wurde.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Wiederherstellen von Azure-Dateifreigaben und -Dateien am ursprünglichen Speicherort

Wenn Sie an einem ursprünglichen Speicherort eine Wiederherstellung vornehmen, müssen Sie nicht alle Zielparameter und zielbezogenen Parameter angeben. Nur **ResolveConflict** muss angegeben werden.

#### <a name="overwrite-an-azure-file-share"></a>Überschreiben einer Azure-Dateifreigabe

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Überschreiben einer Azure-Datei

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Wiederherstellung von Azure Files](restore-afs.md) im Azure-Portal.
