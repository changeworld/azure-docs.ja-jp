---
title: August-1-2015 プレビューのスキーマの更新 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps でロジック アプリ定義のために更新されたスキーマ バージョン 2015-08-01-preview
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: stepsic-microsoft-com
ms.author: stepsic
ms.reviewer: klam, estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: dd05543c2a727f010432ecb54c2dc3e77a245de4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122779"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Azure Logic Apps のスキーマの更新 - August 1, 2015 preview

Azure Logic Apps のこのバージョンのスキーマと API には、ロジック アプリの信頼性と使いやすさを向上させる複数の機能強化が行われています:

* **APIApp** というアクションの種類の名前が [**APIConnection**](#api-connections) になりました。
* **Repeat** アクションの名前が [**Foreach**](#foreach) になりました。
* [**HTTP リスナー** API アプリ](#http-listener)が不要になりました。
* 子ワークフローの呼び出しに[新しいスキーマ](#child-workflows)を使用します。

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>API 接続への移行

最大の変更は、API を使用するために、Azure サブスクリプションに API アプリをデプロイする必要がなくなった点です。 次の方法で API を使用することができます。

* マネージド API
* カスタム Web API

これらの API は、管理モデルとホスティング モデルが異なることから、それぞれの処理方法も若干異なります。 このモデルのメリットの 1 つは、Azure リソース グループにデプロイされたリソース以外にも、リソースを使用できるようになったことです。 

### <a name="managed-apis"></a>マネージド API

Microsoft では、Office 365、Salesforce、Twitter、FTP などの API の一部を代わりに管理しています。 このようなマネージド API には、Bing 翻訳のようにそのまま使用できる API もあれば、*接続*とも呼ばれる構成が必要な API もあります。

たとえば、Office 365 を使用するときは、Office 365 のサインイン トークンを含む接続を作成する必要があります。 トークンは、ロジック アプリでいつでも Office 365 API を呼び出すことができるように、セキュリティで保護された状態で保存と更新が行われます。 SQL サーバーや FTP サーバーに接続する場合は、接続文字列を含む接続を作成する必要があります。 

これらのアクションは、定義では `APIConnection` と呼ばれます。 電子メールを送信するために Office 365 を呼び出す接続の例を次に示します。

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

`host` オブジェクトは、API 接続に固有の入力の一部であり、`api` と `connection` のパートが含まれています。 `api` オブジェクトは、マネージド API がホストされる場所のランタイム URL を指定します。 `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` を呼び出すと、利用可能なマネージド API すべてを表示することができます。

API を使用する場合、その API には*接続パラメーター*が定義されている場合と定義されていない場合があります。 したがって、API でこれらのパラメーターを定義しない場合、接続は必要ありません。 API でこれらのパラメーターを定義する場合、指定された名前で接続を作成する必要があります。  
そして、`host` オブジェクト内の `connection` オブジェクトでその名前を参照することになります。 リソース グループ内に接続を作成するには、このメソッドを呼び出します。

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

さらに以下の本体を追加します。

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでマネージド API をデプロイする

作成するアプリに対話型サインインが必要ない場合、そのアプリ全体を Azure Resource Manager テンプレートで作成できます。
対話型サインインが必要な場合は、Azure Resource Manager テンプレートですべて設定できますが、さらに Azure Portal にアクセスして接続を承認する必要があります。 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

この例では、接続がリソース グループ内に存在する単なるリソースであることがわかります。 これらの接続は、ユーザーのサブスクリプション内で利用可能なマネージド API を参照しています。

### <a name="your-custom-web-apis"></a>カスタム Web API

独自の API (Microsoft によって管理されていない API) を使用している場合、その呼び出しには、組み込みの **HTTP** アクションを使用します。 最適なユーザー エクスペリエンスを確保するためには、API の Swagger エンドポイントを公開する必要があります。 これにより、ロジック アプリ デザイナーで API の入出力を表示できるようになります。 Swagger がないと、デザイナーでは入出力を不透明な JSON オブジェクトとしてしか表示できません。

新しい `metadata.apiDefinitionUrl` プロパティを次の例に示します。

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Web API を Azure App Service でホストした場合、デザイナーで利用可能なアクションの一覧にその Web API が自動的に表示されます。 ホストしていない場合は、URL 内に直接貼り付ける必要があります。 Swagger エンドポイントは、ロジック アプリ デザイナー内で使用できるように、認証しない必要があります (ただし、Swagger でサポートされているいずれかの方法で API 自体をセキュリティで保護することはできます)。

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>既にデプロイ済みの API アプリを 2015-08-01-preview で呼び出す

API アプリを事前にデプロイしてある場合、**HTTP** アクションによってそのアプリを呼び出すことができます。
たとえば、Dropbox を使用してファイルを一覧表示する場合、**2014-12-01-preview** のスキーマ バージョン定義の内容は次のようになっていることがあります。

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

ここで、同等の HTTP アクションを次の例のように作成できます (ロジック アプリ定義の parameters セクションは未変更のままです):

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

これらのプロパティを 1 つずつ説明します。

| アクション プロパティ | 説明 |
| --- | --- |
| `type` | `APIapp` の代わりに `Http` を使用します |
| `metadata.apiDefinitionUrl` | ロジック アプリ デザイナーでこのアクションを使用する場合は、次の内容で構成されたメタデータ エンドポイントを含める必要があります。`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | 次の内容で構成されています。`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 常に `POST` |
| `inputs.body` | API アプリのパラメーターと同じです |
| `inputs.authentication` | API アプリの認証と同じです |

この方法は、API アプリのアクションすべてに対して有効です。 ただし、こうした以前の API Apps は今後サポートされないことに注意してください。 したがって冒頭に挙げた他の 2 つの方法、つまりマネージド API を使用する方法とカスタム Web API をホストする方法のいずれかに移行する必要があります。

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>'repeat' から 'foreach' への名前変更

以前のスキーマ バージョンについては **Repeat** というアクション名が理解しづらく、また **Repeat** は実際には for-each ループであることを正しく把握できないというフィードバックがお客様から数多く寄せられました。 そのため、`repeat` は `foreach` に変更されました。 以前は、このアクションを次の例のように記述していました。

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

代わりに、このバージョンを記述するようになりました。

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

また、現在のイテレーションの間にループが処理している項目を参照していた `repeatItem()` 関数の名前が `item()` に変更されました。 

### <a name="reference-outputs-from-foreach"></a>'foreach' からの出力の参照

単純化するために、`foreach` アクションからの出力が `repeatItems` というオブジェクトにラップされなくなりました。 また、このような変更により、`repeatItem()`、`repeatBody()`、`repeatOutputs()` 関数が削除されています。

したがって、以前の `repeat` の例を使用すると、次の出力が得られます。

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

代わりに、次の出力が得られるようになりました。

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

以前は、これらの出力を参照するとき、アクションから `body` を取得するには、次のようにしていました。

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

代わりに、このバージョンを使用できるようになりました。

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>ネイティブ HTTP リスナー

HTTP リスナーの機能が新たに組み込まれました。 したがって今後は、HTTP リスナーの API アプリをデプロイする必要はありません。 ロジック アプリのエンドポイントを呼び出し可能にする方法の詳細については、[こちら](../logic-apps/logic-apps-http-endpoint.md)を参照してください。 

これらの変更を踏まえて `@accessKeys()` 関数は削除され、必要なときにエンドポイントを取得するための `@listCallbackURL()` 関数に置き換えられました。 また、ロジック アプリには、少なくとも 1 つのトリガーを定義する必要があります。 ワークフローに対して `/run` を実行する場合は、`manual`、`apiConnectionWebhook`、`httpWebhook` のいずれかのトリガーが必要になります。

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>子ワークフローの呼び出し

これまで、子ワークフローを呼び出すには、そのワークフローでアクセス トークンを取得し、呼び出し元とするロジック アプリの定義内にそのトークンを貼り付ける必要がありました。 新しいスキーマを使用すると、Logic Apps エンジンにより、子ワークフローに対して実行時に SAS が自動生成されます。つまり、定義内にシークレットを貼り付ける必要はありません。 たとえば次のようになります。

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

2 つ目の改善点は、受信要求へのフル アクセスが子ワークフローに付与されるようになったことです。 つまり、*queries* セクションと *headers* オブジェクトでパラメーターを渡すことができ、本体全体を自由に定義できるということになります。

最後に、子ワークフローに対して必要な変更があります。 これまでは子ワークフローを直接呼び出すことができましたが、これからは親から呼び出すためにワークフロー内にトリガー エンドポイントを定義する必要があります。 つまり、通常は `manual` 型のトリガーを追加して、それを親の定義内で使用することになります。 `host` プロパティには特別に `triggerName` が含まれていることに注意してください。これは、どのトリガーを呼び出すかを常に指定する必要があるためです。

## <a name="other-changes"></a>その他の変更点

### <a name="new-queries-property"></a>新しい 'queries' プロパティ

現時点では、すべての種類のアクションで `queries` という新しい入力がサポートされています。 この入力は構造化オブジェクトにすることが可能で、文字列を手動で組み合わせる必要がありません。

### <a name="renamed-parse-function-to-json"></a>'parse()' 関数の名前を 'json()' に変更

近日中にコンテンツの種類を追加するため、`parse()` 関数の名前を `json()` に変更しました。

## <a name="coming-soon-enterprise-integration-apis"></a>近日提供予定: Enterprise Integration API

AS2 のようなマネージド バージョンの Enterprise Integration API はまだありません。 その間は、既にデプロイされている BizTalk API を HTTP アクションで使用することができます。 詳しくは、[統合ロードマップ](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)の「Using your already deployed API apps (既にデプロイされている API アプリ)」をご覧ください。 
