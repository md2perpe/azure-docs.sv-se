---
title: Lägg till pushmeddelanden i din Xamarin.Forms-app | Microsoft Docs
description: Lär dig hur du använder Azure-tjänster för att skicka flera plattformar push-meddelanden till din Xamarin.Forms-appar.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b7e2ff63211ec5891a48a585e4f69e18116cdeb3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446583"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Lägg till pushmeddelanden i din Xamarin.Forms-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center investerar i nya och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda **skapa**, **Test** och **fördela** tjänster för att konfigurera pipeline för kontinuerlig integrering och leverans. När appen har distribuerats, utvecklare kan övervaka status och användningen av sin app med hjälp av den **Analytics** och **diagnostik** services och interagera med användare som använder den **Push** tjänsten. Utvecklare kan även utnyttja **Auth** att autentisera användarna och **Data** -tjänsten för att bevara och synkronisera AppData i molnet. Kolla in [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-push) idag.
>

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till alla projekt som härrör från den [Xamarin.Forms Snabbstart](app-service-mobile-xamarin-forms-get-started.md). Det innebär att ett push-meddelande skickas till alla plattformar klienter varje gång en post infogas.

Om du inte använder serverprojekt hämtade Snabbstart måste tilläggspaket för push-meddelande. Mer information finns i [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

För iOS, behöver du en [Apple Developer Program medlemskap](https://developer.apple.com/programs/ios/) och en fysisk iOS-enhet. Den [stöder inte push-meddelanden i iOS-simulatorn](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurera en notification hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera serverprojektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurera och kör androidprojektet (valfritt)

Slutför det här avsnittet om du vill aktivera push-meddelanden för Xamarin.Forms Droid-projektet för Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Aktivera Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurera Mobile Apps-serverdelen för att skicka push-begäranden med hjälp av FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Lägg till push-meddelanden till Android-projekt

Med backend-servern konfigurerats med FCM, kan du lägga till komponenter och koder till klienten för att registrera med FCM. Du kan också registrera för push-meddelanden med Azure Notification Hubs via Mobile Apps-serverdel och ta emot meddelanden.

1. I den **Droid** projektet genom att högerklicka på **referenser > Hantera NuGet-paket...** .
1. I fönstret NuGet Package Manager söker du efter den **Xamarin.Firebase.Messaging** paketera och lägga till den i projektet.
1. I egenskaperna för projektet för den **Droid** projektet genom att ange appen att kompilera med hjälp av Android-version 7.0 eller senare.
1. Lägg till den **google-services.json** fil som hämtats från Firebase-konsolen till roten i den **Droid** projektet och ange dess byggåtgärden till **GoogleServicesJson**. Mer information finns i [lägga till Google Services JSON-filen](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registering med Firebase Cloud Messaging

1. Öppna filen **AndroidManifest.xml** och infoga följande `<receiver>`-element i elementet `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementera Firebase instans-ID-tjänst

1. Lägg till en ny klass till den **Droid** projektet med namnet `FirebaseRegistrationService`, och se till att följande `using` instruktioner finns överst i filen:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Ersätt tomt `FirebaseRegistrationService` klassen med följande kod:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    Den `FirebaseRegistrationService` klassen är ansvarig för att generera säkerhetstoken som ge programmet åtkomst till FCM. Den `OnTokenRefresh` metoden anropas när programmet tar emot en registreringstoken från FCM. Metoden hämtar token från den `FirebaseInstanceId.Instance.Token` egenskapen, som uppdateras asynkront med FCM. Den `OnTokenRefresh` metoden anropas sällan, eftersom token uppdateras när programmet installeras eller avinstalleras, när användaren tar bort programdata när programmet raderas instans-ID eller när säkerheten för token har komprometterade. Dessutom kan begär tjänsten FCM instans-ID att programmet uppdaterar sin token med jämna mellanrum, vanligtvis var sjätte månad.

    Den `OnTokenRefresh` anropar också metoden den `SendRegistrationTokenToAzureNotificationHub` metod som används för att associera användartoken registrering med Azure Notification Hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrera med Azure Notification Hub

1. Lägg till en ny klass till den **Droid** projektet med namnet `AzureNotificationHubService`, och se till att följande `using` instruktioner finns överst i filen:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Ersätt tomt `AzureNotificationHubService` klassen med följande kod:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    Den `RegisterAsync` metoden skapar en mall för enkel aviseringsmeddelanden som JSON och registrerar sig för att ta emot mallvarningar från meddelandehubben, med hjälp av Firebase-registreringstoken. Detta säkerställer att alla meddelanden som skickas från Azure Notification Hub gäller enheten som representeras av registreringstoken.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visar innehållet i ett Push-meddelande

1. Lägg till en ny klass till den **Droid** projektet med namnet `FirebaseNotificationService`, och se till att följande `using` instruktioner finns överst i filen:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Ersätt tomt `FirebaseNotificationService` klassen med följande kod:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    Den `OnMessageReceived` metod som anropas när ett program tar emot ett meddelande från FCM, extraherar innehåll meddelandet och anropar den `SendNotification` metoden. Den här metoden konverterar meddelandeinnehåll till en lokal meddelande om att startas medan programmet körs med meddelandet som visas i meddelandefältet.

Nu är du redo test-push-meddelanden i appen som körs på en Android-enhet eller emulatorn.

### <a name="test-push-notifications-in-your-android-app"></a>Test-push-meddelanden i din Android-app

De två första stegen krävs bara när du testar på en emulator.

1. Se till att du distribuerar till eller felsökning på en enhet eller emulator som är konfigurerad med Google Play-tjänster. Detta kan verifieras genom att kontrollera som den **spela upp** appar installeras på enheten eller emulatorn.
2. Lägg till ett Google-konto till Android-enhet genom att klicka på **appar** > **inställningar** > **Lägg till konto**. Följ sedan anvisningarna för att lägga till ett befintligt Google-konto till enheten eller skapa en ny.
3. I Visual Studio eller Xamarin Studio högerklickar du på den **Droid** projektet och klicka på **Ställ in som Startprojekt**.
4. Klicka på **kör** att skapa projektet och starta appen på din Android-enhet eller emulator.
5. Skriv en uppgift i appen, och klicka sedan på plustecknet ( **+** ) ikonen.
6. Kontrollera att ett meddelande tas emot när ett objekt läggs till.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurera och köra iOS-projektet (valfritt)

Det här avsnittet handlar om att köra Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurera meddelandehubben för APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Därefter konfigurerar du inställningen för iOS-projekt i Xamarin Studio eller Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Lägg till push-meddelanden till iOS-app

1. I den **iOS** projekt, öppna AppDelegate.cs och Lägg till följande uttryck överst i kodfilen.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. I den **AppDelegate** klass, Lägg till en åsidosättning för den **RegisteredForRemoteNotifications** händelse registreras för meddelanden om:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. I **AppDelegate**, även lägga till följande åsidosättningen för den **DidReceiveRemoteNotification** händelsehanteraren:

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Den här metoden hanterar inkommande meddelanden när appen körs.

4. I den **AppDelegate** klass, Lägg till följande kod till den **FinishedLaunching** metod:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Detta möjliggör stöd för fjärranslutna meddelanden och registrering av push-begäranden.

Din app har uppdaterats för att stödja push-meddelanden.

#### <a name="test-push-notifications-in-your-ios-app"></a>Test-push-meddelanden i din iOS-app

1. Högerklicka på iOS-projektet och klicka på **Set as StartUp Project**.
2. Tryck på den **kör** knappen eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Klicka sedan på **OK** accepterar push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen godkänna push-meddelanden från din app. Den här begäran inträffar bara första gången som appen körs.

3. Skriv en uppgift i appen, och klicka sedan på plustecknet ( **+** ) ikonen.
4. Kontrollera att ett meddelande tas emot och klicka sedan på **OK** att stänga meddelandet.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurera och köra Windows-projekt (valfritt)

Det här avsnittet handlar om att köra Xamarin.Forms WinApp och WinPhone81 projekt för Windows-enheter. De här stegen har också stöd för Universal Windows Platform (UWP)-projekt. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrera din Windows-app för push-meddelanden med Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Lägg till push-meddelanden till din Windows-app

1. Öppna i Visual Studio **App.xaml.cs** i en Windows projekt och Lägg till följande instruktioner.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Ersätt `<your_TodoItemManager_portable_class_namespace>` med namnområdet för ditt projekt, bärbar som innehåller den `TodoItemManager` klass.

2. I App.xaml.cs lägger du till följande **InitNotificationsAsync** metod:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Den här metoden hämtar meddelandekanal push och registrerar en mall för att ta emot meddelanden om mallar från din meddelandehubb. Ett mall-meddelande som har stöd för *messageParam* levereras till den här klienten.

3. I App.xaml.cs, uppdaterar den **OnLaunched** händelse hanteraren metoddefinitionen genom att lägga till den `async` modifieraren. Lägg sedan till följande kodrad i slutet av metoden:

    ```csharp
    await InitNotificationsAsync();
    ```

    Detta säkerställer att push-meddelanderegistreringen skapas eller uppdateras varje gång appen startas. Det är viktigt att göra detta för att garantera att push-kanalen WNS alltid är aktiv.  

4. I Solution Explorer för Visual Studio, öppna den **Package.appxmanifest** filen och ange **popup-kompatibel** till **Ja** under **meddelanden**.
5. Skapa appen och kontrollera att du har inga fel. Klientappen bör nu registrera för mall-meddelanden från Mobile Apps-serverdel. Upprepa det här avsnittet för varje Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Test-push-meddelanden i din Windows-app

1. Högerklicka på en Windows-projekt i Visual Studio och klicka på **Ställ in som Startprojekt**.
2. Tryck på **Run** (Kör) för att skapa projektet och starta appen.
3. Skriv ett namn för en ny todoitem i appen, och klicka sedan på plustecknet ( **+** ) ikonen för att lägga till den.
4. Kontrollera att ett meddelande tas emot när objektet har lagts till.

## <a name="next-steps"></a>Nästa steg

Mer information om push-meddelanden:

* [Skicka Push-meddelanden från Azure-appar](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Remote-meddelanden med Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostisera problem med push-meddelande](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Det finns olika orsaker varför meddelanden kan tas bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och ta reda på orsaken till fel för push-meddelande.

Du kan också fortsätta in på någon av följande självstudiekurser:

* [Lägg till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en Mobile Apps-serverdel. Med offlinesynkronisering kan användare kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
