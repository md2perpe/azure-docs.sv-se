---
title: Övervaka nätverkskommunikation – självstudie – Azure Portal | Microsoft Docs
description: Lär dig hur du övervakar nätverkskommunikationen mellan två virtuella datorer med funktionen för anslutningsövervakning i Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 5cac4a46fb35ef955903018028abbe7588c94dc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66233895"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Självstudier: Övervaka nätverkskommunikationen mellan två virtuella datorer i Azure-portalen

Det kan vara mycket viktigt att kommunikationen mellan en virtuell dator (VM) och en slutpunkt, som en annan virtuell dator, fungerar ordentligt. Ibland görs konfigurationsändringar som kan bryta kommunikationen. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer
> * Övervaka kommunikationen mellan virtuella datorer med funktionen för anslutningsövervakning i Network Watcher
> * Generera aviseringar om mått från anslutningsövervakare
> * Diagnostisera ett kommunikationsproblem mellan två virtuella datorer och lär dig hur du kan lösa det

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in till Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-vms"></a>Skapa VM:ar

Skapa två virtuella datorer.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute** och sedan ett operativsystem. I den här självstudien används **Windows Server 2016 Datacenter**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm1|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och ange **myResourceGroup**.|
    |Location| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **Inställningar** väljer du **Tillägg**. Välj **Lägg till tillägg** och välj **Network Watcher Agent for Windows**, så som visas i följande bild:

    ![Network Watcher-agenttillägget](./media/connection-monitor/nw-agent-extension.png)

6. Under **Network Watcher Agent for Windows** väljer du **Skapa**, under **Installera tillägg** väljer du **OK** och under **Tillägg** väljer du **OK**.
7. Behåll standardinställningarna för återstående **inställningar** och välj **OK**.
8. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Utför stegen i [Skapa den första virtuella datorn](#create-the-first-vm) igen med följande ändringar:

|Steg|Inställning|Värde|
|---|---|---|
| 1 | Välj en version av **Ubuntu Server** |                                                                         |
| 3 | Namn                                  | myVm2                                                                   |
| 3 | Autentiseringstyp                   | Klistra in den offentliga SSH-nyckeln eller välj **Lösenord** och ange ett lösenord. |
| 3 | Resursgrupp                        | Välj **Använd befintlig** och sedan **myResourceGroup**.                 |
| 6 | Tillägg                            | **Network Watcher-Agent för Linux**                                             |

Det tar några minuter att distribuera den virtuella datorn. Vänta tills distributionen av den virtuella datorn är klar innan du fortsätter med nästa steg.

## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

Skapa en anslutningsövervakare för övervakning av kommunikationen via TCP-port 22 från *myVm1* till *myVm2*.

1. Välj **Alla tjänster** till vänster i portalen.
2. Börja skriva *network watcher* i **filterrutan**. När **Network Watcher** visas i sökresultatet väljer du posten.
3. Under **ÖVERVAKNING** väljer du **Anslutningsövervakare**.
4. Välj **+ Lägg till**.
5. Ange eller välj information för anslutningen du vill övervaka och välj sedan **Lägg till**. I exemplet som visas i följande bild övervakas anslutningen från den virtuella datorn *myVm1* till den virtuella datorn *myVm2* VM via port 22:

    | Inställning                  | Värde               |
    | ---------                | ---------           |
    | Namn                     | myVm1-myVm2(22)     |
    | Source                   |                     |
    | Virtuell dator          | myVm1               |
    | Mål              |                     |
    | Välj en virtuell dator |                     |
    | Virtuell dator          | myVm2               |
    | Port                     | 22                  |

    ![Lägg till anslutningsövervakare](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Visa en anslutningsövervakare

1. Utför steg 1–3 i [Skapa en anslutningsövervakare](#create-a-connection-monitor) om du vill visa en anslutningsövervakare. Du kan se en lista med befintliga anslutningsövervakare, så som visas i följande bild:

    ![Anslutningsövervakare](./media/connection-monitor/connection-monitors.png)

2. Välj övervakaren med namnet **myVm1-myVm2(22)** om du vill visa information om den, så som visas i föregående bild:

    ![Information om övervakare](./media/connection-monitor/vm-monitor.png)

    Notera följande information:

    | Objekt                     | Värde                      | Information                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Kan nås                  | Visar om slutpunkten kan nås eller inte.|
    | GENOMSN. TIDSFÖRDRÖJNING          | Visar anslutningens tidsfördröjning i millisekunder. Anslutningsövervakaren söker av anslutningen var 60:e sekund så att du kan se svarstiderna över tid.                                         |
    | Hopp                     | Anslutningsövervakaren visar hoppen mellan de två slutpunkterna. I det här exemplet ligger anslutningen mellan de två virtuella datorerna i samma virtuella nätverk, så det sker bara ett hopp till IP-adressen 10.0.0.5. Om något befintligt system eller en egen väg dirigerar trafiken mellan de två virtuella datorerna via en VPN-gateway eller en virtuell nätverksenhet visas fler hopp.                                                                                                                         |
    | STATUS                   | En grön bockmarkering vid respektive slutpunkt anger att tillståndet är felfritt.    ||

## <a name="generate-alerts"></a>Generera aviseringar

Aviseringar skapas av aviseringsregler i Azure Monitor och kan automatiskt köra sparade frågor eller anpassade loggsökningar med jämna mellanrum. En genererad avisering kan automatiskt köra en eller flera åtgärder, till exempel att meddela någon eller starta en annan process. När du anger en aviseringsregel avgör den resurs som du väljer som mål listan över tillgängliga mått som du kan använda för att generera aviseringar.

1. I Azure-portalen väljer du **övervakningstjänsten** och sedan **Aviseringar** > **Ny aviseringsregel**.
2. Klicka på **Välj mål** och välj sedan de resurser som du vill ha som mål. Välj **Prenumeration** och ange **Resurstyp** till att filtrera ned till den anslutningsövervakare som du vill använda.

    ![aviseringsskärm med mål som valts](./media/connection-monitor/set-alert-rule.png)
1. När du har valt en resurs som ska vara mål väljer du **Lägg till villkor**. Network Watcher har [mått som du kan skapa aviseringar om](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#metrics-and-dimensions-supported). Ange **Tillgängliga signaler** till måtten ProbesFailedPercent och AverageRoundtripMs:

    ![aviseringssida med signaler som valts](./media/connection-monitor/set-alert-signals.png)
1. Fyll i information om aviseringar såsom namn på aviseringsregel, beskrivning och allvarlighetsgrad. Du kan även lägga till en åtgärdsgrupp till aviseringen för att automatisera och anpassa aviseringssvaret.

## <a name="view-a-problem"></a>Visa ett problem

Som standard tillåter Azure kommunikation via alla portar mellan virtuella datorer i samma virtuella nätverk. Senare kanske du eller någon annan i organisationen åsidosätter standardreglerna i Azure och därmed orsakar ett kommunikationsfel. Gör så här för att skapa ett kommunikationsproblem och visa sedan anslutningsövervakaren igen:

1. Skriv *myResourceGroup* i sökrutan högst upp i portalen. När du ser **myResourceGroup** bland sökresultaten markerar du posten.
2. Välj nätverkssäkerhetsgruppen **myVm2-nsg**.
3. Välj **Ingående säkerhetsregler** och sedan **Lägg till**, så som visas i följande bild:

    ![Ingående säkerhetsregler](./media/connection-monitor/inbound-security-rules.png)

4. Standardregeln som tillåter kommunikation mellan alla virtuella datorer i ett virtuellt nätverk heter **AllowVnetInBound**. Skapa en regel med högre prioritet (lägre nummer) än regeln **AllowVnetInBound** som nekar inkommande kommunikation via port 22. Välj eller ange följande information, acceptera standardvärdena för resten av inställningarna och välj sedan **Lägg till**:

    | Inställning                 | Värde          |
    | ---                     | ---            |
    | Målportsintervall | 22             |
    | Åtgärd                  | Avvisa           |
    | Prioritet                | 100            |
    | Namn                    | DenySshInbound |

5. Eftersom anslutningsövervakaren gör avsökningar var 60:e sekund ska du vänta några minuter. Välj **Network Watcher** och **Anslutningsövervakare** till vänster i portalen, och välj sedan övervakaren  **myVm1-myVm2(22)** igen. Nu blir resultatet ett annat, så som visas i följande bild:

    ![Övervaka information om fel](./media/connection-monitor/vm-monitor-fault.png)

    Du ser ett rött utropstecken i statuskolumnen för nätverksgränssnittet **myvm2529**.

6. Välj 10.0.0.5 om du vill se varför statusen har ändrats. Anslutningsövervakaren informerar dig om att orsaken till kommunikationsfelet är: *Trafiken blockerades på grund av följande regel för nätverkssäkerhetsgrupp: UserRule_DenySshInbound*.

    Om du inte hade vetat att någon implementerat den säkerhetsregel du skapade i steg 4 så skulle du se att det är den här regeln som orsakar kommunikationsproblemet i anslutningsövervakaren. Då kan du ändra, åsidosätta eller ta bort regeln för att återställa kommunikationen mellan de virtuella datorerna.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du övervakar en anslutning mellan två virtuella datorer. Du har sett att en regel för en nätverkssäkerhetsgrupp förhindrade kommunikationen till en virtuell dator. Om du vill se alla möjliga svar som en anslutningsövervakare kan returnera kan du läsa om [svarstyper](network-watcher-connectivity-overview.md#response). Du kan också övervaka en anslutning mellan en virtuell dator, ett fullständigt kvalificerat domännamn, en URI eller en IP-adress.

Någon gång kanske du ser att resurser i ett virtuellt nätverk inte kan kommunicera med resurser i andra nätverk som är anslutna via en virtuell nätverksgateway i Azure. I nästa självstudiekurs får du lära dig mer om att diagnostisera problem med en virtuell nätverksgateway.

> [!div class="nextstepaction"]
> [Diagnostisera kommunikationsproblem mellan nätverk](diagnose-communication-problem-between-networks.md)
