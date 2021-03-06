---
title: Anpassen der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Anpassungsoptionen für die Self-Service-Kennwortzurücksetzung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979456"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Anpassen der Azure AD-Funktionalität für die Self-Service-Kennwortzurücksetzung

IT-Experten, die die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) in Azure Active Directory (Azure AD) bereitstellen möchten, können die Bedienung an die Anforderungen ihrer Benutzer anpassen.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassen des Links „Wenden Sie sich an Ihren Administrator“

Benutzern mit Self-Service-Kennwortzurücksetzung wird im Portal für die Kennwortzurücksetzung ein Link „Wenden Sie sich an Ihren Administrator“ angezeigt. Wenn ein Benutzer auf diesen Link klickt, geschieht Folgendes:

* Link im Standardzustand:
   * Es wird eine E-Mail an Ihre Administratoren gesendet, in der diese gebeten werden, den Benutzer beim Ändern des Kennworts zu unterstützen. Nachstehend finden Sie eine [Beispiel-E-Mail](#sample-email).
* Angepasster Link:
   * Ihre Benutzer werden an eine vom Administrator angegebene Webseite oder E-Mail-Adresse weitergeleitet, über die sie Unterstützung erhalten.

> [!TIP]
> Bei einer Anpassung wird empfohlen, eine Einstellung zu verwenden, die den Benutzern für den Support bereits vertraut ist.

> [!WARNING]
> Wenn Sie diese Einstellung mit einer E-Mail-Adresse und einem Konto anpassen, für die bzw. das eine Kennwortzurücksetzung erforderlich ist, können die Benutzer möglicherweise keine Unterstützung anfordern.

### <a name="sample-email"></a>Beispiel-E-Mail

![Beispielanforderung zum Zurücksetzen der an den Administrator gesendeten E-Mail][Contact]

Die Kontakt-E-Mail wird in der folgenden Reihenfolge an die folgenden Empfänger gesendet:

1. Wenn die Rolle **Heldeskadministrator** oder **Kennwortadministrator** zugewiesen ist, werden die Administratoren mit dieser Rolle benachrichtigt.
1. Falls keine Helfdesk- oder Kennwortadministratoren zugewiesen sind, werden die Administratoren mit der Rolle **Benutzeradministrator** benachrichtigt.
1. Wenn keine der obigen Rollen zugewiesen ist, werden die **globalen Administratoren** benachrichtigt.

In jedem Fall werden bis zu 100 Empfänger benachrichtigt.

Weitere Informationen zu den verschiedenen Administratorrollen und wie sie zugewiesen werden können finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Deaktivieren von „Wenden Sie sich an Ihren Administrator“-E-Mails

Wenn Ihre Organisation nicht möchte, dass Administratoren über Anforderungen zur Kennwortzurücksetzung benachrichtigt werden, können Sie die folgende Konfiguration aktivieren:

* Aktivieren Sie die Self-Service-Kennwortzurücksetzung für alle Endbenutzer. Diese Option finden Sie unter **Zurücksetzen des Kennworts** > **Eigenschaften**. Wenn Sie nicht möchten, dass Benutzer ihre eigenen Kennwörter zurücksetzen, können Sie den Zugriff auf eine leere Gruppe beschränken. *Dies ist nicht zu empfehlen.*
* Passen Sie den Helpdesklink an, um eine Web-URL oder eine mailto:-Adresse bereitzustellen, die Benutzer verwenden können, um Unterstützung zu erhalten. Diese Option finden Sie unter **Zurücksetzen des Kennworts** > **Anpassung** > **Benutzerdefinierte Helpdesk-E-Mail oder -URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Anpassen der AD FS-Anmeldeseite für SSPR

AD FS-Administratoren (Active Directory Federation Services) können einen Link zu ihrer Anmeldeseite hinzufügen, indem sie gemäß der Anleitung vorgehen, die im Artikel [Beschreibung der Anmeldeseite hinzufügen](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) zu finden ist.

Um der AD FS-Anmeldeseite einen Link hinzuzufügen, verwenden Sie den folgenden Befehl auf dem AD FS-Server. Benutzer können diese Seite verwenden, um den SSPR-Workflow einzugeben.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Anpassen des Aussehens und Verhaltens der Anmeldeseite und des Zugriffsbereichs

Sie können die Anmeldeseite anpassen. Sie können ein Logo hinzufügen, das zusammen mit dem Bild angezeigt wird, das zu Ihrem Unternehmensbranding gehört.

Die von Ihnen ausgewählten Grafiken werden in den folgenden Situationen angezeigt:

* Nachdem ein Benutzer seinen Benutzernamen eingegeben hat
* Wenn der Benutzer auf die angepasste URL zugreift:
   * Durch Übergeben des `whr`-Parameters an die Seite zum Zurücksetzen des Kennworts, z. B. `https://login.microsoftonline.com/?whr=contoso.com`
   * Durch Übergeben des `username`-Parameters an die Seite zum Zurücksetzen des Kennworts, z. B. `https://login.microsoftonline.com/?username=admin@contoso.com`

Im Artikel [Schnellstart: Hinzufügen eines Unternehmensbrandings zur Anmeldeseite in Azure AD](../fundamentals/customize-branding.md) finden Sie ausführliche Informationen zum Konfigurieren von Unternehmensbranding.

### <a name="directory-name"></a>Verzeichnisname

Sie können das Attribut für den Verzeichnisnamen unter **Azure Active Directory** > **Eigenschaften** ändern. Sie können einen Anzeigenamen für Organisation anzeigen, der Im Portal und in automatisierter Kommunikation zu sehen ist. Diese Option wird insbesondere in automatisierten E-Mails in den folgenden Formen angezeigt:

* Der Anzeigename in einer E-Mail, beispielsweise „Microsoft im Namen von CONTOSO – Demo“
* Die Betreffzeile einer E-Mail, beispielsweise „E-Mail-Prüfnummer für CONTOSO-Demokonto“

## <a name="next-steps"></a>Nächste Schritte

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Beispiel-E-Mail zum Anfordern von Administratorunterstützung bei der Kennwortzurücksetzung"
