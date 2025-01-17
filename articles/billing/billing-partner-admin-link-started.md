---
title: Länka ett Azure-konto till ett partner-ID | Microsoft Docs
description: Spåra arbete med Azure-kunder genom att länka ett partner-ID till det användarkonto som används för att hantera kundens resurser.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 97866a1f1299c028cdc8f86245308ae4a8b5db88
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502675"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Länka ett partner-ID till dina Azure-konton

Microsoft-partner tillhandahålla tjänster som hjälper kunder få verksamhets- och verksamhetskritiska mål med hjälp av Microsoft-produkter. När vägnar kunden hantering, behöver konfiguration och support av Azure-tjänster, partneranvändare åtkomst till kundens miljö. Med länken för Partner-administratör kan associera partner sina partner network-ID med de autentiseringsuppgifter som används för serviceleverans.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Hämta från kunden

Innan du länka ditt partner-ID, måste kunden får du åtkomst till sina Azure-resurser med hjälp av något av följande alternativ:

- **Gästanvändaren**: Kunden kan lägga till dig som en gästanvändare och tilldela rollbaserad åtkomst roller för åtkomstkontroll (RBAC). Mer information finns i [lägga till gästanvändare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-konto**: Kunden kan skapa ett användarkonto för dig i sin egen directory och tilldela en RBAC-roll.

- **Tjänstens huvudnamn**: Kunden kan lägga till en app eller ditt skript från din organisation i sin katalog och tilldela en RBAC-roll. Identiteten för den app eller ditt skript kallas ett huvudnamn för tjänsten.

## <a name="link-to-a-partner-id"></a>Länka till ett partner-ID

När du har åtkomst till kundens resurser, använda Azure portal, PowerShell eller Azure CLI för att länka ditt Microsoft Partner Network-ID (MPN-ID) till dina användar-ID eller tjänstens huvudnamn. Länka partner-ID i varje kundklient.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Använd Azure-portalen för att länka till en ny partner-ID

1. Gå till [länk till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) i Azure-portalen.

2. Logga in på Azure Portal.

3. Ange Microsoft partner-ID. Partner-ID är den [Microsoft Partner Network](https://partner.microsoft.com/) ID för din organisation.

   ![Skärmbild som visar länk till en partner-ID](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Växla katalogen för att länka ett partner-ID för en annan kund. Under **växla katalog**, Välj din katalog.

   ![Skärmbild som visar växla katalog](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Använd PowerShell för att länka till en ny partner-ID

1. Installera den [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell-modulen.

2. Logga in på kundens klient med användarkonton eller tjänstens huvudnamn. Mer information finns i [logga in med PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Länka till ny partner-ID. Partner-ID är den [Microsoft Partner Network](https://partner.microsoft.com/) ID för din organisation.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Hämta länkade partner-ID
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkade partner-ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Ta bort länkade partner-ID
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Använda Azure CLI för att länka till en ny partner-ID
1. Installera Azure CLI-tillägget.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Logga in på kundens klient med användarkonton eller tjänstens huvudnamn. Mer information finns i [logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Länka till ny partner-ID. Partner-ID är den [Microsoft Partner Network](https://partner.microsoft.com/) ID för din organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Hämta länkade partner-ID
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkade partner-ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Ta bort länkade partner-ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Nästa steg

Gå med i diskussionen den [Microsoft Partner Community](https://aka.ms/PALdiscussion) ta emot uppdateringar eller skicka feedback.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem kan länka partner-ID?**

Alla användare från partnerorganisationen som hanterar en kunds Azure-resurser kan länka partner-ID till kontot.

**Ett partner-ID som kan ändras när den är länkad?**

Ja. En länkad partner-ID kan ändras, har lagts till eller tas bort.

**Vad händer om en användare har ett konto i fler än en kundklient?**

Länken mellan partner-ID och konton som görs för varje kundklient. Länka partner-ID i varje kundklient.

**Kan andra partners eller kunder redigera eller ta bort länken till partner-ID?**

Länken är associerade på nivån för kontot. Endast kan du redigera eller ta bort länken till partner-ID. Kunden och andra partners kan inte ändra länken till partner-ID.


**Vilka MPN-ID ska jag använda om mitt företag har flera?**

Plats-partnerkonton och associerade MPN-ID: N ska användas för att länka partner-ID.  Läs mer om [partnerkonton](https://docs.microsoft.com/partner-center/account-structure)

**Var hittar jag har påverkat intäktsrapportering för länkade partner-ID?**

Molnet produkten Prestandarapportering är tillgängliga för partners i Partnercenter på [min instrumentpanel](https://partner.microsoft.com/membership/reports/myinsights). Du måste välja länken för Partner-administratör som typ av partner-kopplingen.

**Varför kan jag inte se min kund i rapporterna?**

Du kan inte se kunden i rapporterna på grund av följande orsaker

1. Det länkade användarkontot inte har [Role-Based Access](https://docs.microsoft.com/azure/role-based-access-control/overview) på kundens Azure-prenumeration eller resurs.

2. Azure-prenumerationen där användaren har [Role-Based Access](https://docs.microsoft.com/azure/role-based-access-control/overview) åtkomst har inte någon användning.

**Länka partner-ID som fungerar med Azure Stack?**

Ja, du kan länka ditt partner-ID för Azure Stack.
