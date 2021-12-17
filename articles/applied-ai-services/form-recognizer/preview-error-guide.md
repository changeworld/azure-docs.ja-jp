---
title: 'リファレンス: Form Recognizer のエラー (2021-09-30-preview)'
titleSuffix: Azure Applied AI Services
description: Form Recognizer でのエラーの表現方法を説明し、サービスによって返される可能性があるエラーの一覧を示します。
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: ドキュメントの処理
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730571"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>Form Recognizer エラー ガイド v3.0 (プレビュー)

Form Recognizer では、統一された設計を使用して、REST API で発生するすべてのエラーが表されます。  API 操作から 4xx または 5xx の状態コードが返されるときは常に、エラーに関する追加情報が次のように応答 JSON の本文で返されます。

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

複数のエラーが発生する可能性がある実行時間の長い操作の場合、最上位レベルのエラー コードが最も重大なエラーに設定され、個々のエラーは *error.details* プロパティの下の一覧に表示されます。  このようなシナリオでは、個々のエラーの *target* プロパティによってエラーのトリガーが指定されます。

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

最上位の *error.code* プロパティには、次のいずれかのエラー コード メッセージが設定される可能性があります。

| エラー コード           | Message                                                | HTTP の状態 |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | 無効な要求です。                                       | 400         |
| InvalidArgument      | 引数が無効です。                                      | 400         |
| Forbidden            | ポリシーまたは他の構成のためにアクセスが禁止されました。 | 403         |
| NotFound             | リソースが見つかりません。                                    | 404         |
| MethodNotAllowed     | 要求された HTTP メソッドは許可されません。              | 405         |
| 競合             | 競合のため、要求を完了できませんでした。  | 409         |
| UnsupportedMediaType | 要求のコンテンツ タイプはサポートされていません。                 | 415         |
| InternalServerError  | 予期しないエラーが発生しました。                          | 500         |
| ServiceUnavailable   | 一時的なエラーが発生しました。 やり直してください。      | 503         |

可能であれば、"*内部エラー*" プロパティで詳細が指定されています。

| 最上位のエラー コード | 内部エラー コード | Message |
| -------------- | ---------------- | ------- |
| 競合 | ModelExists | 指定された名前のモデルは既に存在します。 |
| Forbidden | AuthorizationFailed | 承認に失敗しました: <詳細> |
| Forbidden | InvalidDataProtectionKey | データ保護キーが無効です: <詳細> |
| Forbidden | OutboundAccessForbidden | 要求に、現在のアクセス制御ポリシーで許可されていないドメイン名が含まれます。 |
| InternalServerError | Unknown | 不明なエラー。 |
| InvalidArgument | InvalidContentSourceFormat | 無効なコンテンツ ソース: <詳細> |
| InvalidArgument | InvalidParameter | パラメーター <パラメーター名> が無効です: <詳細> |
| InvalidArgument | InvalidParameterLength | パラメーター <パラメーター名> の長さは <最大文字数> 文字を超えることはできません。 |
| InvalidArgument | InvalidSasToken | Shared Access Signature (SAS) が無効です: <詳細> |
| InvalidArgument | ParameterMissing | パラメーター <パラメーター名> は必須です。 |
| InvalidRequest | ContentSourceNotAccessible | コンテンツにアクセスできません: <詳細> |
| InvalidRequest | ContentSourceTimeout | クライアントからのファイルの受信中のタイムアウト。 |
| InvalidRequest | DocumentModelLimit | アカウントで <最大モデル数> 個より多くのモデルを作成することはできません。 |
| InvalidRequest | DocumentModelLimitComposed | アカウントで <詳細> 個より多くのコンポーネント モデルを含むモデルを作成することはできません。 |
| InvalidRequest | InvalidContent | ファイルが破損しているか、サポートされてない形式です。 サポートされる形式の一覧については、ドキュメントを参照してください。 |
| InvalidRequest | InvalidContentDimensions | 入力画像の寸法が範囲を超えています。 サポートされている画像の寸法については、ドキュメントを参照してください。 |
| InvalidRequest | InvalidContentLength | 入力画像が大きすぎます。 ファイルの最大サイズについては、ドキュメントを参照してください。 |
| InvalidRequest | InvalidFieldsDefinition | 無効なフィールド: <詳細> |
| InvalidRequest | InvalidTrainingContentLength | トレーニング コンテンツには <バイト数> バイトが含まれます。 トレーニングは <最大バイト数> バイトに制限されます。 |
| InvalidRequest | InvalidTrainingContentPageCount | トレーニング コンテンツには <ページ数> ページが含まれます。 トレーニングは <ページ数> ページに制限されます。 |
| InvalidRequest | ModelAnalyzeError | カスタム モデルを使用して分析できませんでした: <詳細> |
| InvalidRequest | ModelBuildError | モデルをビルドできませんでした: <詳細> |
| InvalidRequest | ModelComposeError | モデルを作成できませんでした: <詳細> |
| InvalidRequest | ModelNotReady | モデルは、要求された操作の準備ができていません。 トレーニングが完了するまで待つか、操作のエラーを確認してください。 |
| InvalidRequest | ModelReadOnly | 要求されたモデルは読み取り専用です。 |
| InvalidRequest | NotSupportedApiVersion | 要求された操作には <API の最低バージョン> 以降が必要です。 |
| InvalidRequest | OperationNotCancellable | 操作を取り消すことはもうできません。 |
| InvalidRequest | TrainingContentMissing | トレーニング データがありません: <詳細> |
| InvalidRequest | UnsupportedContent | コンテンツがサポートされていません: <詳細> |
| NotFound | ModelNotFound | 要求されたモデルは見つかりませんでした。 削除された可能性があります。または、まだビルド中です。 |
| NotFound | OperationNotFound | 要求された操作は見つかりませんでした。 識別子が無効であるか、操作の有効期限が切れている可能性があります。 |