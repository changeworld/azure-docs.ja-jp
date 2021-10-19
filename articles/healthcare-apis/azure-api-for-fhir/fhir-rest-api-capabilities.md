---
title: FHIR 用 Azure API 用の FHIR Rest API 機能
description: この記事では、Azure API for FHIR の RESTful 相互作用と機能について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: c69c3838693c3e59aa7a024c0a67c28c6f1a5d21
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187728"
---
# <a name="fhir-rest-api-capabilities-for-azure-api-for-fhir"></a>FHIR 用 Azure API 用の FHIR Rest API 機能

この記事では、Azure API for FHIR の RESTful な相互作用について、いくつかの微妙な違いについて説明します。


## <a name="conditional-createupdate"></a>条件付き作成/更新

Azure API for FHIR では、FHIR 仕様で定義されているように、作成、条件付き作成、更新、条件付き更新をサポートしています。 これらのシナリオで役に立つヘッダーの1つは、 [if-match](https://www.hl7.org/fhir/http.html#concurrency) ヘッダーです。 `If-Match`ヘッダーが使用され、更新を行う前に更新されるバージョンが検証されます。 が予期されると一致しない場合は、 `ETag` `ETag` エラーメッセージ *412 の前提条件に失敗* したことが生成されます。 

## <a name="delete"></a>削除

FHIR 仕様によって定義された[削除](https://www.hl7.org/fhir/http.html#delete)では、削除後に、後続の非バージョン固有のリソースの読み取りが 410 HTTP 状態コードを返し、検索によってリソースが見つからなくなっている必要があります。 さらに、Azure API for FHIR を使用すると、リソースを完全に削除できます (すべての履歴を含む)。 リソースを完全に削除するには、パラメーター設定 `hardDelete` を true (`DELETE {server}/{resource}/{id}?hardDelete=true`) にします。 このパラメーターを渡していない場合、または false に設定した場合 `hardDelete` 、リソースの履歴バージョンは引き続き使用できます。

> [!NOTE]
> 履歴のみを削除する場合、Azure API for FHIR はカスタム操作をサポートしてい `$purge-history` ます。これにより、リソースから履歴を削除できます。 

## <a name="conditional-delete"></a>条件付き削除

Delete に加えて、Azure API for FHIR は条件付き削除をサポートしています。これにより、リソースを削除するための検索条件を渡すことができます。 既定では、条件付き削除を使用すると、一度に1つの項目を削除できます。 パラメーターを指定して `_count` 、最大100個の項目を一度に削除することもできます。 条件付き削除の使用例を次に示します。

条件付き削除を使用して1つの項目を削除するには、1つの項目を返す検索条件を指定する必要があります。

DELETE `https://{{hostname}}/Patient?identifier=1032704`

同じ検索を実行できますが、すべての履歴も削除するには、"ハード削除 = true" を含める必要があります。

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

複数のリソースを削除する場合は、を含めることができます `_count=100` 。これにより、検索条件に一致する最大100のリソースが削除されます。

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>Patch および条件付きパッチ

Patch は、FHIR リソースの一部のみを更新する必要がある場合に役立つ RESTful 操作です。 Patch を使用すると、レコード全体を更新しなくてもリソース内で更新する要素を指定できます。 FHIR では、FHIR のリソースを修正するための3種類の方法が定義されています: JSON Patch、XML Patch、および FHIR Path パッチ。 FHIR サービスは、JSON 修正プログラムと条件付き JSON 修正プログラムをサポートしています (これにより、ID ではなく検索条件に基づいてリソースに修正プログラムを適用できます)。 JSON 修正プログラムの使用例については、REST のサンプル [ファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http)を参照してください。

> [!NOTE]
> STU3 に対してを使用し、 `PATCH` 履歴バンドルを要求する場合は、修正されたリソースの `Bundle.entry.request.method` がにマップされ `PUT` ます。 これは、STU3 に `PATCH` [httpverb 値セット](http://hl7.org/fhir/STU3/valueset-http-verb.html)内の動詞の定義が含まれていないためです。

### <a name="testing-patch"></a>テスト修正プログラム

Patch には、修正プログラムを実行する前に条件が true であることを検証するためのテスト操作があります。 たとえば、患者の死亡を設定する場合は、死亡としてマークされていない場合にのみ、次の例を使用できます。 

修正プログラム `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` のコンテンツの種類: `application/json-patch+json`

```
[
    {
        “op”: “test”,
        “path”: “/deceasedBoolean”,
        “value”: false
    },
    {
        “op”: “replace”
        “path”: “/deceasedBoolean”,
        “value”: true
    }
]

```

### <a name="patch-in-bundles"></a>バンドルのパッチ

既定では、バンドルリソースでは JSON パッチはサポートされていません。 これは、バンドルがサポートしているのは FHIR リソースだけで、JSON パッチは FHIR リソースではないためです。 この問題を回避するには、 `"application/json-patch+json"` バンドルの実行時に JSON 文字列の base64 エンコードとしてのコンテンツタイプのバイナリリソースを扱います。 この回避策の詳細については、 [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)にログインしてください。 

次の例では、患者の性別を女性に変更します。 JSON 修正プログラムを取得 `[{"op":"replace","path":"/gender","value":"female"}]` し、base64 にエンコードしました。

投稿 `https://{FHIR-SERVICE-NAME}/` の内容-種類: `application/json`

```
{
    “resourceType”: “Bundle”
    “id”: “bundle-batch”,
    “type”: “batch”
    “entry”: [
        {
            “fullUrl”: “Patient/{PatientID}”,
            “resource”: {
                “resourceType”: “Binary”,
                “contentType”: “application/json-patch+json”,
                “data”: "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            “request”: { 
                “method”: “PATCH”,
                “url”: “Patient/{PatientID}”
            }
        }
    ]
}

```

## <a name="next-steps"></a>次の手順

この記事では、Azure API for FHIR の REST 機能の一部について説明しました。 次に、FHIR でリソースを検索するための主要な側面について学習します。 

>[!div class="nextstepaction"]
>[Azure API for FHIR での検索の概要](overview-of-search.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。