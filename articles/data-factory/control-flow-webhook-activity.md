---
title: Azure Data Factory の Webhook アクティビティ
description: Webhook アクティビティは、ユーザーが指定する特定の条件でアタッチされたデータセットを検証するまで、パイプラインの実行を継続しません。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 70c67a99274eaedc5592c7b90b1ef80a3a17acf8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110007"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory の Webhook アクティビティ
Webhook アクティビティを使用すると、カスタム コードでパイプラインの実行を制御できます。 Webhook アクティビティを使用すると、顧客は、エンドポイントを呼び出し、コールバック URL を渡ことができます。 パイプラインの実行は、コールバックが呼び出されるのを待ってから、次のアクティビティに進みます。

## <a name="syntax"></a>構文

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>型のプロパティ



プロパティ | 説明 | 使用できる値 | Required
-------- | ----------- | -------------- | --------
name | Webhook アクティビティの名前 | String | はい |
type | **WebHook** に設定する必要があります。 | String | はい |
method | ターゲット エンドポイント用の Rest API メソッド。 | 文字列 をオンにします。 サポートされている型'POST' | はい |
url | ターゲット エンドポイントおよびパス | 文字列 (または文字列の resultType を含む式)。 | はい |
headers | 要求に送信されるヘッダー。 たとえば、要求に種類と言語を設定する場合: "headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }。 | 文字列 (または文字列の resultType を含む式) | あり。Content-type ヘッダーが必要です。 "headers":{ "Content-Type":"application/json"} |
body | エンドポイントに送信されるペイロードを表します。 | 有効な JSON (または JSON の resultType を含む式)。 「[要求ペイロードのスキーマ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)」セクションにある要求ペイロードのスキーマを参照してください。 | はい |
認証 | エンドポイントを呼び出すために使用される認証方法。 サポートされるタイプは "Basic" または "ClientCertificate" です。 詳細については、「[認証](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)」セクションを参照してください。 認証が必要ない場合は、このプロパティを除外します。 | 文字列 (または文字列の resultType を含む式) | いいえ |
timeout | &#39;callBackUri&#39; が呼び出されるまでのアクティビティの待機時間。 "callBackUri" が呼び出されるまでのアクティビティの待機時間。 既定値は 10 分 (“00:10:00”) です。 形式は期間 (つまり、d.hh:mm:ss) です | String | いいえ |
[Report status on callback] (コールバックで状態を報告する) | アクティビティを "失敗" とマークする Webhook アクティビティの失敗状態をユーザーが報告できるようにします。 | Boolean | いいえ |

## <a name="authentication"></a>認証

Webhook アクティビティでサポートされている認証の種類を以下に示します。

### <a name="none"></a>なし

認証が必要ない場合は、"authentication" プロパティを含めないでください。

### <a name="basic"></a>Basic

基本認証で使用するユーザー名とパスワードを指定します。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>クライアント証明書

PFX ファイルの Base64 でエンコードされたコンテンツとパスワードを指定します。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>マネージド ID

データ ファクトリのマネージド ID を使用してアクセス トークンの要求対象となるリソース URI を指定します。 Azure Resource Management API を呼び出すには、`https://management.azure.com/` を使用します。 マネージド ID が機能する方法について詳しくは、[Azure リソースのマネージド ID の概要](/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> ご利用のデータ ファクトリが git リポジトリを使用して構成されている場合、基本認証またはクライアント証明書認証を使用するには、ご自分の資格情報を Azure Key Vault に格納する必要があります。 Azure Data Factory では、git にパスワードは保存されません。

## <a name="additional-notes"></a>その他のメモ

Azure Data Factory では、本文中にある追加のプロパティ "callBackUri" を url エンドポイントに渡されます。また、この uri が指定されたタイムアウト値の前に呼び出されることが期待されます。 この uri が呼び出されない場合、アクティビティは状態 'TimedOut' で失敗します。

Webhook アクティビティ自体が失敗するのは、カスタム エンドポイントへの呼び出しが失敗した場合です。 すべてのエラー メッセージをコールバックの本文に追加して、後続のアクティビティで使用することができます。

コールバック URI に戻される本文は、有効な JSON である必要があります。 Content-Type ヘッダーを `application/json` に設定する必要があります。

[Report status on callback] (コールバックで状態を報告する) オプションを使用する場合は、コールバックを作成するときに本文に次のスニペットを追加する必要があります。

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>次のステップ

Data Factory でサポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
