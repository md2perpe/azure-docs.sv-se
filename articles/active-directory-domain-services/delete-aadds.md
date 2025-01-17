---
title: Inaktivera Azure Active Directory Domain Services | Microsoft Docs
description: Inaktivera Azure Active Directory Domain Services med Azure portal
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 2f9b7f5342753c27bd8159056c8f162a5a4b78d3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473549"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Inaktivera Azure Active Directory Domain Services med Azure portal
Den här artikeln visar hur du använder Azure-portalen för att inaktivera Azure Active Directory (AD)-domäntjänster för Azure AD-katalogen.

> [!WARNING]
> **Borttagningen är permanent och kan inte ångras.**
> Gå vidare med försiktighet! När du tar bort den hanterade domänen:
>   * Domänkontrollanter för den hanterade domänen är avetableras och tas bort från det virtuella nätverket.
>   * Data i den hanterade domänen tas bort permanent. Detta inkluderar anpassade organisationsenheter, grupprincipobjekt, anpassade DNS-poster, tjänstens huvudnamn, GMSAs etc. som du har skapat i den hanterade domänen.
>   * Datorer som är anslutna till den hanterade domänen förlora sina förtroenderelation med domänen och behöver inte frånkopplas från domänen.
>   * Du kan inte logga in på dessa datorer med hjälp av företagets AD-autentiseringsuppgifter. Använd autentiseringsuppgifter för lokal administratör för datorn, i stället.
> Tar bort den hanterade domänen tar inte bort Azure AD-katalogen eller annars negativt påverka katalogen.

Utför följande steg för att ta bort din hanterade domän i Azure AD Domain Services:
1. Navigera till den [Azure AD Domain Services-tillägget](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure-portalen.
2. Klicka på namnet på den hanterade domänen.

    ![Välj domän att ta bort](./media/getting-started/domain-services-delete-select-domain.png)

3. På den **översikt** klickar du på den **ta bort** knappen.

    ![Ta bort domänen](./media/getting-started/domain-services-delete-domain.png)

4. Bekräfta borttagningen genom att ange DNS-domännamnet för den hanterade domänen. Klicka på den **ta bort** knappen när du är klar.

    ![Ta bort domänen bekräftelse](./media/getting-started/domain-services-delete-domain-confirm.png)

Den hanterade domänen tas bort på 15-20 minuter.

Överväg att [delning feedback](contact-us.md) som hjälper oss att förstå vilka funktioner vill hjälpa du har valt Azure AD Domain Services i framtiden. Denna feedback hjälper oss att utveckla tjänsten så att den bättre passar dina distributionsbehov och användningsfall.
