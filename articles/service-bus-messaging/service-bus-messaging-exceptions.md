---
title: Leitfaden zur Problembehandlung für Azure Service Bus | Microsoft-Dokumentation
description: Dieser Artikel stellt eine Liste von Azure Service Bus-Messagingausnahmen und vorgeschlagenen Aktionen zur Verfügung, wenn eine Ausnahme auftritt.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37f316af68bc0b20f21eb606e2abc8232f29ce32
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759362"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Leitfaden zur Problembehandlung für Azure Service Bus
Dieser Artikel beschreibt einige der .NET-Ausnahmen, die von Service Bus .NET Framework-APIs generiert werden, und enthält weitere Tipps zur Problembehandlung. 

## <a name="service-bus-messaging-exceptions"></a>Service Bus-Messagingausnahmen
In diesem Abschnitt werden die von .NET Framework-APIs generierten .NET-Ausnahmen aufgelistet. 

### <a name="exception-categories"></a>Ausnahmekategorien
Die von den Messaging-APIs generierten Ausnahmen können – zusammen mit den zugehörigen Korrekturmaßnahmen – zu den folgenden Kategorien gehören. Die Bedeutung und die Ursachen einer Ausnahme können je nach Art der Messagingentität variieren:

1. Fehler der Benutzercodierung ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allgemeine Maßnahme: Korrigieren Sie den Code, bevor Sie fortfahren.
2. Fehler bei der Einrichtung oder Konfiguration: ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allgemeine Maßnahme: Überprüfen Sie die Konfiguration, und ändern Sie diese gegebenenfalls.
3. Vorübergehende Ausnahmen ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allgemeine Maßnahme: Wiederholen Sie den Vorgang, oder benachrichtigen Sie die Benutzer. Beachten Sie, dass die `RetryPolicy`-Klasse im Client-SDK so konfiguriert werden kann, dass Wiederholungsversuche automatisch verarbeitet werden. Weitere Informationen finden Sie in im [Leitfaden zu Wiederholungsversuchen](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Andere Ausnahmen ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allgemeine Aktion: spezifisch für den Typ der Ausnahme, weitere Informationen finden Sie in der Tabelle im folgenden Abschnitt. 

### <a name="exception-types"></a>Ausnahmetypen
In der folgenden Tabelle werden die Typen von Messagingausnahmen, ihre Ursachen und Vorschläge für Korrekturmaßnahmen aufgelistet.

| **Ausnahmetyp** | **Beschreibung/Ursache/Beispiele** | **Vorgeschlagene Maßnahme** | **Hinweis zur automatischen/sofortigen Wiederholung** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Der Server hat nicht innerhalb der von [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) vorgegebenen Zeit auf den angeforderten Vorgang reagiert. Unter Umständen hat der Server den angeforderten Vorgang abgeschlossen. Dies kann aufgrund von Verzögerungen im Netzwerk oder in der Infrastruktur auftreten. |Überprüfen Sie den Systemzustand auf Konsistenz, und wiederholen Sie den Vorgang bei Bedarf. Siehe [Timeoutausnahmen](#timeoutexception). |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Der angeforderte Benutzervorgang ist auf dem Server oder innerhalb des Diensts unzulässig. Sehen Sie sich die Details in der Ausnahmemeldung an. Beispielsweise generiert [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) diese Ausnahme, wenn die Meldung im [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)-Modus empfangen wurde. |Überprüfen Sie den Code und die Dokumentation. Stellen Sie sicher, dass der angeforderte Vorgang gültig ist. |Der Wiederholungsversuch ist nicht hilfreich. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Es wurde versucht, einen Vorgang für ein Objekt aufzurufen, das bereits geschlossen, abgebrochen oder verworfen wurde. In seltenen Fällen wurde die Ambient-Transaktion bereits verworfen. |Überprüfen Sie den Code, und stellen Sie sicher, dass keine Vorgänge für verworfene Objekte aufgerufen werden. |Der Wiederholungsversuch ist nicht hilfreich. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Das [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider)-Objekt konnte kein Token abrufen, das Token ist ungültig, oder das Token enthält nicht die Ansprüche, die zum Ausführen des Vorgangs erforderlich sind. |Stellen Sie sicher, dass der Tokenanbieter mit den richtigen Werten erstellt wird. Überprüfen Sie die Konfiguration für den Access Control Service. |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Mindestens eines der für die Methode bereitgestellten Argumente ist ungültig.<br /> Der für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI enthält Pfadsegmente.<br /> Das für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder für [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI-Schema ist ungültig. <br />Der Eigenschaftswert ist größer als 32 KB. |Überprüfen Sie den aufrufenden Code, und stellen Sie sicher, dass die Argumente richtig sind. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Die dem Vorgang zugeordnete Entität ist nicht vorhanden oder wurde gelöscht. |Stellen Sie sicher, dass die Entität vorhanden ist. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Es wurde versucht, eine Nachricht mit einer bestimmten Sequenznummer zu empfangen. Diese Nachricht wurde nicht gefunden. |Stellen Sie sicher, dass die Nachricht nicht bereits empfangen wurde. Überprüfen Sie in der Warteschlange für unzustellbare Nachrichten, ob die Nachricht als unzustellbar gekennzeichnet wurde. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Der Client kann keine Verbindung mit Service Bus herstellen. |Stellen Sie sicher, dass der angegebene Hostname richtig und der Host erreichbar ist. |Eine Wiederholung kann helfen, wenn zeitweilige Verbindungsprobleme vorliegen. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Der Dienst kann die Anforderung derzeit nicht verarbeiten. |Der Client kann eine gewisse Zeit warten und dann den Vorgang wiederholen. |Der Client kann den Vorgang nach einer gewissen Zeitspanne wiederholen. Wenn die Wiederholung zu einer anderen Ausnahme führt, überprüfen Sie das Wiederholungsverhalten dieser Ausnahme. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Das der Nachricht zugeordnete Sperrtoken ist abgelaufen, oder das Sperrtoken wurde nicht gefunden. |Verwerfen Sie die Nachricht. |Der Wiederholungsversuch ist nicht hilfreich. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Die dieser Sitzung zugeordnete Sperre ist verloren gegangen. |Brechen Sie das [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -Objekt ab. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Eine allgemeine Messagingausnahme, die in folgenden Fällen ausgelöst werden kann:<br /> Es wird versucht, ein [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)-Element mit einem Namen oder einem Pfad zu erstellen, der zu einem anderen Entitätstyp gehört (z.B. zu einem Thema).<br />  Es wird versucht, eine Nachricht zu senden, die größer als 256 KB ist. Der Server oder der Dienst hat beim Verarbeiten der Anforderung einen Fehler festgestellt. Sehen Sie sich die Details in der Ausnahmemeldung an. Dies ist in der Regel eine vorübergehende Ausnahme. |Überprüfen Sie den Code, und stellen Sie sicher, dass nur serialisierbare Objekte für den Nachrichtentext verwendet werden (oder verwenden Sie ein benutzerdefiniertes Serialisierungsprogramm). Überprüfen Sie die Dokumentation für die unterstützten Werttypen der Eigenschaften, und verwenden Sie nur unterstützte Typen. Überprüfen Sie die [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) -Eigenschaft. Wenn sie den Wert **TRUE**aufweist, können Sie versuchen, den Vorgang zu wiederholen. |Das Verhalten einer Wiederholung ist nicht definiert, u. U. hilft sie nicht. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Es wurde versucht, eine Entität mit einem Namen zu erstellen, der bereits von einer anderen Entität in diesem Dienstnamespace verwendet wird. |Löschen Sie die vorhandene Entität, oder wählen Sie einen anderen Namen für die zu erstellende Entität. |Der Wiederholungsversuch ist nicht hilfreich. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Die Messagingentität hat die maximal zulässige Größe erreicht, oder die maximale Anzahl von Verbindungen zu einem Namespace wurde überschritten. |Schaffen Sie Platz in der Entität, indem Sie Nachrichten aus der Entität oder ihren Unterwarteschlangen empfangen. Siehe [QuotaExceededException](#quotaexceededexception). |Eine Wiederholung kann helfen, wenn in der Zwischenzeit Nachrichten entfernt wurden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie versuchen, eine ungültige Regelaktion zu erstellen. Service Bus fügt diese Ausnahme zu einer unzustellbaren Nachricht hinzu, wenn beim Verarbeiten der Regelaktion für diese Nachricht ein Fehler festgestellt wird. |Überprüfen Sie die Regelaktion auf ihre Richtigkeit. |Der Wiederholungsversuch ist nicht hilfreich. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie versuchen, einen ungültigen Filter zu erstellen. Service Bus fügt diese Ausnahme zu einer unzustellbaren Nachricht hinzu, wenn beim Verarbeiten des Filters für diese Nachricht ein Fehler festgestellt wird. |Überprüfen Sie den Filter auf Richtigkeit. |Der Wiederholungsversuch ist nicht hilfreich. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Es wurde versucht, eine Sitzung mit einer bestimmten Sitzungs-ID zu akzeptieren, aber die Sitzung wird derzeit durch einen anderen Client gesperrt. |Stellen Sie sicher, dass die Sperre für die Sitzung von den anderen Clients aufgehoben wird. |Eine Wiederholung kann helfen, wenn die Sitzung in der Zwischenzeit freigegeben wurde. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Die Transaktion umfasst zu viele Vorgänge. |Reduzieren Sie die Anzahl der Vorgänge, die Teil dieser Transaktion sind. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Es wurde ein Laufzeitvorgang für eine deaktivierte Entität angefordert. |Aktivieren Sie die Entität. |Eine Wiederholung kann helfen, wenn die Entität in der Zwischenzeit aktiviert wurde. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie eine Nachricht zu einem Thema senden, für das eine Vorfilterung aktiviert wurde. |Stellen Sie sicher, dass mindestens ein Filter übereinstimmt. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Eine Nachrichtennutzlast überschreitet den Grenzwert von 256 KB. Der Grenzwert von 256 KB gilt für die Gesamtgröße der Nachricht, zu der auch Systemeigenschaften und .NET-Mehraufwand gehören. |Reduzieren Sie die Größe der Nachrichtennutzlast, und wiederholen Sie den Vorgang. |Der Wiederholungsversuch ist nicht hilfreich. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Die Ambient-Transaktion (*Transaction.Current*) ist ungültig. Sie wurde möglicherweise abgeschlossen oder abgebrochen. Unter Umständen enthält die innere Ausnahme weitere Informationen. | |Der Wiederholungsversuch ist nicht hilfreich. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Es wurde versucht, einen Vorgang für eine unsichere Transaktion auszuführen, oder es wurde versucht, ein Commit für die Transaktion auszuführen, und die Transaktion wurde unsicher. |Die Anwendung muss diese Ausnahme (als Sonderfall) behandeln, da für die Transaktion u. U. bereits ein Commit ausgeführt wurde. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) gibt an, dass das Kontingent für eine bestimmte Entität überschritten wurde.

#### <a name="queues-and-topics"></a>Warteschlangen und Themen
Für Warteschlangen und Themen ist dies häufig die Größe der Warteschlange. Die Fehlermeldungseigenschaft enthält weitere Details, wie das folgende Beispiel zeigt:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Die Meldung besagt, dass das Thema seine maximale Größe überschritten hat, in diesem Fall 1GB (Standardgrenzwert). 

#### <a name="namespaces"></a>Namespaces

Für Namespaces kann [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) angeben, dass eine Anwendung die maximale Anzahl der Verbindungen zu einem Namespace überschritten hat. Beispiel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Häufige Ursachen
Es gibt zwei häufige Ursachen für diesen Fehler: die Warteschlange für unzustellbare Nachrichten und nicht funktionierende Nachrichtenempfänger.

1. **[Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md)** : Ein Leser kann Nachrichten nicht abschließen, und die Nachrichten werden nach Ablauf der Sperre an die Warteschlange/das Thema zurückgegeben. Dies kann auftreten, wenn der Leser auf eine Ausnahme trifft, die den Aufruf von [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) verhindert. Nachdem eine Nachricht 10 Mal gelesen wurde, wird sie standardmäßig in die Warteschlange für unzustellbare Nachrichten verschoben. Dieses Verhalten wird von der Eigenschaft [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) gesteuert, die standardmäßig den Wert 10 aufweist. Wenn sich Nachrichten in der Warteschlange für unzustellbare Nachrichten anhäufen, belegen sie Speicherplatz.
   
    Um das Problem zu beheben, lesen und schließen Sie die Nachrichten in der Warteschlange für unzustellbare Nachrichten ab – genau wie in jeder anderen Warteschlange. Sie können die Methode [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) verwenden, die Sie beim Formatieren des Pfads für die Warteschlange für unzustellbare Nachrichten unterstützt.
2. **Empfänger beendet**. Ein Empfänger hat den Empfang von Nachrichten aus einer Warteschlange oder einem Abonnement beendet. Dies können Sie anhand der Eigenschaft [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) feststellen, die die vollständige Aufschlüsselung der Nachrichten zeigt. Wenn die Eigenschaft [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) hoch ist oder dieser wert anwächst, werden die Nachrichten nicht so schnell gelesen, wie sie geschrieben werden.

### <a name="timeoutexception"></a>TimeoutException
Eine [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) zeigt an, dass ein von einem Benutzer initiierter Vorgang länger als das Timeout des Vorgangs dauert. 

Überprüfen Sie den Wert der [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit)-Eigenschaft, da das Erreichen dieses Limits auch eine [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) auslösen kann.

#### <a name="queues-and-topics"></a>Warteschlangen und Themen
Für Warteschlangen und Themen wird das Zeitlimit entweder in der [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)-Eigenschaft als Teil der Verbindungszeichenfolge oder über [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) angegeben. Die Fehlermeldung selbst kann variieren, aber sie enthält immer den Timeoutwert für den aktuellen Vorgang. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Konnektivitäts-, Zertifikat- oder Timeoutprobleme
Die folgenden Schritte unterstützen Sie bei der Problembehandlung von Konnektivitäts-/Zertifikat-/Timeoutproblemen für alle Dienste unter *.servicebus.windows.net. 

- Navigieren Sie zu `https://<yournamespace>.servicebus.windows.net/`, oder verwenden Sie [wget](https://www.gnu.org/software/wget/). Dies hilft bei der Überprüfung, ob Probleme mit der IP-Filterung oder dem virtuellen Netzwerk bzw. der Zertifikatkette vorliegen (häufiges Problem bei Verwendung des Java SDK).

    Beispiel für eine erfolgreiche Meldung:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Beispiel für eine Fehlermeldung:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Führen Sie den folgenden Befehl aus, um zu überprüfen, ob ein Port auf der Firewall blockiert ist. Die verwendeten Ports lauten 443 (HTTPS), 5671 (AMQP) und 9354 (.NET-Messaging/SBMP). Abhängig von der verwendeten Bibliothek werden auch andere Ports verwendet. Dies ist der Beispielbefehl, mit dem überprüft wird, ob Port 5671 blockiert ist. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Unter Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Führen Sie bei zeitweiligen Konnektivitätsproblemen den folgenden Befehl aus, um zu überprüfen, ob gelöschte Pakete vorhanden sind. Mit diesem Befehl wird versucht, jede Sekunde 25 verschiedene TCP-Verbindungen mit dem Dienst herzustellen. Anschließend können Sie überprüfen, wie viele davon erfolgreich/fehlerhaft waren, und außerdem die Latenz der TCP-Verbindung anzeigen. Sie können das `psping`-Tool [hier](/sysinternals/downloads/psping) herunterladen.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Sie können äquivalente Befehle verwenden, wenn Sie andere Tools wie `tnc`, `ping` usw. nutzen. 
- Rufen Sie eine Netzwerkablaufverfolgung ab, wenn die vorherigen Schritte nicht hilfreich sind, und analysieren Sie diese mit Tools wie [Wireshark](https://www.wireshark.org/). Wenden Sie sich bei Bedarf an den [Microsoft-Support](https://support.microsoft.com/). 


## <a name="next-steps"></a>Nächste Schritte

Die vollständige .NET-API-Referenz für Service Bus finden Sie unter [Azure .NET API Referenz](/dotnet/api/overview/azure/service-bus).

Weitere Informationen zu [Service Bus](https://azure.microsoft.com/services/service-bus/) finden Sie in den folgenden Artikeln:

* [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
* [Service Bus-Architektur](service-bus-architecture.md)

