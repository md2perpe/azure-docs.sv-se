---
title: Skapa ContentKeys med .NET
description: Lär dig hur du skapar nycklar som ger säker åtkomst till resurser.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab26be3b9ac5d209cfe8117bdf9e87e0c7e74188
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465583"
---
# <a name="create-contentkeys-with-net"></a>Skapa ContentKeys med .NET 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services kan du skapa och leverera krypterade tillgångar. En **ContentKey** ger säker åtkomst till din **tillgången**s. 

När du skapar en ny tillgång (till exempel innan du [ladda upp filer](media-services-dotnet-upload-files.md)), kan du ange följande alternativ för kryptering: **StorageEncrypted**, **CommonEncryptionProtected**, eller **EnvelopeEncryptionProtected**. 

När du levererar tillgångar till dina klienter kan du [konfigurera för tillgångar ska vara dynamiskt krypterad](media-services-dotnet-configure-asset-delivery-policy.md) med någon av följande två krypteringar: **DynamicEnvelopeEncryption** eller **DynamicCommonEncryption**.

Krypterade tillgångar måste associeras med **ContentKey**s. Den här artikeln beskriver hur du skapar en innehållsnyckel.

> [!NOTE]
> När du skapar en ny **StorageEncrypted** tillgång med Media Services .NET SDK, den **ContentKey** skapas automatiskt och som är kopplad till tillgången.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
En av de värden som du måste anges när du skapar en innehåll nyckeln är viktiga innehållstyp. Välj något av följande värden. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a id="envelope_contentkey"></a>Skapa kuvert typen ContentKey
Följande kodfragment skapar en innehållsnyckel av typen kuvert-kryptering. Nyckeln kopplas sedan till den angivna tillgången.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>Skapa gemensam typ. ContentKey
Följande kodfragment skapar en innehållsnyckel av krypteringstypen gemensam. Nyckeln kopplas sedan till den angivna tillgången.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

