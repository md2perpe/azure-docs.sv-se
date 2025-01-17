---
title: Få åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
description: I självstudien får du åtkomst till virtuella datorer i ett klassrumslabb som har konfigurerats av en lärare.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: bc5c12d4bb92edaafcc9808da8c48106a6e0cbd5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104162"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Få åtkomst till ett klassrumslabb i Azure Lab Services
Artikeln beskriver hur du får åtkomst till ett klassrumslabb, ansluter till den virtuella datorn i labbet och stoppar den virtuella datorn. 

## <a name="register-to-a-lab"></a>Registrera dig till ett labb
1. Gå till **registrerings-URL:en** som du fick av läraren. 
2. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
3. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
2. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Visa alla klassrumslabb
När du registrerat dig till labben kan du visa alla klassrumslabb genom att utföra följande steg: 

1. Navigera till [https://labs.azure.com](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Logga in på tjänsten med hjälp av det användarkonto som du använde för att registrera dig till labbet. 
3. Bekräfta att du ser alla labb som du har åtkomst till. 

    ![Visa alla labb](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Ansluta till den virtuella datorn i ett klassrumslabb

1. Starta den virtuella datorn om den inte redan startats, och välj **Starta** i panelen. 
2. Välj **Anslut** i panelen som representerar den virtuella dator som du vill ha åtkomst till i labbet. 
3. Gör något av följande steg: 
   1. För **Windows** virtuella datorer, spara den **RDP** filen till hårddisken. Öppna RDP-filen för att ansluta till den virtuella datorn. Använd den **användarnamn** och **lösenord** du får från educator/professor att logga in på datorn. 
   3. För **Linux** virtuella datorer, kopiera och spara anslutningssträngen SSH på den **Anslut till den virtuella datorn** dialogrutan. Använd den här anslutningssträngen från en SSH-terminalen (t.ex. [Putty](https://www.putty.org/)) att ansluta till den virtuella datorn.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Stoppa den virtuella datorn i ett klassrumslabb

Om du vill stoppa den virtuella datorn väljer du **Stoppa** på panelen. När den virtuella datorn har stoppats aktiveras knappen **Starta** i panelen. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
 