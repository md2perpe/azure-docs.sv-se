---
title: Ange behörigheter för Data Lake Storage Gen2 med Azure Storage Explorer
description: I dessa anvisningar lär du dig hur du anger behörigheter med Azure Storage Explorer för filer och kataloger på ditt lagringskonto med funktioner för Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.openlocfilehash: 5f0211765c96cad668abaad7d42da87ec88298c3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939315"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Ange behörigheter på fil- och katalognivå med hjälp av Azure Storage Explorer med Azure Data Lake Storage Gen2

Filer som lagras i Azure Data Lake Storage Gen2 stöder detaljerade behörigheter och hantering av åtkomstkontrollistor (ACL). Tillsammans kan du, med detaljerade behörigheter och ACL-hantering, hantera åtkomsten till dina data på en mycket detaljerad nivå.

I den här artikeln lär du dig hur du använder Azure Storage Explorer till att:

> [!div class="checklist"]
> * ange behörigheter på filnivå
> * ange behörigheter på katalognivå
> * lägga till användare eller grupper i en åtkomstkontrollista

## <a name="prerequisites"></a>Nödvändiga komponenter

För att du ska få den bästa beskrivningen av processen behöver du slutföra [snabbstarten för Azure Storage Explorer](data-lake-storage-Explorer.md). Detta garanterar att ditt storage-konto är i det lämpligaste tillståndet (filsystem som har skapats och data som överförs till den).

## <a name="managing-access"></a>Hantera åtkomst

Du kan ange behörigheter i roten för filsystemet. Om du vill göra det måste du vara inloggad i Azure Storage Explorer med ditt individuella konto med behörighet att göra detta (i motsats till med en anslutningssträng). Högerklicka på ditt filsystem och välj **hantera behörigheter**, att både upp den **hantera behörighet** dialogrutan.

![Microsoft Azure Storage Explorer – Hantera katalogåtkomst](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

I dialogrutan **Hantera behörighet** kan du hantera behörigheter för ägare och ägargruppen. Du kan även lägga till nya användare och grupper i åtkomstkontrollistan, som du sedan kan lägga till behörigheter för.

Du kan lägga till en ny användare eller grupp i åtkomstkontrollistan genom att välja fältet **Lägg till användare eller grupp**.

Ange motsvarande Azure Active Directory-post (AAD) du vill lägga till i listan och välj sedan **Lägg till**.

Användaren eller gruppen visas nu i fältet **Användare och grupper:**, där du kan börja hantera deras behörigheter.

> [!NOTE]
> Vi rekommenderar bästa praxis att skapa en säkerhetsgrupp i AAD och hantera behörigheter för gruppen istället för enskilda användare. Information om den här rekommendationen, samt andra metodtips, finns i [metodtipsen för Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Det finns två kategorier av behörigheter du kan tilldela: åtkomst-ACL och standard-ACL.

* **Åtkomst**: Åtkomst-ACL:er kontrollerar åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

* **Standard**: En mall med ACL:er som är associerade med en katalog som bestämmer åtkomst-ACL:erna för underordnade objekt som skapas under den katalogen. Filer har inte standard-ACL:er.

I båda dessa kategorier finns det tre behörigheter du sedan kan tilldela för filer eller kataloger: **läsa**, **skriva** och **köra**.

>[!NOTE]
> När du gör val här anges inte behörigheter för några befintliga objekt i katalogen. Du måste gå till varje enskilt objekt och ange behörigheterna manuellt, om filen redan finns.

Du kan hantera behörigheter för enskilda kataloger, och enskilda filer, vilket är det som ger dig detaljerad åtkomstkontroll. Processen för att hantera behörigheter för både kataloger och filer är samma som beskrivs ovan. Högerklicka på filen eller katalogen du vill hantera behörigheter för och följ samma process.

## <a name="next-steps"></a>Nästa steg

I dessa anvisningar har du lärt dig hur du anger behörigheter för filer och kataloger med hjälp av **Azure Storage Explorer**. Mer information om åtkomstkontrollistor (ACL), t.ex. standard- och åtkomst-ACL:er, och deras motsvarande behörigheter får du om du fortsätter till vår konceptuella artikel om ämnet.

> [!div class="nextstepaction"]
> [Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
