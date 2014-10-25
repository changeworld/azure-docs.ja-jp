<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="s****danie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API Management で API Inspector を使用して呼び出しをトレースする方法

API Management (プレビュー) には、API のデバッグとトラブルシューティングに役立つ API Inspector ツールが用意されています。API Inspector は、アプリケーションからプログラムで使用することも、開発者ポータルから直接使用することもできます。このガイドでは、API Inspector の使い方を順をおって説明していきます。

## このトピックの内容

-   [API Inspector を使用した呼び出しのトレース][API Inspector を使用した呼び出しのトレース]
-   [トレースの確認][トレースの確認]
-   [次のステップ][次のステップ]

## <a name="trace-call"> </a>API Inspector を使用した呼び出しのトレース

API Inspector を使用するには、**ocp-apim-trace: true** 要求ヘッダーを操作の呼び出しに追加します。その後、**ocp-apim-trace-location** 応答ヘッダーで示される URL を使用してトレースをダウンロードし、内容を確認します。この操作は、プログラムで実行することも、開発者ポータルから直接実行することもできます。

このチュートリアルでは、API Inspector を使用して、Echo API を使用する操作をトレースする方法について説明します。

> それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。Echo API は、受け取った入力をそのまま返します。これを使用するには、任意の HTTP 動詞を呼び出します。すると、送った値がそのまま返されます。

最初に、ご利用の API Management サービスの Azure ポータルで **[開発ポータル]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management developer portal][API Management developer portal]

開発者ポータルには、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。このチュートリアルでは、**Echo API** の **Get Resource** メソッドを呼び出します。

上部のメニューの **[API]** をクリックし、**[Echo API]** をクリックします。

![Echo API](./media/api-management-howto-api-inspector/api-management-echo-api.png)

> アカウントに対して構成されている (またはアカウントから見える) API が 1 つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。

**[GET Resource]** 操作を選択し、**[コンソールを開く]** をクリックします。

![Open console][Open console]

既定のパラメーターの値はそのままにし、**[サブスクリプション キー]** ボックスの一覧でサブスクリプション キーを選択します。

**[要求ヘッダー]** ボックスに「**ocp-apim-trace: true**」と入力し、**[HTTP Get]** をクリックします。

![HTTP Get](./media/api-management-howto-api-inspector/api-management-http-get.png)

応答ヘッダーは、次の例のような値を含む **ocp-apim-trace-location** となります。

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

トレースは、次のステップに示すように、指定された場所からダウンロードして内容を確認できます。

## <a name="inspect-trace"> </a>トレースの確認

トレース内の値を確認するには、**ocp-apim-trace-location** URL からトレース ファイルをダウンロードします。これは JSON 形式のテキスト ファイルで、次の例に示すようなエントリを含んでいます。

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>次のステップ

-   「[Azure API Management の詳細な構成について][Azure API Management の詳細な構成について]」チュートリアルにあるその他のトピックもチェックしてください。

  [API Inspector を使用した呼び出しのトレース]: #trace-call
  [トレースの確認]: #inspect-trace
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management developer portal]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Open console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [Azure API Management の詳細な構成について]: ../api-management-get-started-advanced
