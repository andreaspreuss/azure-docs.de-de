---
title: 'Häufig gestellte Fragen zu Azure Security Center: Datensammlung und Agents'
description: Häufig gestellte Fragen zu Datensammlung, Agents und Arbeitsbereichen für Azure Security Center – einem Produkt, das Ihnen dabei hilft, Bedrohungen zu verhindern und zu erkennen und auf Bedrohungen zu reagieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225314"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Häufig gestellte Fragen: Datensammlung, Agents und Arbeitsbereiche

Security Center erfasst Daten von Ihren virtuellen Azure-Computern (virtual machines, VMs), VM-Skalierungsgruppen, IaaS-Containern und Azure-fremden Computern (auch lokal), um sie auf Sicherheitslücken und Bedrohungen zu überwachen. Die Daten werden mithilfe von Microsoft Monitoring Agent gesammelt. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer und kopiert die Daten zur Analyse in Ihren Arbeitsbereich.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Werden mir die Azure Monitor-Protokolle auf den von Security Center erstellten Arbeitsbereiche in Rechnung gestellt?

Nein. Von Security Center erstellte Arbeitsbereiche sind zwar für die knotenbasierte Abrechnung von Azure Monitor-Protokollen konfiguriert, es fallen jedoch keine Kosten für Azure Monitor-Protokolle an. Die Abrechnung von Security Center basiert immer auf Ihrer Security Center-Sicherheitsrichtlinie und den installierten Lösungen in einem Arbeitsbereich:

- **Free-Tarif**: Security Center aktiviert die Lösung „SecurityCenterFree“ im Standardarbeitsbereich. Für den Free-Tarif entstehen keine Kosten.

- **Standard-Tarif**: Security Center aktiviert die Lösung „Security“ im Standardarbeitsbereich.

Weitere Informationen zu Preisen finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Der Protokollanalyse-Tarif von Arbeitsbereichen, die von Security Center erstellt wurden, hat keine Auswirkungen auf die Security Center-Abrechnung.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Wann kommt eine VM für die automatische Bereitstellung der Microsoft Monitoring Agent-Installation infrage?

Windows- oder Linux-IaaS-VMs kommen unter folgenden Voraussetzungen infrage:

- Die Microsoft Monitoring Agent-Erweiterung ist derzeit nicht auf der VM installiert.
- Die VM wird ausgeführt.
- Der [Azure-VM-Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) für Windows oder Linux ist installiert.
- Die VM wird nicht als Appliance wie z.B. Webanwendungsfirewall oder Firewall der nächsten Generation verwendet.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kann ich die von Security Center erstellten Standardarbeitsbereiche löschen?

**Das Löschen des Standardarbeitsbereichs wird nicht empfohlen.** Security Center verwendet die Standardarbeitsbereiche, um Sicherheitsdaten Ihrer virtuellen Computer zu speichern. Wenn Sie einen Arbeitsbereich löschen, kann Security Center diese Daten nicht erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar.

Entfernen Sie zur Wiederherstellung den Microsoft Monitoring Agent auf den virtuellen Computern, die mit dem gelöschten Arbeitsbereich verbunden sind. Security Center installiert den Agent erneut und erstellt neue Standardarbeitsbereiche.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?

Sie können einen vorhandenen Log Analytics-Arbeitsbereich zum Speichern der von Security Center gesammelten Daten auswählen. Wenn Sie Ihren vorhandenen Log Analytics-Arbeitsbereich verwenden möchten, gilt Folgendes:

- Der Arbeitsbereich muss Ihrem ausgewählten Azure-Abonnement zugeordnet sein.
- Sie müssen mindestens über Leseberechtigungen verfügen, um auf den Arbeitsbereich zugreifen zu können.

So wählen Sie einen vorhandenen Log Analytics-Arbeitsbereich aus:

1. Wählen Sie unter **Sicherheitsrichtlinie – Datensammlung** die Option **Use another workspace** (Anderen Arbeitsbereich verwenden) aus.

    ![Verwenden eines anderen Arbeitsbereichs][4]

1. Wählen Sie im Pulldownmenü einen Arbeitsbereich zum Speichern der gesammelten Daten aus.

    > [!NOTE]
    > Das Pulldownmenü enthält nur Arbeitsbereiche, auf die Sie Zugriff haben und die sich in Ihrem Azure-Abonnement befinden.

1. Wählen Sie **Speichern** aus. Sie werden gefragt, ob Sie überwachte virtuelle Computer neu konfigurieren möchten.

    - Klicken Sie auf **Nein**, wenn die neuen Arbeitsbereichseinstellungen **nur auf neue virtuelle Computer angewendet werden sollen**. Die neuen Arbeitsbereichseinstellungen gelten nur für neue Agent-Installationen (neu ermittelte virtuelle Computer, auf denen Microsoft Monitoring Agent nicht installiert ist).
    - Klicken Sie auf **Ja**, wenn die neuen Arbeitsbereichseinstellungen **auf alle virtuellen Computer angewendet werden sollen**. Darüber hinaus wird jeder virtuelle Computer, der mit einem von Security Center erstellten Arbeitsbereich verbunden ist, nun mit dem neuen Zielarbeitsbereich verbunden.

    > [!NOTE]
    > Wenn Sie **Ja** auswählen, löschen Sie die von Security Center erstellten Arbeitsbereiche erst, wenn alle virtuellen Computer mit dem neuen Zielarbeitsbereich verbunden sind. Dieser Vorgang ist nicht erfolgreich, wenn ein Arbeitsbereich zu früh gelöscht wird.

    - Wenn Sie den Vorgang abbrechen möchten, klicken Sie auf **Abbrechen**.

## <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Was passiert, wenn der Microsoft Monitoring Agent bereits als Erweiterung auf dem virtuellen Computer installiert wurde?<a name="mmaextensioninstalled"></a>

Wenn der Monitoring Agent als Erweiterung installiert ist, erlaubt die Konfiguration der Erweiterung auch Berichte an nur einen einzelnen Arbeitsbereich. Bereits vorhandene Verbindungen mit Benutzerarbeitsbereichen werden von Security Center nicht überschrieben. Security Center speichert Sicherheitsdaten eines virtuellen Computers in einem bereits verbundenen Arbeitsbereich, sofern darin die Lösung „Security“ oder „SecurityCenterFree“ installiert wurde. Security Center kann die Version der Erweiterung während dieses Vorgangs auf die neueste Version aktualisieren.

Weitere Informationen finden Sie unter [Automatische Bereitstellung bei einer bereits vorhandenen Agent-Installation](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-microsoft-monitoring-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Was, wenn auf dem Computer ein Microsoft Monitoring Agent direkt installiert ist, aber nicht als Erweiterung (Direct Agent)?<a name="directagentinstalled"></a>

Wenn Microsoft Monitoring Agent direkt auf dem virtuellen Computer (also nicht als Azure-Erweiterung) installiert ist, installiert Security Center die Microsoft Monitoring Agent-Erweiterung und aktualisiert diesen ggf. auf die neueste Version.

Der installierte Agent erstellt seine Berichte weiterhin für die bereits konfigurierten Arbeitsbereiche und darüber hinaus für den in Security Center konfigurierten Arbeitsbereich (Multi-Homing wird auf Windows-Computern unterstützt).

Wenn der konfigurierte Arbeitsbereich ein Benutzerarbeitsbereich ist (nicht der Standardarbeitsbereich von Security Center), müssen Sie darin die Lösung „Security“ oder „SecurityCenterFree“ installieren, damit Security Center mit der Verarbeitung von Ereignissen von VMs und Computern, die ihre Berichte in diesem Arbeitsbereich erstellen, beginnen kann.

Auf Linux-Computern wird das Multi-Homing von Agents noch nicht unterstützt, weshalb, wenn eine bestehende Agent-Installation erkannt wird, keine automatische Bereitstellung erfolgt und die Konfiguration des Computers nicht geändert wird.

Wenn für vorhandene Computer in Abonnements, die vor dem 17. März 2019 in Security Center integriert wurden, ein vorhandener Agent erkannt wurde, wird die Microsoft Monitoring Agent-Erweiterung nicht installiert, und der Computer ist nicht betroffen. Für diese Computer wird die Empfehlung „Monitoring Agent-Integritätsprobleme auf Ihren Computern beheben“ angezeigt, damit Sie die Installationsprobleme des Agents auf diesen Computern beheben können.

Weitere Informationen finden Sie im nächsten Abschnitt: [Was geschieht, wenn ein System Center Operations Manager- oder OMS-Direkt-Agent bereits auf meiner VM installiert ist?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Was, wenn auf meinem virtuellen Computer bereits ein System Center Operations Manager-Agent installiert ist?<a name="scomomsinstalled"></a>

Security Center installiert die Microsoft Monitoring Agent-Erweiterung zusätzlich zum vorhandenen System Center Operations Manager-Agent. Der vorhandene Agent sendet weiterhin normal Berichte an den System Center Operations Manager-Server. Beachten Sie, dass die vom Operations Manager-Agent und dem Microsoft Monitoring Agent gemeinsam genutzten Laufzeitbibliotheken bei diesem Vorgang auf die neueste Version aktualisiert werden. Hinweis: Falls Version 2012 des Operations Manager-Agents installiert ist, aktivieren Sie die automatische Bereitstellung nicht (Verwaltungsfunktionen können verloren gehen, wenn der Operations Manager-Server auch Version 2012 aufweist).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Was passiert, wenn ich diese Erweiterungen entferne?

Wenn Sie die Microsoft Monitoring-Erweiterung entfernen, kann Security Center keine Sicherheitsdaten des virtuellen Computers erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar. Innerhalb von 24 Stunden erkennt Security Center, dass die Erweiterung auf dem virtuellen Computer fehlt, und installiert sie erneut.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Wie verhindere ich die automatische Agent-Installation und die Arbeitsbereicherstellung?

Sie können die automatische Bereitstellung für Ihre Abonnements in der Sicherheitsrichtlinie deaktivieren, dies wird jedoch nicht empfohlen. Die Deaktivierung der automatischen Bereitstellung schränkt die Empfehlungen und Warnungen von Security Center ein. So deaktivieren Sie die automatische Bereitstellung:

1. Wenn Ihr Abonnement für den Standard-Tarif konfiguriert ist, öffnen Sie die Sicherheitsrichtlinie für dieses Abonnement, und wählen Sie den Tarif **Free** aus.

   ![Tarif][1]

1. Deaktivieren Sie als Nächstes die automatische Bereitstellung, indem Sie auf der Seite **Sicherheitsrichtlinie – Datensammlung** auf **Aus** klicken.
   ![Datensammlung][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Sollte ich die automatische Agent-Installation und Arbeitsbereicherstellung deaktivieren?

> [!NOTE]
> Lesen Sie unbedingt die Abschnitte [Welche Auswirkungen hat die Deaktivierung der automatischen Bereitstellung?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) und [Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning), falls Sie entscheiden, die automatische Bereitstellung zu deaktivieren.

Möglicherweise möchten Sie die automatische Bereitstellung deaktivieren, wenn Folgendes auf Sie zutrifft:

- Die automatische Agent-Installation durch Security Center gilt für das gesamte Abonnement. Sie können die automatische Installation nicht auf eine Teilmenge der VMs anwenden. Wenn wichtige VMs nicht mit Microsoft Monitoring Agent installiert werden können, sollten Sie die automatische Bereitstellung deaktivieren.
- Die Installation der Microsoft Monitoring Agent-Erweiterung (MMA) aktualisiert die Agent-Version. Dies gilt für einen direkten Agent und einen System Center Operations Manager-Agent (im letztgenannten teilen sich Operations Manager und MMA gemeinsame Laufzeitbibliotheken, die im Prozess aktualisiert werden). Wenn der installierte Operations Manager-Agent Version 2012 aufweist und aktualisiert wird, können Verwaltungsfunktionen verloren gehen, wenn die Version des Operations Manager-Servers ebenfalls 2012 ist. Erwägen Sie die Deaktivierung der automatischen Bereitstellung, wenn Sie Version 2012 des Operations Manager-Agents installiert haben.
- Wenn Sie über einen benutzerdefinierten Arbeitsbereich außerhalb des Abonnements (einen zentralen Arbeitsbereich) verfügen, sollten Sie die automatische Bereitstellung deaktivieren. Sie können die Microsoft Monitoring Agent-Erweiterung manuell installieren und ihre Verbindung mit Ihrem Arbeitsbereich herstellen, ohne dass Security Center die Verbindung überschreibt.
- Wenn Sie das Erstellen mehrerer Arbeitsbereiche pro Abonnement vermeiden möchten und innerhalb des Abonnements über Ihren eigenen benutzerdefinierten Arbeitsbereich verfügen, haben Sie zwei Optionen:

   1. Sie können die automatische Bereitstellung deaktivieren. Legen Sie nach der Migration die standardmäßigen Arbeitsbereichseinstellungen wie in [Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?](#how-can-i-use-my-existing-log-analytics-workspace) beschrieben fest.

   1. Alternativ können Sie zulassen, dass die Migration abgeschlossen, der Microsoft Monitoring Agent auf den VMs installiert und die Verbindung der VMs mit dem erstellten Arbeitsbereich hergestellt wird. Wählen Sie dann Ihren eigenen benutzerdefinierten Arbeitsbereich durch Einstellung der standardmäßigen Arbeitsbereichseinstellung mit Aktivierung zur Neukonfiguration der bereits installierten Agents. Weitere Informationen finden Sie unter [Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?](#how-can-i-use-my-existing-log-analytics-workspace).


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Welche Auswirkungen hat die Deaktivierung der automatischen Bereitstellung?

Wenn die Migration abgeschlossen ist, kann Security Center keine Sicherheitsdaten der VM erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar. Wenn Sie die Deaktivierung durchführen, installieren Sie den Microsoft Monitoring Agent manuell. Siehe [Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?

Damit Security Center Sicherheitsdaten auf Ihren VMs erfassen und Empfehlungen und Warnungen bereitstellen kann, installieren Sie die Erweiterung Microsoft Monitoring Agent manuell. Eine Anleitung zur Installation finden Sie unter [Agent-Installation für Windows-VM](../virtual-machines/extensions/oms-windows.md) oder [Agent-Installation für Linux-VM](../virtual-machines/extensions/oms-linux.md).

Sie können eine Verbindung des Agent zu jedem vorhandenen benutzerdefinierten Arbeitsbereich oder in Security Center erstellten Arbeitsbereich herstellen. Wenn in einem benutzerdefinierten Arbeitsbereich die Lösungen „Security“ oder „SecurityCenterFree“ nicht aktiviert sind, müssen Sie eine Lösung anwenden. Wählen Sie zum Anwenden den benutzerdefinierten Arbeitsbereich oder das Abonnement aus, und wenden Sie einen Tarif über die Seite **Sicherheitsrichtlinie – Tarif** an.

   ![Tarif][1]

Security Center aktiviert basierend auf dem ausgewählten Tarif die richtige Lösung im Arbeitsbereich.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Wie entferne ich durch Security Center installierte OMS-Erweiterungen?<a name="remove-oms"></a>

Der Microsoft Monitoring Agent kann manuell entfernt werden. Dies wird jedoch nicht empfohlen, da es die Empfehlungen und Warnungen von Security Center einschränkt.

> [!NOTE]
> Bei aktivierter Datensammlung wird der Agent nach dem Entfernen erneut installiert.  Daher müssen Sie vor dem manuellen Entfernen des Agents die Datensammlung deaktivieren. Die Anleitung zum Deaktivieren der Datensammlung finden Sie unter „Wie verhindere ich die automatische Agent-Installation und die Arbeitsbereicherstellung?“

So entfernen Sie den Agent manuell:

1.  Öffnen Sie im Portal **Log Analytics**.

1.  Wählen Sie auf der Seite „Log Analytics“ einen Arbeitsbereich aus:

1.  Wählen Sie die virtuellen Computer aus, die Sie nicht überwachen möchten, und wählen Sie anschließend **Trennen** aus.

   ![Entfernen des Agents][3]

> [!NOTE]
> Falls ein virtueller Linux-Computer bereits über einen OMS-Agent verfügt, bei dem es sich nicht um eine Erweiterung handelt, wird der Agent beim Entfernen der Erweiterung ebenfalls entfernt und muss neu installiert werden.


## <a name="how-do-i-disable-data-collection"></a>Wie deaktiviere ich Datensammlung?

Die automatische Bereitstellung ist standardmäßig deaktiviert. Sie können die automatische Bereitstellung in Ressourcen jederzeit deaktivieren, indem Sie diese Einstellung in der Sicherheitsrichtlinie deaktivieren. Die automatische Bereitstellung wird dringend empfohlen, um Sicherheitswarnungen und -empfehlungen zu Systemupdates, zu Sicherheitsrisiken für das Betriebssystem und zu Endpoint Protection zu erhalten.

Um die Datensammlung zu deaktivieren, [melden Sie sich beim Azure-Portal an](https://portal.azure.com), und wählen Sie nacheinander **Durchsuchen**, **Security Center** und **Richtlinie auswählen** aus. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung deaktivieren möchten. Wenn Sie ein Abonnement auswählen, wird **Sicherheitsrichtlinie – Datensammlung** geöffnet. Wählen Sie unter **Automatische Bereitstellung** die Option **Aus** aus.


## <a name="how-do-i-enable-data-collection"></a>Wie aktiviere ich die Datensammlung?

Sie können die Datensammlung für Ihr Azure-Abonnement in der Sicherheitsrichtlinie aktivieren. Zum Aktivieren der Datensammlung [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und wählen Sie nacheinander **Durchsuchen**, **Security Center** und **Sicherheitsrichtlinie** aus. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung aktivieren möchten. Wenn Sie ein Abonnement auswählen, wird **Sicherheitsrichtlinie – Datensammlung** geöffnet. Wählen Sie unter **Automatische Bereitstellung** die Option **Ein** aus.


## <a name="what-happens-when-data-collection-is-enabled"></a>Was passiert, wenn die Datensammlung aktiviert wird?

Bei aktivierter automatischer Bereitstellung wird Microsoft Monitoring Agent von Security Center auf allen unterstützten virtuellen Azure-Computern sowie auf allen neu erstellten virtuellen Computern bereitgestellt. Die automatische Bereitstellung wird empfohlen, die manuelle Agent-Installation ist jedoch ebenfalls verfügbar. [Erfahren Sie, wie Sie die Microsoft Monitoring Agent-Erweiterung installieren.](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

Der Agent aktiviert das Prozesserstellungsereignis 4688 und das Feld *CommandLine* im Ereignis 4688. Auf der VM erstellte neue Prozesse werden von EventLog aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht. Weitere Informationen zu den für jeden neuen Prozess aufgezeichneten Details finden Sie unter [Description Fields in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields) (Beschreibungsfelder in 4688). Außerdem sammelt der Agent die auf dem virtuellen Computer erstellten 4688-Ereignisse und speichert sie in der Suche.

Der Agent aktiviert auch die Datensammlung für [Adaptive Anwendungssteuerungen](security-center-adaptive-application.md), und Security Center konfiguriert eine lokale AppLocker-Richtlinie im Überwachungsmodus, um alle Anwendungen zuzulassen. Diese Richtlinie bewirkt, dass AppLocker Ereignisse generiert, die dann von Security Center gesammelt und genutzt werden können. Es ist wichtig zu beachten, dass diese Richtlinie nicht auf Computern konfiguriert wird, auf denen bereits eine AppLocker-Richtlinie konfiguriert ist. 

Wenn Security Center verdächtige Aktivitäten auf dem virtuellen Computer erkennt, wird der Kunde per E-Mail benachrichtigt, sofern [Sicherheitskontaktinformationen](security-center-provide-security-contact-details.md) bereitgestellt wurden. Außerdem wird eine Warnung auf dem Sicherheitswarnungs-Dashboard von Security Center angezeigt.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Wird Security Center mit einem OMS-Gateway funktionieren?

Ja. Azure Security Center nutzt Azure Monitor zum Sammeln von Daten von Azure-VMs und -Servern mithilfe des Microsoft Monitoring Agent.
Um die Daten zu sammeln, muss jede VM und jeder Server über HTTPS mit dem Internet verbunden sein. Die Verbindung kann direkt, über einen Proxy oder über das [OMS-Gateway](../azure-monitor/platform/gateway.md) hergestellt werden.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Wirkt sich der Monitoring Agent auf die Leistung der Server aus?

Der Agent beansprucht eine äußerst geringe Menge von Systemressourcen und sollten nur eine geringe Auswirkung auf die Leistung haben. Weitere Informationen zu Auswirkungen auf die Leistung, zum Agent und zur Erweiterung finden Sie unter [Planungs- und Betriebshandbuch](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Wo werden meine Daten gespeichert?

Die von diesem Agent gesammelten Daten werden entweder in einem vorhandenen Log Analytics-Arbeitsbereich, der mit Ihrem Abonnement verknüpft ist, oder in einem neuen Arbeitsbereich gespeichert. Weitere Informationen finden Sie unter [Datensicherheit](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png