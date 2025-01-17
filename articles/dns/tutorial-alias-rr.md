---
title: Självstudie – skapa en Azure DNS-aliaspost för att referera till en resurspost i zonen.
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att referera till en resurspost i zonen.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 3b4ee688d6a5606ab6008b459fcf6331c24afaae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429798"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Självstudier: Skapa en aliaspost för att referera till en resurspost för zonen

Aliasposter kan referera till andra postuppsättningar av samma typ. Du kan till exempel låta en DNS CNAME-postuppsättning vara ett alias för en annan CNAME-postuppsättning av samma typ. Den här funktionen är användbar om du vill ha vissa postuppsättningar som alias och andra som alias när det gäller beteende.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en aliaspost för en resurspost i zonen.
> * Testa aliasposten.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [självstudien: Använda Azure DNS som värd för din domän.](dns-delegate-domain-azure-dns.md)


## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost för som pekar mot en resurspost i zonen.

### <a name="create-the-target-resource-record"></a>Skapa målresursposten
1. Välj din Azure DNS-zon för att öppna zonen.
2. Välj **Postuppsättning**.
3. I textrutan **Namn** anger du **server**.
4. För **Typ**, väljer du **A**.
5. I textrutan **IP-ADRESS** anger du **10.10.10.10**.
6. Välj **OK**.

### <a name="create-the-alias-record"></a>Skapa aliasposten
1. Välj din Azure DNS-zon för att öppna zonen.
2. Välj **Postuppsättning**.
3. I textrutan **Namn** anger du **test**.
4. För **Typ**, väljer du **A**.
5. Välj **Ja** i kryssrutan **Aliaspostuppsättning**. Välj sedan alternativet **Zonpostuppsättning**.
6. Som **zonpostuppsättning**väljer du posten **server**.
7. Välj **OK**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. Starta ditt föredragna nslookup-verktyg. Ett alternativ är att bläddra till [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Ange frågetyp för A-poster och sök efter **test.\< ditt domännamn\>**. Svaret **10.10.10.10**.
3. I Azure-portalen, ändrar du **server** A-posten till **10.11.11.11**.
4. Vänta några minuter och använd därefter nslookup igen för att **testa** posten. Svaret **10.11.11.11**.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver resurserna för den här självstudiekursen tar du bört resursposterna **server** och **test** i zonen.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en aliaspost för att referera till en resurspost i zonen. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
