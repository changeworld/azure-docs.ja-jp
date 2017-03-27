---
title: "Media Services を使用する Azure 関数の開発"
description: "このトピックでは、Media Services を使用する Azure 関数を Azure Portal で開発する方法について説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Media Services を使用する Azure 関数の開発
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

このトピックでは、Media Services を使用する Azure 関数を Azure Portal で開発する方法について説明します。 

[こちら](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)から **[Azure に配置する]** ボタンを押して既存の Media Services Azure 関数をデプロイすることもできます。 このリポジトリには、Azure Media Services を使った Azure 関数のサンプルが格納されています。Blob Storage から直接コンテンツを取り込んだり、エンコードしたり、Blob Storage にコンテンツを書き戻したりする処理に関連するワークフローの例が紹介されています。 また、webhook と Azure キューを介してジョブの通知を監視するサンプルも含まれています。

[この](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration)リポジトリのサンプルを基に関数を開発することができます。 このトピックでは、Media Services を使用した Azure 関数の作成方法について説明しています。 

## <a name="prerequisites"></a>前提条件

初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。

Azure Media Services (AMS) アカウントでアクションを実行する Azure 関数を作成したり、Media Services から送信されるイベントをリッスンしたりするためには、[こちら](media-services-portal-create-account.md)の説明に従って AMS アカウントを作成する必要があります。

## <a name="create-a-function-app"></a>Function App を作成する

[こちら](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app)の説明に従って関数アプリを作成します。

## <a name="create-a-function"></a>関数を作成する

デプロイした関数アプリは、**[App Services]** の Azure Functions に表示されます。 

1. 目的の関数アプリを選択し、**[新しい関数]** をクリックします。
3. **[C#]** 言語を選択し、**[Webhook + API]** シナリオを選択します。
3. **[GenericWebHook-CSharp]** (webhook 要求を受信するたびに実行される) または **[HttpTrigger-CSharp]** (HTTP 要求を受信するたびに実行される) を選択し、関数の名前を変更します。
4. ページの下部にある **[Create]**」を参照してください。 

## <a name="get-function-url"></a>関数の URL の取得

HTTP テスト ツールや別のブラウザー ウィンドウから関数の実行をトリガーするには、[関数の URL] の値が必要となります。 

![[設定]](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>ファイル

開発した Azure 関数は、コード ファイルなど、このセクションで取り上げる各種ファイルに関連付けることになります。 既定では、**function.json** ファイルと **run.csx** ファイルに関数が関連付けられます。 **project.json** ファイルを追加する必要があります。 以降このセクションでは、これらのファイルの定義を紹介していきます。

![ファイルのアップロード](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json ファイルは、関数バインドとその他の構成設定を定義します。 ランタイムはこのファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 

以下に **function.json** ファイルの例を示します。

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

project.json ファイルには、依存関係が含まれています。 以下に示したのは、AMS ライブラリを含んだ **function.json** ファイルの例です。

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

これは関数の C# コードです。 webhook 関数の例については、[こちら](media-services-dotnet-check-job-progress-with-webhooks.md)のトピックを参照してください。 

必要な関数を定義したら、**[実行]** をクリックします。
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>関数アプリの設定の構成

Media Services の関数を開発するときは、自分が開発するさまざまな関数で使用するパラメーターを **[アプリケーション設定]** セクションに追加しておくと便利です。 

次に例を示します。

![[設定]](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>次のステップ

これで、Media Services アプリケーションの開発準備が整いました。 詳細については、「[Azure WebHook を使用して .NET で Media Services ジョブ通知を監視する](media-services-dotnet-check-job-progress-with-webhooks.md)」を参照してください。   

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


