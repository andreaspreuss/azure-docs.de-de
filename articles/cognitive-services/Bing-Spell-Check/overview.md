---
title: Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?
titleSuffix: Azure Cognitive Services
description: Enthält eine Beschreibung der Bing-Rechtschreibprüfungs-API, bei der Machine Learning und die statistische maschinelle Übersetzung für eine kontextbezogene Rechtschreibprüfung verwendet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382998"
---
# <a name="what-is-the-bing-spell-check-api"></a>Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?

Mit der Bing-Rechtschreibprüfungs-API können Sie Grammatik und Rechtschreibung eines Texts überprüfen. Während für die meisten Rechtschreibprüfungen wörterbuchbasierte Regelsätze genutzt werden, werden bei der Bing-Rechtschreibprüfung Machine Learning und die statistische maschinelle Übersetzung verwendet, um präzise und kontextbezogene Korrekturen bereitzustellen. 

## <a name="features"></a>Features


|  |  |
|---------|---------|
|Mehrere Modi für die Rechtschreibprüfung     | Mit mehreren Modi für die Rechtschreibprüfung können Sie Korrekturen in Bezug auf die Grammatik bzw. Rechtschreibung erhalten. |
|Erkennung von Jargon und Umgangssprache     | Es werden häufig verwendete Ausdrücke und umgangssprachliche Begriffe erkannt, die im Text verwendet werden.         |
|Unterscheidung zwischen ähnlichen Wörtern     | Hierbei wird die richtige Verwendung von Wörtern ermittelt, die ähnlich klingen, aber eine unterschiedliche Bedeutung haben (z. B. im Englischen „see“ und „sea“).        |
|Unterstützung von Marke, Titel und Trendausdrücke     | Es werden neue Marken, Titel und andere Trendausdrücke erkannt, die neu entstehen. |

## <a name="workflow"></a>Workflow

Die Bing-Rechtschreibprüfungs-API kann problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON-Antworten analysieren kann. Auf den Dienst kann über die REST-API oder die Bing-Rechtschreibprüfungs-SDKs zugegriffen werden. 

1. Erstellen Sie ein [Cognitive Services-API-Konto](../cognitive-services-apis-create-account.md) mit Zugriff auf die Bing-Suche-APIs. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen. 
2. Senden Sie eine Anforderung an die Bing-Websuche-API.
3. Analysieren der JSON-Antwort

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie zunächst das [interaktive Demo](https://azure.microsoft.com/services/cognitive-services/spell-check/) der Such-API für die Bing-Rechtschreibprüfung aus, um zu erfahren, wie Sie viele verschiedene Texte schnell überprüfen können.

Wenn Sie zum Aufrufen der API bereit sind, erstellen Sie ein [Cognitive Services-API-Konto](../../cognitive-services/cognitive-services-apis-create-account.md). Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses [Konto erstellen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Sie können auch die Seite [Was ist die Bing-Websuche-API?](../bing-web-search/search-the-web.md) besuchen, um die anderen verfügbaren APIs zu untersuchen.