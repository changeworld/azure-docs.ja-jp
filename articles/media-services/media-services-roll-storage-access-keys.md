---
title: "ストレージ アクセス キーをローリングした後に Media Services を更新する | Microsoft Docs"
description: "この記事は、ストレージ アクセス キーをローリングした後に Media Services を更新する際のガイダンスについて説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: milangada;cenkdin;juliako
translationtype: Human Translation
ms.sourcegitcommit: 602f86f17baffe706f27963e8d9963f082971f54
ms.openlocfilehash: a979519dc617f40e6f090a412d17aa7778cbcf69


---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>ストレージ アクセス キーをローリングした後に Media Services を更新する
新しく Azure Media Services アカウントを作成すると、メディア コンテンツの保存に使用する Azure ストレージ アカウントも選択するよう求めるメッセージが表示されます。 Media Services アカウントには [複数のストレージ アカウントを追加](meda-services-managing-multiple-storage-accounts.md) できます。

新しいストレージ アカウントが作成されると、Azure は、ストレージ アカウントへのアクセス認証に使用される 512 ビット ストレージ アクセス キーを 2 つ生成します。 ストレージの接続の安全性を高めるため、ストレージ アクセス キーを定期的に再生成して入れ換えることをお勧めします。 片方のアクセス キーでストレージ アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ (プライマリとセカンダリ) 提供されます。 この手順は、「アクセス キーのローリング」とも呼ばれます。

Media Services は、指定されたストレージ キーに依存します。 特に、アセットのストリーミングやダウンロードに使用されるロケーターは指定されたアクセス キーに依存します。 AMS アカウントが作成されると、既定ではプライマリ ストレージ アクセス キーに依存しますが、ユーザーは AMS が使用するストレージ キーを更新できます。 このトピックで説明する次の手順に従って、どのキーを使用するかを Media Services に確実に認識させる必要があります。 さらに、ストレージ アクセス キーをローリングする際は、ストリーミング サービスが中断されないようにロケーターを更新する必要があります (この手順もこのトピックに説明があります)。

> [!NOTE]
> ストレージ アカウントが複数ある場合、この手順をストレージ アカウントごとに実行する必要があります。
>
> このトピックで説明する手順を実稼働アカウントで実行する前に、実稼働前のアカウントでテストするようにしてください。
>
>

## <a name="step-1-regenerate-secondary-storage-access-key"></a>手順 1: セカンダリ ストレージ アクセス キーを再生成する
セカンダリ ストレージ キーの再生成から開始します。 既定では、セカンダリ キーは Media Services で使用されません。  ストレージ キーをローリングする方法の詳細については、「 [方法: ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」を参照してください。

## <a name="a-idstep2astep-2-update-media-services-to-use-the-new-secondary-storage-key"></a><a id="step2"></a>手順 2: Media Services を更新して新しいセカンダリ ストレージ キーを使用する
Media Services を更新してセカンダリ ストレージ アクセス キーを使用します。 再生成されたストレージ キーを Media Services と同期するには、次の 2 つの方法のどちらかを使用できます。

* Azure Portal を使用します。 名前とキーの値を検索するには、Azure Portal に移動してアカウントを選択します。 [設定] ウィンドウが右側に表示されます。 [設定] ウィンドウで、[キー] を選択します。 Media Services で同期するストレージ キーに応じて、同期するキーをプライマリ キーとセカンダリ キーのどちらかに指定します。 この例では、セカンダリ キーを使用します。
* Media Services Management REST API を使用します。

次のコード例は、指定したストレージ キーを Media Services と同期するために、https://endpoint/*subscriptionId*/services/mediaservices/Accounts/*accountName*/StorageAccounts/*storageAccountName*/Key 要求を作成する方法を示しています。 この例では、セカンダリ ストレージ キーの値が使用されます。 詳細については、「 [方法: Media Services Management REST API の使用](http://msdn.microsoft.com/library/azure/dn167656.aspx)」を参照してください。

    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);

        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);


        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }

        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

この手順を実行した後で、既存のロケーター (古いストレージ キーに依存しているロケーター) を次の手順に従って更新します。

> [!NOTE]
> 保留中のジョブに影響しないよう、30 分待ってから、Media Services で操作を行います (新しいロケーターの作成など)。
>
>

## <a name="step-3-update-locators"></a>手順 3: ロケーターを更新する
> [!NOTE]
> ストレージ アクセス キーをローリングする際は、ストリーミング サービスが中断されないように既存のロケーターを更新する必要があります。
>
>

新しいストレージ キーを AMS と同期した後、少なくとも 30 分間待ちます。 その後、OnDemand ロケーターを再作成して、指定したプライマリ ストレージ キーとの依存関係を確立し、既存の URL を保持できます。

SAS ロケーターを更新 (または再作成) するときに、URL は常に変更されることに注意してください。

> [!NOTE]
> OnDemand ロケーターの既存の URL を保持するには、既存のロケーターを削除し、同じ ID を持つ新しいロケーターを作成する必要があります。
>
>

以下の .NET の例は、同じ ID のロケーターを再作成する方法を示しています。

private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator) { // Save properties of existing locator.
var asset = locator.Asset; var accessPolicy = locator.AccessPolicy; var locatorId = locator.Id; var startDate = locator.StartTime; var locatorType = locator.Type; var locatorName = locator.Name;

// Delete old locator.
locator.Delete();

if (locator.ExpirationDateTime <= DateTime.UtcNow) { throw new Exception(String.Format( "Cannot recreate locator Id={0} because its locator expiration time is in the past", locator.Id)); }

        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);



        return newLocator;
    }


## <a name="step-5-regenerate-primary-storage-access-key"></a>手順 5: プライマリ ストレージ アクセス キーを再生成する
プライマリ ストレージ アクセス キーを再生成します。 ストレージ キーをローリングする方法の詳細については、「 [方法: ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」を参照してください。

## <a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>手順 6: Media Services を更新して新しいプライマリ ストレージ キーを使用する
[手順 2](media-services-roll-storage-access-keys.md#step2) と同じ手順を使用しますが、ここで Media Services アカウントと同期するのは新しいプライマリ ストレージ アクセス キーです。

> [!NOTE]
> 保留中のジョブに影響しないよう、30 分待ってから、Media Services で操作を行います (新しいロケーターの作成など)。
>
>

## <a name="step-7-update-locators"></a>手順 7: ロケーターを更新する
30 分が経過したら、OnDemand ロケーターを再作成して、新しいプライマリ ストレージ キーとの依存関係を確立し、既存の URL を保持できます。

[手順 3.](media-services-roll-storage-access-keys.md#step-3-update-locators)と同じ操作を行います。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>謝辞
この文書の作成に協力してくれた Cenk Dingiloglu、Milan Gada、 Seva Titov に感謝します。



<!--HONumber=Nov16_HO3-->


