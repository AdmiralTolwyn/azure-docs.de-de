---
title: Erstellen von ContentKeys mit .NET
description: "Erfahren Sie, wie Sie Inhaltsschlüssel erstellen, die den sicheren Zugriff auf Medienobjekte ermöglichen."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c547afd9535eb4764caf6b37d4e38a22f6e88186
ms.contentlocale: de-de
ms.lasthandoff: 11/17/2016

---
# <a name="create-contentkeys-with-net"></a>Erstellen von ContentKeys mit .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services ermöglicht das Erstellen und Übermitteln verschlüsselter Medienobjekte. Ein **ContentKey** ermöglicht den sicheren Zugriff auf Ihre **Medienobjekte**. 

Beim Erstellen eines neuen Medienobjekts (z.B. vor dem [Hochladen von Dateien](media-services-dotnet-upload-files.md)) können Sie die folgenden Verschlüsselungsoptionen angeben: **StorageEncrypted**, **CommonEncryptionProtected** oder **EnvelopeEncryptionProtected**. 

Wenn Sie Medienobjekte an Ihre Clients übermitteln, können Sie mithilfe einer der beiden folgenden Verschlüsselungen die [dynamische Verschlüsselung von Medienobjekten konfigurieren](media-services-dotnet-configure-asset-delivery-policy.md): **DynamicEnvelopeEncryption** oder **DynamicCommonEncryption**.

Verschlüsselte Medienobjekte müssen **ContentKeys**zugeordnet werden. In diesem Artikel wird beschrieben, wie ein Inhaltsschlüssel erstellt wird.

> [!NOTE]
> Beim Erstellen eines neuen **StorageEncrypted**-Medienobjekts mithilfe des Media Services .NET SDK wird der **ContentKey** automatisch erstellt und mit dem Medienobjekt verknüpft.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Einer der Werte, die Sie beim Erstellen eines Inhaltsschlüssels festlegen müssen, ist der Typ des Inhaltsschlüssels. Wählen Sie unter folgenden Werten aus. 

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

## <a id="envelope_contentkey"></a>Erstellen eines "ContentKey" vom Typ "Umschlagverschlüsselung"
Im folgenden Codeausschnitt wird ein Inhaltsschlüssel vom Typ „Umschlagverschlüsselung“ erstellt. Anschließend wird der Schlüssel dem angegebenen Medienobjekt zugeordnet.

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

Aufruf

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>Erstellen eines "ContentKey" vom Typ "Allgemeine Verschlüsselung"
Im folgenden Codeausschnitt wird ein Inhaltsschlüssel vom Typ „Allgemeine Verschlüsselung“ erstellt. Anschließend wird der Schlüssel dem angegebenen Medienobjekt zugeordnet.

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
Aufruf

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


