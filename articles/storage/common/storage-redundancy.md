---
title: Datenredundanz
titleSuffix: Azure Storage
description: Die Daten in Ihrem Microsoft Azure Storage-Konto werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Die Redundanzkonfiguration umfasst den lokal redundanten Speicher (LRS), den zonenredundanten Speicher (ZRS), den georedundanten Speicher (GRS), den georedundanten Speicher mit Lesezugriff (RA-GRS), den geozonenredundanten Speicher (GZRS, Vorschau) und den geozonenredundanten Speicher mit Lesezugriff (RA-GZRS, Vorschau).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7ae5f59a1bd96362d5466b2f6363185ba168d942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228326"
---
# <a name="azure-storage-redundancy"></a>Azure Storage-Redundanz

Azure Storage speichert immer mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern und Netzwerk- oder Stromausfällen bis zu schweren Naturkatastrophen usw. Durch diese Redundanz wird sichergestellt, dass Ihr Speicherkonto auch bei Ausfällen die [Servicelevelvereinbarung (SLA) für Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) erfüllt.

Berücksichtigen Sie bei der Entscheidung, welche Redundanzoption für Ihr Szenario am besten geeignet ist, die Kompromisse zwischen geringeren Kosten und höherer Verfügbarkeit sowie der Dauerhaftigkeit. Anhand der folgenden Faktoren können Sie bestimmen, welche Redundanzoption Sie auswählen sollten:  

- Wie werden Ihre Daten in der primären Region repliziert?
- Werden Ihre Daten an einen zweiten Standort repliziert, der geografisch von der primären Region entfernt ist, um Schutz vor regionalen Ausfällen zu erreichen?
- Benötigt Ihre Anwendung Lesezugriff auf die replizierten Daten in der sekundären Region, falls die primäre Region aus irgendeinem Grund nicht verfügbar ist?

## <a name="redundancy-in-the-primary-region"></a>Redundanz in der primären Region

Daten in einem Azure Storage-Konto werden immer dreimal in der primären Region repliziert. Azure Storage bietet zwei Optionen für die Replikation Ihrer Daten in der primären Region:

- **Lokal redundanter Speicher (LRS):** Die Daten werden synchron innerhalb eines einzelnen physischen Standorts in der primären Region kopiert. LRS ist die kostengünstigste Replikationsoption, wird jedoch nicht für Anwendungen empfohlen, die Hochverfügbarkeit erfordern.
- **Zonenredundanter Speicher (ZRS):** Die Daten werden synchron über drei Azure-Verfügbarkeitszonen hinweg in der primären Region kopiert. Für Anwendungen, die Hochverfügbarkeit erfordern, empfiehlt Microsoft die Verwendung von ZRS in der primären Region und auch das Replizieren in eine sekundäre Region.

### <a name="locally-redundant-storage"></a>Lokal redundanter Speicher

Bei lokal redundantem Speicher (LRS) werden die Daten innerhalb eines einzelnen physischen Standorts in der primären Region repliziert. LRS stellt eine Dauerhaftigkeit von mindestens 99,999999999 % (11 Neunen) für Objekte in einem bestimmten Jahr bereit.

LRS ist die kostengünstigste Redundanzoption und bietet im Vergleich zu anderen Optionen die geringste Dauerhaftigkeit. LRS schützt Ihre Daten vor Serverrack- und Laufwerkfehlern. Bei einem Katastrophenfall in einem Rechenzentrum (Feuer, Überschwemmung usw.) gehen jedoch eventuell alle Replikate in einem Speicherkonto, das LRS verwendet, verloren oder können nicht mehr wiederhergestellt werden. Um dieses Risiko zu minimieren, empfiehlt Microsoft, [zonenredundanten Speicher](#zone-redundant-storage) (ZRS), [georedundanten Speicher](#geo-redundant-storage) (GRS) oder [geozonenredundanten Speicher](#geo-zone-redundant-storage-preview) (GZRS, Vorschau) zu verwenden.

Schreibanforderungen an ein Speicherkonto, das LRS verwendet, erfolgen synchron. Die Schreibanforderung wird erst dann erfolgreich zurückgegeben, nachdem die Daten in alle drei Replikate geschrieben wurden.

LRS ist eine gute Wahl für die folgenden Szenarien:

- Wenn Ihre Anwendung Daten speichert, die bei Datenverlust problemlos wiederhergestellt werden können, können Sie sich für LRS entscheiden.
- Wenn Ihre Anwendungen aufgrund von Datengovernancevorschriften auf die Replikation von Daten innerhalb eines Lands/einer Region beschränkt ist, können Sie LRS verwenden. In einigen Fällen können sich die gekoppelten Regionen, in denen die Daten georepliziert werden, in einem anderen Land oder einer anderen Region befinden. Weitere Informationen zu gekoppelten Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zonenredundanter Speicher

Bei zonenredundantem Speicher (ZRS) werden die Daten synchron über drei Azure-Verfügbarkeitszonen hinweg in der primären Region repliziert. Jede Verfügbarkeitszone ist ein getrennter physischer Standort mit unabhängigen Stromversorgungs-, Kühlungs- und Netzwerkgeräten. ZRS bietet eine Dauerhaftigkeit für Azure Storage-Datenobjekte von mindestens 99,9999999999 % (12-mal die Zahl 9) über einen Zeitraum von einem Jahr.

Auf Ihre Daten kann mit ZRS weiterhin von Lese- und Schreibvorgängen zugegriffen werden, auch wenn eine Zone nicht mehr verfügbar ist. Wenn eine Zone nicht verfügbar ist, führt Azure Netzwerkupdates durch, z. B. durch die Festlegung neuer DNS-Ziele. Diese Updates können sich auf Ihre Anwendung auswirken, wenn Sie auf Daten zugreifen, bevor die Updates abgeschlossen sind. Halten Sie beim Entwerfen von Anwendungen für ZRS die Vorgehensweisen für vorübergehende Fehler ein. Dazu gehört u. a. die Implementierung von Wiederholungsrichtlinien mit exponentiellem Backoff.

Schreibanforderungen an ein Speicherkonto, das ZRS verwendet, erfolgen synchron. Die Schreibanforderung wird erst dann erfolgreich zurückgegeben, nachdem die Daten in alle Replikate in den drei Verfügbarkeitszonen geschrieben wurden.

Microsoft empfiehlt die Verwendung von ZRS in der primären Region für Szenarien, die Konsistenz, Dauerhaftigkeit und Hochverfügbarkeit erfordern. ZRS bietet hervorragende Leistung, geringe Latenz und Resilienz für Ihre Daten, wenn diese vorübergehend nicht verfügbar sind. ZRS selbst kann Ihre Daten jedoch nicht vor einem regionalen Notfall schützen, bei dem mehrere Zonen dauerhaft betroffen sind. Für den Schutz vor regionalen Notfällen empfiehlt Microsoft die Verwendung von [geozonenredundantem Speicher](#geo-zone-redundant-storage-preview) (GZRS), der ZRS in der primären Region verwendet und die Daten in eine sekundäre Region georepliziert.

In der folgenden Tabelle wird gezeigt, welche Typen von Speicherkonten ZRS in welchen Regionen unterstützen:

|    Speicherkontotyp    |    Unterstützte Regionen    |    Unterstützte Dienste    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Universell v2<sup>1</sup>    | Asien, Südosten<br /> Australien (Osten)<br /> Europa, Norden<br />  Europa, Westen<br /> Frankreich, Mitte<br /> Japan, Osten<br /> Südafrika, Norden<br /> UK, Süden<br /> USA, Mitte<br /> USA, Osten<br /> USA (Ost 2)<br /> USA, Westen 2    |    Blockblobs<br /> Seitenblobs<sup>2</sup><br /> Dateifreigaben (Standard)<br /> Tabellen<br /> Warteschlangen<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa, Westen<br /> USA, Osten    |    Nur Blockblobs    |
|    FileStorage    | Europa, Westen<br /> USA, Osten    |    Nur Azure Files    |

<sup>1</sup> Die Archivspeicherebene wird derzeit nicht für ZRS-Konten unterstützt.<br />
<sup>2</sup> Speicherkonten, die verwaltete Azure-Datenträger für virtuelle Computer enthalten, verwenden immer LRS. Für nicht verwaltete Azure-Datenträger sollte ebenfalls LRS verwendet werden. Es ist möglich, ein Speicherkonto für nicht verwaltete Azure-Datenträger zu erstellen, das GRS verwendet. Dies wird jedoch aufgrund von potenziellen Konsistenzproblemen mit der asynchronen Georeplikation nicht empfohlen. Weder verwaltete noch nicht verwaltete Datenträger unterstützen ZRS oder GZRS. Weitere Informationen zu verwalteten Datenträgern finden Sie unter [Azure Managed Disks – Preise](https://azure.microsoft.com/pricing/details/managed-disks/).

Informationen zu den Regionen, in denen ZRS unterstützt wird, finden Sie unter **Unterstützung der Dienste nach Region** in [Was sind Azure-Verfügbarkeitszonen?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundanz in einer sekundären Region

Bei Anwendungen, die Hochverfügbarkeit erfordern, können Sie die Daten in Ihrem Speicherkonto zusätzlich in eine sekundäre Region kopieren, die Hunderte von Kilometern von der primären Region entfernt ist. Wenn Ihr Speicherkonto in eine sekundäre Region kopiert wird, sind Ihre Daten dauerhaft gespeichert, selbst bei einem regionalen Komplettausfall oder einem Notfall, nach dem die primäre Region nicht mehr wiederhergestellt werden kann.

Wenn Sie ein Speicherkonto erstellen, wählen Sie die primäre Region für das Konto aus. Die gekoppelte sekundäre Region wird basierend auf der primären Region bestimmt und kann nicht geändert werden. Weitere Informationen zu von Azure unterstützten Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage bietet zwei Optionen für das Kopieren Ihrer Daten in eine sekundäre Region:

- **Georedundanter Speicher (GRS):** Die Daten werden synchron dreimal innerhalb eines einzelnen physischen Standorts in der primären Region mit LRS kopiert. Anschließend werden die Daten asynchron an einen einzelnen physischen Standort in der sekundären Region kopiert.
- **Geozonenredundanter Speicher (GZRS, Vorschau):** Die Daten werden mit ZRS synchron über drei Azure-Verfügbarkeitszonen hinweg in der primären Region kopiert. Anschließend werden die Daten asynchron an einen einzelnen physischen Standort in der sekundären Region kopiert.

Der Hauptunterschied zwischen GRS und GZRS besteht in der Art, wie Daten in der primären Region repliziert werden. Am sekundären Standort werden die Daten immer dreimal synchron mithilfe von LRS repliziert.

Mit GRS oder GZRS sind die Daten am sekundären Standort nicht für Lese- oder Schreibzugriffe verfügbar, sofern kein Failover in die sekundäre Region ausgeführt wird. Für den Lesezugriff am sekundären Standort konfigurieren Sie Ihr Speicherkonto für die Verwendung von georedundantem Speicher mit Lesezugriff (RA-GRS) oder geozonenredundantem Speicher mit Lesezugriff (RA-GZRS). Weitere Informationen finden Sie unter [Lesezugriff auf Daten in der sekundären Region](#read-access-to-data-in-the-secondary-region).

Wenn die primäre Region nicht verfügbar ist, können Sie ein Failover in die sekundäre Region (in der Vorschauphase) ausführen. Nachdem das Failover abgeschlossen ist, wird die sekundäre Region zur primären Region, und Sie können wieder Daten lesen und schreiben. Weitere Informationen zur Notfallwiederherstellung und zum Failover in die sekundäre Region finden Sie unter [Notfallwiederherstellung und Kontofailover (Vorschauversion)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Da Daten asynchron in die sekundären Region repliziert werden, kann ein Fehler, der sich auf die primäre Region auswirkt, zu Datenverlusten führen, wenn die primäre Region nicht wiederhergestellt werden kann. Das Intervall zwischen den letzten Schreibvorgängen in der primären Region und dem letzten Schreibvorgang in der sekundären Region wird als RPO (Recovery Point Objective) bezeichnet. Die RPO gibt den Zeitpunkt an, auf den Daten wiederhergestellt werden können. Azure Storage weist normalerweise einen RPO-Wert von weniger als 15 Minuten auf, aber es gibt derzeit keine SLA zur Dauer der Replikation in die sekundäre Region.

### <a name="geo-redundant-storage"></a>Georedundanter Speicher

Bei georedundantem Speicher (GRS) werden die Daten synchron dreimal innerhalb eines einzelnen physischen Standorts in der primären Region mit LRS kopiert. Anschließend werden Ihre Daten asynchron an einen physischen Standort in einer sekundären Region kopiert, die Hunderte von Kilometern von der primären Region entfernt ist. GRS bietet eine Dauerhaftigkeit für Azure Storage-Datenobjekte von mindestens 99,99999999999999 % (16-mal die Zahl 9) über einen Zeitraum von einem Jahr.

Ein Schreibvorgang wird zunächst an den primären Speicherort übertragen und mit LRS repliziert. Anschließend wird das Update asynchron in die sekundäre Region repliziert. Wenn Daten in den sekundären Speicherort geschrieben werden, werden sie dort auch mit LRS repliziert.

### <a name="geo-zone-redundant-storage-preview"></a>Geozonenredundanter Speicher (Vorschauversion)

Mit geozonenredundantem Speicher (GZRS, Vorschauversion) wird die Hochverfügbarkeit durch Redundanz über Verfügbarkeitszonen hinweg mit dem Schutz vor regionalen Ausfällen kombiniert, der durch Georeplikation bereitgestellt wird. Daten in einem GZRS-Speicherkonto werden über drei [Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md) in die primäre Region kopiert sowie auch in eine sekundäre geografische Region zum Schutz vor regionalen Notfällen. Microsoft empfiehlt die Verwendung von GZRS für Anwendungen, die maximale Konsistenz, Dauerhaftigkeit und Verfügbarkeit, hervorragende Leistung und Resilienz bei der Notfallwiederherstellung erfordern.

Mit einem GZRS-Speicherkonto können Sie weiterhin Daten lesen und schreiben, wenn eine Verfügbarkeitszone nicht verfügbar oder nicht wiederherstellbar ist. Außerdem sind Ihre Daten auch bei einem regionalen Komplettausfall oder einem Notfall beständig gespeichert, nach dem die primäre Region nicht mehr wiederherstellbar ist. GZRS ist darauf ausgelegt, für Objekte eine Dauerhaftigkeit von mindestens 99,99999999999999 Prozent (16 Neunen) in einem bestimmten Jahr bereitzustellen.

Nur Speicherkonten vom Typ „Allgemein v2“ unterstützen GZRS und RA-GZRS. Weitere Informationen zu Arten von Speicherkontotypen finden Sie unter [Übersicht über Azure Storage-Konten](storage-account-overview.md). GZRS und RA-GZRS unterstützen Blockblobs, Seitenblobs (mit Ausnahme von VHD-Datenträgern), Dateien, Tabellen und Warteschlangen.

GZRS und RA-GZRS sind derzeit in den folgenden Regionen als Vorschau verfügbar:

- Asien, Südosten
- Europa, Norden
- Europa, Westen
- Japan, Osten
- UK, Süden
- USA, Osten
- USA (Ost 2)
- USA, Mitte
- USA, Westen 2

Microsoft arbeitet daran, GZRS und RA-GZRS in weiteren Azure-Regionen zu aktivieren. Sehen Sie regelmäßig auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/) nach, um Informationen zu unterstützten Regionen zu erhalten.

Weitere Informationen zu den Preisen für die Vorschauversion finden Sie unter den GZRS-Vorschaupreisen für [Blobs](https://azure.microsoft.com/pricing/details/storage/blobs), [Dateien](https://azure.microsoft.com/pricing/details/storage/files/), [Warteschlangen](https://azure.microsoft.com/pricing/details/storage/queues/) und [Tabellen](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft empfiehlt, Vorschaufunktionen nicht für Produktionsworkloads zu verwenden.

## <a name="read-access-to-data-in-the-secondary-region"></a>Lesezugriff auf Daten in der sekundären Region

Georedundanter Speicher (mit GRS oder GZRS) repliziert Ihre Daten an einen anderen physischen Standort in der sekundären Region, um sie vor regionalen Ausfällen zu schützen. Diese Daten sind jedoch nur dann lesbar, wenn der Kunde oder Microsoft ein Failover von der primären zur sekundären Region initiiert. Wenn Sie Lesezugriff auf die sekundäre Region aktivieren, können Ihre Daten gelesen werden, falls die primäre Region nicht mehr verfügbar ist. Für den Lesezugriff in der sekundären Region aktivieren Sie georedundanten Speicher mit Lesezugriff (RA-GRS) oder geozonenredundanten Speicher mit Lesezugriff (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Entwerfen von Anwendungen für den Lesezugriff am sekundären Standort

Wenn Ihr Speicherkonto für den Lesezugriff in der sekundären Region konfiguriert ist, können Sie Ihre Anwendungen so entwerfen, dass sie nahtlos zum Lesen von Daten in der sekundären Region wechseln, wenn die primäre Region aus irgendeinem Grund nicht verfügbar ist. Die sekundäre Region ist immer für den Lesezugriff verfügbar, sodass Sie Ihre Anwendung testen können, um sicherzustellen, dass sie bei einem Ausfall am sekundären Standort liest. Weitere Informationen zum Entwerfen von Anwendungen für Hochverfügbarkeit finden Sie unter [Entwerfen von hochverfügbaren Anwendungen mithilfe von georedundantem Speicher mit Lesezugriff](storage-designing-ha-apps-with-ragrs.md).

Wenn der Lesezugriff auf den sekundären Standort aktiviert ist, können Ihre Daten sowohl vom sekundären Endpunkt als auch vom primären Endpunkt für Ihr Speicherkonto gelesen werden. Der sekundäre Endpunkt fügt das Suffix *–secondary* an den Kontonamen an. Wenn Ihr primärer Endpunkt für Blob Storage z. B. `myaccount.blob.core.windows.net` ist, lautet Ihr sekundärer Endpunkt `myaccount-secondary.blob.core.windows.net`. Die Zugriffsschlüssel für das Speicherkonto sind für die primären und sekundären Endpunkte identisch.

### <a name="check-the-last-sync-time-property"></a>Überprüfen der Eigenschaft für die letzte Synchronisierung

Da Daten asynchron in die sekundären Region repliziert werden, liegt die sekundäre Region oft hinter der primären Region zurück. Wenn in der primären Region ein Fehler auftritt, wurden sämtliche Schreibvorgänge im primären Replikat wahrscheinlich noch nicht in die sekundäre Region repliziert.

Um zu ermitteln, welche Schreibvorgänge in die sekundäre Region repliziert wurden, kann Ihre Anwendung die Eigenschaft **Letzte Synchronisierung** für Ihr Speicherkonto überprüfen. Alle Schreibvorgänge, die vor der letzten Synchronissierungszeit in die primäre Region geschrieben wurden, wurden erfolgreich in die sekundäre Region repliziert, sodass sie für das Lesen aus der sekundären Region zur Verfügung stehen. Alle Schreibvorgänge, die nach der letzten Synchronisationszeit in die primäre Region geschrieben wurden, können ggf. in die sekundäre Region repliziert worden sein (oder auch nicht), sodass sie möglicherweise nicht für Lesevorgänge zur Verfügung stehen.

Sie können den Wert der Eigenschaft **Last Sync Time** (Letzte Synchronisierungszeit) mit Azure PowerShell, der Azure CLI oder einer der Azure Storage-Clientbibliotheken abfragen. Die Eigenschaft **Last Sync Time** ist ein GMT-Datums-/Uhrzeitwert. Weitere Informationen finden Sie unter [Überprüfen der Eigenschaft „Letzte Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Zusammenfassung der Redundanzoptionen

In der folgenden Tabelle wird gezeigt, wie dauerhaft und verfügbar Ihre Daten in einem bestimmten Szenario je nach Redundanztyp für Ihr Speicherkonto sind:

| Szenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (Vorschauversion)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Ein Knoten innerhalb eines Rechenzentrums steht nicht mehr zur Verfügung.                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ein gesamtes Rechenzentrum (zonal oder nicht zonal) ist nicht mehr verfügbar.                                           | Nein                              | Ja                              | Ja                                  | Ja                                  |
| Ein regionsweiter Ausfall tritt auf.                                                                                     | Nein                              | Nein                               | Ja                                  | Ja                                  |
| Wenn die primäre Region nicht verfügbar ist, erfolgt der Lesezugriff in der sekundären Region. | Nein                              | Nein                               | Ja (mit RA-GRS)                                   | Ja (mit RA-GZRS)                                 |
| Prozentuale Dauerhaftigkeit von Objekten über ein bestimmtes Jahr<sup>1</sup>                                          | mindestens 99,999999999 % (11 mal die 9) | mindestens 99,9999999999 % (12 mal die 9) | mindestens 99,99999999999999 % (16 mal die 9) | mindestens 99,99999999999999 % (16 mal die 9) |
| Unterstützte Speicherkontotypen<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Verfügbarkeits-SLA für Leseanforderungen<sup>1</sup>  | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) für GRS<br /><br />mindestens 99,99 % (99,9 % bei der kalten Zugriffsebene) für RA-GRS | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) für GZRS<br /><br />mindestens 99,99 % (99,9 % bei der kalten Zugriffsebene) für RA-GZRS |
| Verfügbarkeits-SLA für Schreibanforderungen<sup>1</sup>  | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) |

<sup>1</sup> Weitere Informationen zu Azure Storage-Garantien in Bezug auf Dauerhaftigkeit und Verfügbarkeit finden Sie im [SLA für Speicherkonten](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Weitere Informationen zu Speicherkontotypen finden Sie unter [Übersicht über Azure Storage-Konten](storage-account-overview.md).

Alle Daten für alle Typen von Speicherkonten werden gemäß der Redundanzoption für das Speicherkonto kopiert. Es werden Objekte einschließlich Blockblobs, Anfügeblobs, Seitenblobs, Warteschlangen, Tabellen und Dateien kopiert.

Die Preise für die verschiedenen Redundanzoptionen finden Sie unter [Azure Storage – Preise](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Disk Storage Premium unterstützt derzeit nur lokal redundanten Speicher (LRS). Blockblob-Speicherkonten unterstützen lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS) in bestimmten Regionen.

## <a name="data-integrity"></a>Datenintegrität

Azure Storage überprüft regelmäßig die Integrität der gespeicherten Daten mithilfe von Cyclic Redundancy Checks (CRCs). Wenn eine Datenbeschädigung erkannt wird, wird sie unter Verwendung von redundanten Daten repariert. Azure Storage berechnet auch die Prüfsummen für den gesamten Netzwerkdatenverkehr, um eine Beschädigung von Datenpaketen beim Speichern oder Abrufen von Daten zu erkennen.

## <a name="see-also"></a>Weitere Informationen

- [Überprüfen der Eigenschaft „Letzte Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md)
- [Ändern der Redundanzoption für ein Speicherkonto](redundancy-migration.md)
- [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS-Speicher](../storage-designing-ha-apps-with-ragrs.md)
- [Notfallwiederherstellung und Kontofailover (Vorschauversion)](storage-disaster-recovery-guidance.md)
