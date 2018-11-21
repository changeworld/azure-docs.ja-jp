---
title: Azure Media Services で動画ファイルをストリーム配信する - .NET | Microsoft Docs
description: このクイック スタートの手順では、新しい Azure Media Services アカウントを作成し、ファイルをエンコードして、Azure Media Player にストリーム配信します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: Azure Media Services, ストリーム配信
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: fc8fc1af51332df032e864c84791791a38bc8601
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612222"
---
# <a name="quickstart-stream-video-files---net"></a>クイック スタート: 動画ファイルをストリーム配信する - .NET

このクイック スタートでは、Azure Media Services を利用すると、さまざまなブラウザーおよびデバイスを対象とした動画のエンコードとストリーム配信の開始がいかに容易であるかを示します。 HTTPS URL、SAS URL、または Azure Blob Storage 内に存在するファイルへのパスを使って入力コンテンツを指定できます。
このトピックのサンプルでは、HTTPS URL を使用してアクセスできるようにするコンテンツをエンコードします。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。

このクイック スタートを最後まで行うと、動画をストリーム配信できるようになります。  

![ビデオを再生する](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- Visual Studio がインストールされていない場合は、[Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手できます。
- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

サンプルは、[EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) フォルダーにあります。

サンプルは、次のアクションを実行します。

1. 変換を作成します (最初に、指定された変換が存在するかどうかを確認します)。 
2. エンコード ジョブの出力として使用される出力アセットを作成します。
3. HTTPS の URL に基づいてジョブの入力を作成します。
4. 前に作成された入力と出力を使用してエンコード ジョブを送信します。
5. ジョブの状態を確認します。
6. StreamingLocator を作成します。
7. ストリーミング URL を作成します。

サンプル内の各関数の機能について詳しくは、[このソース ファイル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のコードを調べて、コメントを参照してください。

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

アプリを実行すると、別のプロトコルでビデオを再生するために使用できる URL が表示されます。 

1. Ctrl + F5 キーを押して、*EncodeAndStreamFiles* アプリケーションを実行します。
2. Apple の **HLS** プロトコル (末尾が *manifest(format=m3u8-aapl)*) を選び、コンソールからストリーム配信 URL をコピーします。

![出力](./media/stream-files-tutorial-with-api/output.png)

サンプルの[ソース コード](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)では、URL の作成方法を確認できます。 URL を作成するには、ストリーミング エンドポイントのホスト名とストリーミング ロケーター パスを連結する必要があります。  

## <a name="test-with-azure-media-player"></a>Azure Media Player でテストする

ストリーム配信をテストするため、この記事では Azure Media Player を使います。 

> [!NOTE]
> プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、アプリケーションを実行したときに取得したストリーム配信 URL 値のいずれかを貼り付けます。 
3. **[Update Player]\(プレーヤーの更新\)** をクリックします。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、リソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>コードを確認する

サンプル内の各関数の機能について詳しくは、[このソース ファイル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のコードを調べて、コメントを参照してください。

[ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)に関するチュートリアルでは、より高度なストリーム配信の例と詳しい説明が提供されています。 

## <a name="multithreading"></a>マルチスレッド

Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリケーションで使うときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)
