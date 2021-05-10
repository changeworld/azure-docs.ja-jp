---
title: ドキュメント翻訳のバッチ要求の送信
titleSuffix: Azure Cognitive Services
description: ドキュメント翻訳要求を Document Translation サービスに送信します。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613777"
---
# <a name="document-translation-submit-batch-request"></a>ドキュメント翻訳: バッチ要求の送信

この API を使用して、一括 (バッチ) 翻訳要求を Document Translation サービスに送信します。 各要求には複数のドキュメントを含めることができ、各ドキュメントのソースと宛先のコンテナーが含まれている必要があります。

フォルダーをフィルター処理するには、プレフィックスとサフィックスのフィルター (指定された場合) を使用します。 プレフィックスは、コンテナー名の後のサブパスに適用されます。

用語集および翻訳メモリは、要求に含めることができ、ドキュメントが翻訳されるときにサービスによって適用されます。

翻訳中に用語集が無効であったり、到達できなかったりした場合は、ドキュメントの状態にエラーが表示されます。 同じ名前のファイルが宛先に既に存在する場合は、上書きされます。 各ターゲット言語の targetUrl は一意でなければなりません。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

[カスタム ドメイン名](../get-started-with-document-translation.md#find-your-custom-domain-name)を見つける方法について確認してください。

> [!IMPORTANT]
>
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページのエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはできません。

## <a name="request-headers"></a>要求ヘッダー

要求ヘッダーを次に示します。

|ヘッダー|説明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必要な要求ヘッダー|

## <a name="request-body-batch-submission-request"></a>要求本文: バッチ送信要求

|名前|Type|説明|
|--- |--- |--- |
|inputs|BatchRequest[]|下に示される BatchRequest。 ドキュメントまたはドキュメントを含むフォルダーの入力リストです。 メディアの種類: "application/json"、"text/json"、"application/*+json"。|

### <a name="inputs"></a>入力

入力バッチの翻訳要求に関する定義。

|名前|Type|必須|説明|
|--- |--- |--- |--- |
|source|SourceInput[]|はい|下に示される inputs.source。 入力ドキュメントのソースです。|
|storageType|StorageInputType[]|はい|下に示される inputs.storageType。 入力ドキュメントのソース文字列のストレージ型です。|
|ターゲット|TargetInput[]|はい|下に示される inputs.target。 出力先の場所です。|

**inputs.source**

入力ドキュメントのソースです。

|名前|Type|必須|説明|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|いいえ|下に示される DocumentFilter[]。|
|filter.prefix|string|いいえ|翻訳対象のソースパス内のドキュメントをフィルター処理するための、大文字と小文字を区別するプレフィックス文字列。 たとえば、Azure ストレージ BLOB の URI を使用する場合は、プレフィックスを使用して、翻訳対象のサブフォルダーを制限します。|
|filter.suffix|string|いいえ|翻訳対象のソースパス内のドキュメントをフィルター処理するための、大文字と小文字を区別するサフィックス文字列。 これは、ファイル拡張子に対して最もよく使用されます。|
|language|string|いいえ|言語コードが指定されていない場合は、ドキュメントに対して自動検出を実行します。|
|sourceUrl|string|True|ドキュメントを含むフォルダーまたはコンテナーあるいは単一ファイルの場所。|
|storageSource|StorageSource|いいえ|下に示される StorageSource。|
|storageSource.AzureBlob|string|いいえ||

**inputs.storageType**

入力ドキュメントのソース文字列のストレージ型です。

|名前|Type|
|--- |--- |
|file|string|
|folder|string|

**inputs.target**

完成した翻訳済みドキュメントの宛先。

|名前|Type|必須|説明|
|--- |--- |--- |--- |
|category|string|いいえ|翻訳要求のカテゴリまたはカスタム システム。|
|用語集|Glossary[]|いいえ|下に示される用語集。 用語集の一覧。|
|glossaries.format|string|いいえ|フォーマットします。|
|glossaries.glossaryUrl|string|True (用語集を使用する場合)|用語集の場所。 format パラメーターが指定されていない場合は、ファイル拡張子を使用して書式設定を抽出します。 翻訳言語のペアが用語集に存在しない場合は、適用されません。|
|glossaries.storageSource|StorageSource|いいえ|上に示される StorageSource。|
|targetUrl|string|True|ドキュメントを含むフォルダーまたはコンテナーの場所。|
|language|string|True|2 文字のターゲット言語コード。 [言語コードの一覧](../../language-support.md)に関するページを参照してください。|
|storageSource|StorageSource []|いいえ|上に示される StorageSource []。|
|version|string|いいえ|バージョン。|

## <a name="example-request"></a>要求の例

バッチ要求の例を下に示します。

**コンテナー内のすべてのドキュメントを翻訳する**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**コンテナー内のすべてのドキュメントを翻訳して用語集を適用する**

(コンテナーではなく) 特定の BLOB またはドキュメントに対して、用語集 URL と SAS トークンが作成されていることを確認してください

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**コンテナー内の特定のフォルダーを翻訳する**

フィルターでプレフィックスとしてフォルダー名 (大文字と小文字を区別) を指定していることを確認してください (ただし、SAS トークンはコンテナー用のままです)。

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**コンテナー内の特定のドキュメントを翻訳する**

* "storageType": "File" が指定されていることを確認してください
* (コンテナーではなく) 特定の BLOB またはドキュメントに対して、ソース URL と SAS トークンが作成されていることを確認してください
* ターゲット URL の一部としてターゲット ファイル名が指定されていることを確認してください (ただし、SAS トークンはコンテナー用のままです)。
* 下のサンプル要求では、1 つのドキュメントが 2 つのターゲット言語に翻訳されています

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

|状態コード|説明|
|--- |--- |
|202|受理されました。 要求が成功し、サービスによってバッチ要求が作成されます。 ヘッダー Operation-Location は、ID.HeadersOperation-Location: string という操作の状態 URL を示します|
|400|正しくない要求。 無効な要求です。 入力パラメーターを確認してください。|
|401|権限がありません。 資格情報を確認してください。|
|429|要求レートが高すぎます。|
|500|内部サーバー エラー。|
|503|現在サービスが使用できません。  後で再度お試しください。|
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません</li></ul>|

## <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
|code|string|高レベルのエラー コードを含む列挙型。 指定できる値<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|innerError|InnerErrorV2|Cognitive Services API のガイドラインに準拠した新しい Inner Error 形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|inner.Errorcode|string|コード エラー文字列を取得します。|
|innerError.message|string|高レベルのエラー メッセージを取得します。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例

成功した応答では、次の情報が返されます。

ジョブ ID は、POST メソッドの応答ヘッダー Operation-Location URL 値で確認できます。 URL の最後のパラメーターは、操作のジョブ ID ("/operation/" に続く文字列) です。

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>エラー応答の例

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、ドキュメント翻訳とクライアント ライブラリの使用方法についてご確認ください。

> [!div class="nextstepaction"]
> [ドキュメント変換を使ってみる](../get-started-with-document-translation.md)
