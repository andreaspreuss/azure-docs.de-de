---
title: Veröffentlichen eines VM-Angebots im Azure Marketplace
description: Hier erfahren Sie Schritt für Schritt, wie Sie ein vorhandenes VM-Angebot im Azure Marketplace veröffentlichen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277343"
---
# <a name="publish-a-virtual-machine-offer"></a>Veröffentlichen eines VM-Angebots

 Nachdem Sie das Angebot im Portal definiert und die zugeordneten technischen Ressourcen erstellt haben, wird das Angebot im letzten Schritt zur Veröffentlichung übermittelt. Das folgende Diagramm zeigt die wichtigsten Schritte des Veröffentlichungsprozesses, die ausgeführt werden müssen, um ein Angebot live zu schalten:

![Schritte zur Veröffentlichung eines VM-Angebots](./media/publishvm_013.png)

Die folgende Tabelle enthält die Beschreibung dieser Schritte sowie die voraussichtliche maximale Dauer der einzelnen Schritte:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Veröffentlichungsschritt**           | **Time**    | **Beschreibung**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Überprüfen der Voraussetzungen         | 15 Min.   | Die Angebotsinformationen und -einstellungen werden überprüft.                        |
| Validierung der Testversion (optional) | 2 Stunden | Wenn Sie ausgewählt haben, dass die Testversion aktiviert wird, überprüft Microsoft die Konfiguration der Testversion sowie ihre Bereitstellung und Replikation in den ausgewählten Regionen. |
| Zertifizierung                  | 3 Tage | Das Angebot wird vom Azure-Zertifizierungsteam analysiert. Dabei wird das Angebot auf Viren, Schadsoftware, Sicherheitskonformität und Sicherheitsprobleme untersucht. Sollte ein Problem gefunden werden, erhalten Sie entsprechendes Feedback. |
| Bereitstellung                   | 4 Tage   | Das VM-Angebot wird in den Produktionssystemen des Marketplace repliziert.               |
| Paketerstellung und Registrierung von generierten Leads | \< 1 Stunde  | Die technischen Ressourcen des Angebots werden für die Verwendung durch die Kunden gepackt, und die Leadsysteme werden konfiguriert und eingerichtet. |
|  Einholen der Genehmigung des Herausgebers             |  -        | Letzte Überprüfung und Bestätigung durch den Herausgeber, bevor das Angebot live geschaltet wird. Sie können Ihr Angebot in den ausgewählten Abonnements (in den Schritten für die Angebotsinformationen) bereitstellen, um sicherzugehen, dass es alle Ihre Anforderungen erfüllt.  |
| Bereitstellung                   | 4 Tage | Das fertige VM-Angebot wird in den Produktionssystemen und Regionen des Marketplace repliziert. | 
| Live                           | 4 Tage | Das VM-Angebot wird veröffentlicht, in den erforderlichen Regionen repliziert und öffentlich verfügbar gemacht. |
|  |  |

Es kann bis zu 16 Tage dauern, bis dieser Prozess abgeschlossen ist.  Nach dem Ausführen dieser Veröffentlichungsschritte wird Ihr VM-Angebot im [Microsoft Azure Marketplace ](https://azuremarketplace.microsoft.com/marketplace/) aufgeführt. 

