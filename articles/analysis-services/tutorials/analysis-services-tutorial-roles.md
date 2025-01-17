---
title: Självstudie – Konfigurera administratörs- och användarroller för Azure Analysis Services | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Analysis Services-roller.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4c1a3f52c37dcaad4bc2f84d6d2fa04b61376cf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60788030"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Självstudie: Konfigurera roller för serveradministratör och användare

 I den här självstudien använder du SQL Server Management Studio (SSMS) för att ansluta till din server i Azure och konfigurera serveradministratörs- och modelldatabasroller. Du får också en introduktion till [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL är ett JSON-baserat skriptspråk för tabellmodeller på kompatibilitetsnivå 1 200 och högre. Det kan användas för att automatisera många tabellmodelleringsuppgifter. TMSL används ofta med PowerShell, men i den här kursen använder du XMLA-frågeredigeraren i SSMS. I den här självstudien får du utföra följande uppgifter: 
  
> [!div class="checklist"]
> * Hämta servernamnet från Portal
> * Ansluta till din server med hjälp av SSMS
> * Lägga till en användare eller grupp i serveradministratörsrollen 
> * Lägga till en användare eller grupp i modelldatabasadministratörsrollen
> * Lägga till en ny modelldatabasroll och lägga till en användare eller grupp

Mer information om användarsäkerhet i Azure Analysis Services finns i [Autentisering och användarbehörigheter](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure Active Directory i din prenumeration.
- Du måste ha skapat en [Azure Analysis Services-server](../analysis-services-create-server.md) i din prenumeration.
- Du måste ha [serveradministratörsbehörigheter](../analysis-services-server-admins.md).
- [Lägg till exempelmodellen adventureworks](../analysis-services-create-sample-model.md) på din server.
- [Installera den senaste versionen av SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [portalen](https://portal.azure.com/).

## <a name="get-server-name"></a>Hämta servernamnet
För att kunna ansluta till servern från SSMS behöver du först servernamnet. Du kan hämta servernamnet på Portal.

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
   ![Hämta servernamnet i Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Ansluta i SSMS

För de återstående uppgifterna använder du SSMS för att ansluta till och hantera servern.

1. I SSMS > **Object Explorer** klickar du på **Anslut** > **Analysis Services**.

    ![Anslut](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. I **Server name** (Servernamn) i dialogrutan **Connect to Server** (Anslut till server) klistrar du in servernamnet som du kopierade från Portal. I **Authentication** (Autentisering) väljer du **Active Directory Universal with MFA Support** (Active Directory Universal med MFA-stöd). Ange ditt användarkonto och tryck sedan på **Connect** (Anslut).
   
    ![Ansluta i SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Vi rekommenderar att du väljer Active Directory Universal med MFA-stöd. Den här typen av autentisering stöder [icke-interaktiv autentisering och multifaktorautentisering](../../sql-database/sql-database-ssms-mfa-authentication.md). 

3. Expandera i **Object Explorer** så att serverobjekt visas. Högerklicka om du vill se serveregenskaperna.
   
    ![Ansluta i SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Lägga till ett användarkonto i serveradministratörsrollen

I den här uppgiften lägger du till ett användarkonto eller gruppkonto från Azure AD till serveradministratörsrollen. Om du vill lägga till en säkerhetsgrupp måste den ha egenskapen `MailEnabled` inställd på `True`.

1. Högerklicka på servernamnet i **Object Explorer** och klicka sedan på **Properties** (Egenskaper). 
2. I fönstret **Analysis Server Properties** (Egenskaper för Analysis Server) klickar du på **Security (Säkerhet)** > **Add (Lägg till)**.
3. I fönstret **Select a User or Group** (Välj en användare eller grupp) anger du ett användarkonto eller ett gruppkonto i Azure AD och klickar på **Add** (Lägg till). 
   
     ![Lägga till serveradministratör](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Klicka på **OK** för att stänga **Analysis Server Properties** (Egenskaper för Analysis Server).

    > [!TIP]
    > Du kan också lägga till serveradministratörer med **Analysis Services-administratörer** i Portal. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Lägga till en användare i modelldatabasadministratörsrollen

I den här uppgiften lägger du till ett användarkonto eller gruppkonto i rollen ”Internet Sales Administrator” (Administratör för Internetförsäljning) som redan finns i modellen. Den här rollen har fullständig behörighet (administratör) för exempelmodelldatabasen adventureworks. I uppgiften används TMSL-kommandot [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) i ett skript som skapats åt dig.

1. Expandera **Databases (Databaser)** > **adventureworks** > **Roles (Roller)** i **Object Explorer**. 
2. Högerklicka på **Internet Sales Administrator** (Administratör för Internetförsäljning) och klicka sedan på **Script Role as (Skriptroll som)** > **CREATE OR REPLACE To (SKAPA ELLER ERSÄTT till)** > **New Query Editor Window (Nytt frågeredigerarfönster)**.

    ![Nytt frågeredigerarfönster](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. I **XMLAQuery** ändrar du värdet för **"memberName":** till ett användarkonto eller gruppkonto i Azure AD. Som standard ingår kontot du är inloggad med, men du behöver inte lägga till ett eget konto eftersom du redan är serveradministratör.

    ![TMSL-skript i XMLA-fråga](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Tryck på **F5** för att köra skriptet.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Lägga till en ny modelldatabasroll och lägga till en användare eller grupp

I den här uppgiften använder du kommandot [Create](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/create-command-tmsl?view=sql-analysis-services-2017) i ett TMSL-skript för att skapa en ny Internet Sales Global-roll (Global Internetförsäljning), anger *läsbehörighet* för rollen och lägger till ett användarkonto eller gruppkonto från Azure AD.

1. I **Object Explorer** högerklickar du på **adventureworks** och klickar sedan på **New Query (Ny fråga)** > **XMLA**. 
2. Kopiera och klistra in följande TMSL-skript i frågeredigeraren:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Ändra `"memberName": "globalsales@adventureworks.com"`-objektvärdet till ett användarkonto eller gruppkonto i Azure AD.
4. Tryck på **F5** för att köra skriptet.

## <a name="verify-your-changes"></a>Kontrollera dina ändringar

1. I **Object Explorer** klickar du på servernamnet och sedan på **Refresh** (Uppdatera) eller trycker på **F5**.
2. Expandera **Databases (Databaser)** > **adventureworks** > **Roles (Roller)**. Kontrollera att de ändringar som du lade till i föregående uppgift för användarkonto och den nya rollen visas.   

    ![Kontrollera i Object Explorer](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort användarkonton, gruppkonton och roller när du inte lägre behöver dem. Det gör du  genom att välja **Role Properties (Rollegenskaper)** > **Membership (Medlemskap)** och ta bort användarkonton, eller genom att högerklicka på en roll och sedan klicka på **Delete (Ta bort)**.


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du lära dig hur du ansluter till en Azure AS-server och utforskar adventureworks-exempelmodelldatabaser och egenskaper i SSMS. Du har också fått lära dig hur du använder SSMS och TMSL-skript för att lägga till användare och grupper i befintliga och nya roller. Nu när du har konfigurerat användarbehörigheter för servern och exempelmodelldatabasen kan du och andra användare ansluta till den via klientprogram som Power BI. Fortsätt med nästa självstudie om du vill lära dig mer. 

> [!div class="nextstepaction"]
> [Självstudie: Anslut med Power BI Desktop](analysis-services-tutorial-pbid.md)

