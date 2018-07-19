---
title: Azure Machine Learning モデル管理 Web サービスの使用 | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning でモデル管理を使用してデプロイされた Web サービスの使用に関連する手順および概念について説明します。
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 4a49ccff68003cf7b81a7d945176992a2893d1ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973174"
---
# <a name="consuming-web-services"></a>Web サービスの使用
モデルをリアルタイム Web サービスとしてデプロイしたら、さまざまなプラットフォームやアプリケーションから予測を取得できるようになります。 リアルタイム Web サービスでは、予測を取得するために REST API を公開します。 単一行または複数行の形式でデータを Web サービスに送信して、1 つまたは複数の予測を一度に取得できます。

[Azure Machine Learning Web サービス](model-management-service-deploy.md)では、外部のアプリケーションは、サービス URL に HTTP POST 呼び出しを実行することによって、予測モデルと同期的に通信します。 Web サービス呼び出しを実行するには、クライアント アプリケーションは、予測のデプロイ時に作成される API キーを指定して、要求データを POST 要求の本文に格納する必要があります。

> [!NOTE]
> API キーを使用できるのは、クラスター デプロイ モードだけであることに注意してください。 ローカルの Web サービスにはキーがありません。

## <a name="service-deployment-options"></a>サービス デプロイのオプション
Azure Machine Learning Web サービスは、実稼働とテストの両方のシナリオでクラウド ベースのクラスターにデプロイでき、Docker エンジンを使用してローカル ワークステーションにデプロイできます。 どちらのケースでも、予測モデルの機能は同じです。 クラスター ベースのデプロイでは、Azure Container Service に基づいて、スケーラブルでパフォーマンスの高いソリューションが提供されます。一方、ローカル デプロイは、デバッグに使用できます。 

Azure Machine Learning CLI と API には、```az ml env``` オプションを使用したサービス デプロイ用のコンピューティング環境を作成して管理するのに役立つコマンドが用意されています。 

## <a name="list-deployed-services-and-images"></a>デプロイされたサービスとイメージの一覧表示
CLI コマンド ```az ml service list realtime -o table``` を使用して、現在デプロイされているサービスと Docker イメージを一覧表示できます。 このコマンドは、常に現在のコンピューティング環境のコンテキストで動作することに注意してください。 現在の環境として設定されていない環境にデプロイされているサービスは表示されません。 環境を設定するには、```az ml env set``` を使用します。 

## <a name="get-service-information"></a>サービス情報の取得
Web サービスが正常にデプロイされたら、次のコマンドを使用して、サービス エンドポイントを呼び出すためのサービス URL とその他の詳細を取得します。 

```
az ml service usage realtime -i <web service id>
```

このコマンドは、サービス API スキーマがデプロイ時に指定された場合、サービスを呼び出すためのサービス URL、必要な要求ヘッダー、Swagger URL、およびサンプル データを出力します。

HTTP 要求を構成せずに、CLI から直接サービスをテストできます。そのためには、次のように、サンプル CLI コマンドと共に入力データを入力します。

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>サービス API キーの取得
Web サービス キーを取得するには、次のコマンドを使用します。

```
az ml service keys realtime -i <web service id>
```
HTTP 要求を作成するときは、Authorization ヘッダーにキーを使用します: "Authorization": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>サービスの Swagger 説明の取得
サービス API のスキーマを指定した場合、サービス エンドポイントにより ```http://<ip>/api/v1/service/<service name>/swagger.json``` に Swagger ドキュメントが公開されます。 Swagger ドキュメントを使用して、サービス クライアントを自動的に生成したり、予測される入力データやサービスに関するその他の詳細を調査したりできます。

## <a name="get-service-logs"></a>サービス ログの取得
サービスの動作を理解し、問題を診断するために、サービス ログを取得するための方法がいくつかあります。
- CLI コマンド ```az ml service logs realtime -i <service id>```。 このコマンドは、クラスターおよびローカル モードの両方で機能します。
- サービスのログ記録をデプロイで有効化した場合は、サービス ログは AppInsight にも送信されます。 CLI コマンド ```az ml service usage realtime -i <service id>``` は、AppInsight URL を表示します。 AppInsight ログは、2 ～ 5 分遅延する場合があります。
- クラスター ログを表示するには、```az ml env set``` を使用して現在のクラスター環境を設定した場合に接続される Kubernetes コンソールを使用します。
- ローカル Docker ログを確認するには、サービスがローカルで実行されているときに Docker エンジンのログを使用します。

## <a name="call-the-service-using-c"></a>C# を使用したサービスの呼び出し
サービス URL を使用して、C# コンソール アプリから要求を送信します。 

1. Visual Studio で、新しいコンソール アプリを作成します。 
    * メニューで、[ファイル]、[新規作成]、[プロジェクト] の順にクリックします。
    * Visual Studio C# で、[Windows Class Desktop]\(ウィンドウ クラス デスクトップ\) をクリックし、[コンソール アプリ] を選択します。
2. プロジェクトの名前として「`MyFirstService`」と入力し、[OK] をクリックします。
3. [プロジェクトの参照] で、参照を `System.Net` と `System.Net.Http` に設定します。
4. [ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール] の順にクリックし、**Microsoft.AspNet.WebApi.Client** パッケージをインストールします。
5. **Program.cs** ファイルを開き、コードを次のコードに置き換えます。
6. `SERVICE_URL` パラメーターと `API_KEY` パラメーターを Web サービスからの情報に変更します。
7. プロジェクトを実行します。

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Python を使用して Web サービスを呼び出す
Python を使用して、要求をリアルタイムの Web サービスに送信します。 

1. 次のコード サンプルを新しい Python ファイルにコピーします。
2. データ、url、および api_key パラメーターを更新します。 ローカル Web サービスの場合は、"Authorization" ヘッダーを削除します。
3. コードを実行します。 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
