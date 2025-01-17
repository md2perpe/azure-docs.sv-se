---
title: Skapa en Machine Learning Studio-arbetsyta
titleSuffix: Azure Machine Learning Studio
description: Du måste ha en Machine Learning Studio-arbetsyta om du vill använda Azure Machine Learning Studio. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109356"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Skapa och dela en Azure Machine Learning Studio-arbetsyta

Du måste ha en Machine Learning Studio-arbetsyta om du vill använda Azure Machine Learning Studio. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

## <a name="create-a-studio-workspace"></a>Skapa en Studio-arbetsyta

1. Logga in på [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Du måste vara administratör för Azure-prenumeration för att logga in och skapa en Studio-arbetsyta. 
    >
    > 

2. Klicka på **+ ny**

3. I sökrutan skriver **Machine Learning Studio-arbetsyta** och välj det matchande objektet. Klicka sedan på Välj **skapa** längst ned på sidan.

4. Ange informationen om arbetsytan:

   - Den *Arbetsytenamn* kan vara upp till 260 tecken, inte slutar med ett blanksteg. Namnet får inte innehålla följande tecken: `< > * % & : \ ? + /`
   - Den *webbtjänstplan* du väljer (eller skapa), tillsammans med den associerade *prisnivån* du väljer, används om du distribuerar webbtjänster från den här arbetsytan.

     ![Skapa en ny Studio-arbetsyta](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**.

> [!NOTE]
> Machine Learning Studio är beroende av ett Azure storage-konto som du anger för att spara mellanliggande data när arbetsflödet körs. När arbetsytan skapas, om lagringskontot tas bort, eller om åtkomstnycklarna ändras, arbetsytan slutar att fungera och alla experiment på arbetsytan misslyckas.
Om du råkar ta bort lagringskontot, återskapar lagringskontot med samma namn i samma region som det borttagna lagringskontot och synkroniserar du om åtkomstnyckeln. Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av Azure Portal.

När arbetsytan har distribuerats kan öppna du den i Machine Learning Studio.

1. Bläddra på Machine Learning Studio på [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **Mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din Studio-arbetsyta finns i [hantera en Azure Machine Learning Studio-arbetsyta](manage-workspace.md).
Om du stöter på problem med att skapa din arbetsyta kan se [felsökningsguide för: Skapa och ansluta till en Machine Learning Studio-arbetsyta](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Dela en Azure Machine Learning Studio-arbetsyta
När en Machine Learning Studio arbetsytan har skapats, du kan bjuda in användare till din arbetsyta dela åtkomst till din arbetsyta och alla dess experiment, datauppsättningar, anteckningsböcker, osv. Du kan lägga till användare i en av två roller:

* **Användaren** – en arbetsyta-användare kan skapa, öppna, ändra och ta bort experiment, datauppsättningar, etc. på arbetsytan.
* **Ägare** – en ägare kan bjuda in och ta bort användare i arbetsytan, utöver vad en användare kan göra.

> [!NOTE]
> Det administratörskonto som skapar arbetsytan läggs automatiskt till arbetsytan som arbetsyta ägare. Men andra administratörer eller användare i den aktuella prenumerationen beviljas automatiskt inte åtkomst till arbetsytan – du behöver att bjuda in dem explicit.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Dela en Studio-arbetsyta

1. Logga in på Machine Learning Studio på [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. I den vänstra rutan klickar du på **inställningar**

3. Klicka på den **användare** fliken

4. Klicka på **bjuda in fler användare** längst ned på sidan

    ![Studio-inställningar](./media/create-workspace/settings.png)

5. Ange en eller flera e-postadresser. Användarna behöver ett giltigt microsoftkonto eller ett organisationskonto (från Azure Active Directory).

6. Välj om du vill lägga till användarna som ägare eller användare.

7. Klicka på den **OK** markeringen knappen.

Varje användare som du lägger till får ett e-postmeddelande med anvisningar om hur du logga in på den delade arbetsytan.

> [!NOTE]
> För användare för att kunna distribuera och hantera webbtjänster på den här arbetsytan kan vara de deltagare / administratör i Azure-prenumeration. 



