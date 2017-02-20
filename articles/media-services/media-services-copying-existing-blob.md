---
title: "ストレージ アカウントから Azure Media Services 資産に BLOB をコピーする | Microsoft Docs"
description: "このトピックでは、既存の BLOB を Media Services 資産にコピーする方法を説明します。 このサンプルでは、Azure Media Services .NET SDK Extensions を使用します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>既存の BLOB を Media Services 資産にコピーする
このトピックでは、[Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) を使用して、ストレージ アカウントから新しい Microsoft Azure Media Services 資産に BLOB をコピーする方法について説明します。

この拡張機能は次の資産に対して使用できます。

- 通常の資産。
- ライブ アーカイブ資産 (FragBlob 形式)。
- 異なる Media Services アカウント (異なるデータ センター間でも可) に属する送信元と送信先の資産。 ただし、これによって料金が発生する可能性があります。 価格の詳細については、「 [データ転送](https://azure.microsoft.com/pricing/#header-11)」をご覧ください。

> [!NOTE]
> Media Services によって生成された BLOB コンテナーの内容を変更する場合は、必ず Media Service API を使用してください。
> 

## <a name="download-sample"></a>サンプルのダウンロード
この記事の手順に従うか、この記事で説明されているコードを含むサンプルを[ここ](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)からダウンロードできます。

## <a name="prerequisites"></a>前提条件
* 新規または既存の Azure サブスクリプションで作成した&2; つの Media Services アカウント。 「[メディア サービス アカウントの作成方法](media-services-portal-create-account.md)」を参照してください。
* オペレーティング システム: Windows 10、Windows 7、Windows Server 2008 R2、または Windows 8。
* .NET Framework 4.5。
* Visual Studio 2010 SP1 (Professional、Premium、Ultimate、または Express) 以降。

## <a name="set-up-your-project"></a>プロジェクトの設定
このセクションでは、C# コンソール アプリケーション プロジェクトを作成、設定できます。

1. Visual Studio を使用すると、C# コンソール アプリケーション プロジェクトを含む新しいソリューションを作成できます。 
2. 名前に「CopyExistingBlobsIntoAsset」と入力し、[OK] をクリックします。
3. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) を使用して Azure Media Services .NET SDK Extensions (windowsazure.mediaservices.extensions) をインストールして追加します。 このパッケージをインストールすると、Media Services .NET SDK が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
4. このプロジェクト (System.Configuration) に必要なその他の参照を追加します。
6. appSettings セクションを .config ファイルに追加し、Media Services アカウント、送信先のストレージ アカウント、および送信元の資産 ID に基づいて値を更新します。 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>ストレージ アカウントから Media Services 資産に BLOB をコピーする

次のコードは、**IAsset.Copy** 拡張メソッドを使用して、送信元の資産内のすべてのファイルを送信先の資産にコピーします。 非同期をサポートする追加のオーバーロドがあります。

Program.cs クラスのコードを次のコードに置き換えます。

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


