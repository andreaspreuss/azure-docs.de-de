---
title: Azure IoT Hub-Bindungen für Azure Functions
description: Erfahren Sie, wie Sie auf Ereignisse reagieren, die in Azure Functions an einen IoT Hub-Ereignisdatenstrom gesendet werden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589605"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub-Trigger für Azure Functions

Dieser Artikel erläutert das Arbeiten mit Azure Functions-Bindungen für IoT Hub. Die IoT Hub-Unterstützung basiert auf der [Azure Event Hubs-Bindung](functions-bindings-event-hubs.md).

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Die folgenden Codebeispiele verwenden zwar die Event Hub-API, aber die Syntax gilt auch für IoT Hub-Funktionen.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Schreiben von Ereignissen in einen Ereignisdatenstrom (Ausgabebindungen)](./functions-bindings-event-iot-output.md)
