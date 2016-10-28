<properties
    pageTitle="Logic Apps のログ記録とエラー処理 | Microsoft Azure"
    description="Logic Apps を使用した高度なエラー処理とログ記録について実際の使用例を紹介しています。"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager=""
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# Logic Apps のログ記録とエラー処理

この記事では、ロジック アプリを拡張して例外処理への対応を強化する方法について説明します。Logic Apps における例外処理とエラー処理への対応状況を実際的な見地から明らかにしていきます。

>[AZURE.NOTE] Microsoft Azure App Service の現行バージョンの Logic Apps 機能には、アクションに対する応答の標準テンプレートが用意されています。これには、内部的な検証と、API アプリから返されるエラー応答の両方が含まれます。

## ユース ケースとシナリオの概要

この記事で扱うユース ケースは次のとおりです。以前ある有名な医療機関から、Azure ソリューションの開発を依頼されたことがあります。Microsoft Dynamics CRM Online を使って患者ポータルを作成することが目的です。Dynamics CRM Online の患者ポータルと Salesforce の間で予約レコードを送信する必要がありました。すべての患者レコードに [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) 標準を使うよう依頼されました。

このプロジェクトには、主に 2 つの要件がありました。

 -  Dynamics CRM Online ポータルから送信されたレコードを記録するメソッド
 -  ワークフロー内で発生したエラーを確認する手段


## 問題の解決方法

>[AZURE.TIP] [Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group") のサイトでプロジェクトの概要ビデオをご覧いただけます。

ここでは、[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") をログとエラーのレコードを格納するリポジトリとして選びました (DocumentDB では、レコードはドキュメントと呼ばれます)。Logic Apps にはあらゆる応答の標準テンプレートが用意されています。そのためカスタム スキーマを作成する必要はないだろうと考えました。場合によっては、エラー レコードとログ レコードの**挿入**と**クエリ**を行う API アプリを作成することもできます。また、それぞれのスキーマを API アプリ内で定義してもかまいません。

もう 1 つの要件は、特定の日付を越えたらレコードを消去するというものでした。DocumentDB には [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "有効期限") (TTL) というプロパティがあり、レコードごと、またはコレクションに対して **Time to Live** 値を設定することができます。これにより、DocumentDB から手動でレコードを削除する手間が省かれました。

### ロジック アプリの作成

最初に行うことは、ロジック アプリを作成してデザイナーに読み込むことです。この例では、親子のロジック アプリを使用しています。親の方は、既に作成済みであると仮定して、子のロジック アプリを 1 つ作成します。

Dynamics CRM Online から送信されたレコードのログを記録することになります。最初から順に見ていきましょう。子のロジック アプリは親のロジック アプリによってトリガーされるので、要求トリガーを使用する必要があります。

> [AZURE.IMPORTANT] このチュートリアルの作業を行うためには、DocumentDB データベースと 2 つのコレクション (ログとエラー) を作成する必要があります。

### ロジック アプリのトリガー

要求トリガーを次の例のように使用します。

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### 手順

Dynamics CRM Online ポータルから送信された患者レコードのソース (要求) を記録する必要があります。

1. まず Dynamics CRM Online から新しい予約レコードを取得する必要があります。CRM から取得したトリガーによって、**CRM 患者 ID**、**レコード タイプ**、**更新/新規** (新しいレコードか更新されたレコードかを表すブール値)、**Salesforce ID** が得られます。**Salesforce ID** は更新時にのみ使用されるので、null の場合もあります。CRM レコードは、CRM **患者 ID** と**レコードの種類**を使用して取得します。
1. 次に、DocumentDB API アプリの **InsertLogEntry** 操作を追加する必要があります (下図)。


#### ログ エントリ挿入のデザイナー ビュー

![Insert Log Entry](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### エラー エントリ挿入のデザイナー ビュー
![Insert Log Entry](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### レコード作成エラーのチェック

![条件](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## ロジック アプリのソース コード

>[AZURE.NOTE]  以降に示したコードは、あくまでサンプルです。このチュートリアルは、現在運用段階の実装がベースになっているため、**Source Node** の値に、予約のスケジューリングに関連するプロパティが表示されない場合があります。

### ログの記録
以下に示したのは、ログ処理の方法を示すロジック アプリのコード サンプルです。

#### ログ エントリ
これはログ エントリを挿入するための、ロジック アプリのソース コードです。

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### ログ要求

これは、API アプリにポストされるログ要求メッセージです。

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }

```


#### ログ応答

これは、API アプリからのログ応答メッセージです。

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}

```

それでは、エラー処理の手順を見ていきましょう。


### エラー処理

以下の Logic Apps のコード サンプルは、エラー処理を実装する方法を示しています。

#### エラー レコードの作成

これはエラー レコードを作成するための、Logic Apps のソース コードです。

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### DocumentDB へのエラーの挿入 (要求)

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### DocumentDB へのエラーの挿入 (応答)


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Salesforce のエラー応答

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### 親ロジック アプリに応答を返す

応答を受け取ったら、それを親ロジック アプリに渡します。

#### 親ロジック アプリに成功応答を返す

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### 親ロジック アプリにエラー応答を返す

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## DocumentDB リポジトリとポータル

ソリューションの機能は、[DocumentDB](https://azure.microsoft.com/services/documentdb) を使って拡張されています。

### エラー管理ポータル

エラーを表示するには、DocumentDB からエラー レコードを取得して表示する MVC Web アプリを作成します。現在のバージョンでは、**一覧表示**、**詳細表示**、**編集**、**削除**の各操作が含まれます。

> [AZURE.NOTE] 編集操作について: DocumentDB では、ドキュメント全体の置き換えが実行されます。**一覧表示**と**詳細表示**に示したレコードは、あくまでサンプルです。実際の患者予約レコードではありません。

これまでに説明した方法で作成した MVC アプリのサンプルの詳細を以下に示します。

#### エラー管理一覧

![エラー一覧](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### エラー管理の詳細表示

![Error Details](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### ログの管理ポータル

ログを表示するために、MVC Web アプリも作成しました。これまでに説明した方法で作成した MVC アプリのサンプルの詳細を以下に示します。

#### ログ詳細表示サンプル

![Log Detail View](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### API アプリの詳細

#### Logic Apps 例外管理 API

Microsoft がソース コードを公開している、Logic Apps 例外管理 API アプリには、次の機能が備わっています。

コントローラーは 2 つ存在します。

- **ErrorController** は、DocumentDB コレクションにエラー レコード (ドキュメント) を挿入します。
- **LogController** は、DocumentDB コレクションにログ レコード (ドキュメント) を挿入します。

> [AZURE.TIP] どちらのコントローラーも `async Task<dynamic>` 操作を使用しています。操作を実行時に解決できるので、DocumentDB スキーマを操作の本体に作成することができます。

DocumentDB 内の各ドキュメントには、一意の ID が割り当てられている必要があります。ここでは `PatientId` を使用し、Unix のタイムスタンプ値 (double) に変換したタイムスタンプを追加しています。小数桁を除外するために切り詰め処理を行っています。

エラー コントローラー API のソース コードは、[GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs) からご覧いただけます。

この API は、ロジック アプリから次の構文を使用して呼び出します。

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

前のコード サンプルの式は、*Create\_NewPatientRecord* ステータスが **Failed** であるかどうかをチェックしています。

## 概要

- ログ処理とエラー処理は、ロジック アプリで簡単に実装できます。
- ログ レコードとエラー レコード (ドキュメント) のリポジトリとしては、DocumentDB を使用できます。
- ログ レコードとエラー レコードを表示するためのポータルは MVC を使用して作成できます。

### ソース コード
Logic Apps 例外管理 API アプリケーションのソース コードは、こちらの [GitHub リポジトリ](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logic Apps 例外管理 API")からご覧いただけます。


## 次のステップ
- [さらに他の Logic Apps の例とシナリオを見る](app-service-logic-examples-and-scenarios.md)
- [Logic Apps の監視ツールについて学習する](app-service-logic-monitor-your-logic-apps.md)
- [ロジック アプリの自動デプロイ テンプレートを作成する](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0817_2016-->