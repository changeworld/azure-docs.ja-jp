---
title: Azure Data Factory および Synapse Analytics で Fail アクティビティを実行する
titleSuffix: Azure Data Factory & Azure Synapse
description: この記事では、Azure Data Factory と Synapse Analytics の Fail アクティビティで、意図的にパイプラインでエラーをスローする方法を説明します。
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 2f0acb894d6bdacef69dcf8adb2088214e094415
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517959"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory および Synapse Analytics で Fail アクティビティを実行する

パイプラインで意図的にエラーをスローしたい場合があります。 [Lookup アクティビティ](control-flow-lookup-activity.md)から一致するデータが返されなかったり、[Custom アクティビティ](transform-data-using-dotnet-custom-activity.md)が内部エラーで終了したりする場合があります。 理由にかかわらず、パイプラインで Fail アクティビティを使用し、そのエラー メッセージとエラー コードの両方をカスタマイズできるようになりました。

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>構文

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>型のプロパティ

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| name | Fail アクティビティの名前。 | String | はい |
| type | **Fail** に設定する必要があります。 | String | はい |
| message | Fail アクティビティで表示されるエラー メッセージ。 実行時に評価される動的コンテンツにすることができます。 | String | はい |
| errorCode | Fail アクティビティのエラーの種類を分類するエラー コード。 実行時に評価される動的コンテンツにすることができます。 | String | はい |
| | |

## <a name="understand-the-fail-activity-error-code"></a>Fail アクティビティのエラー コードを理解する

Fail アクティビティのエラー メッセージとエラー コードは、通常、ユーザーによって設定されます。 エラー コードの具体的な意味については、パイプラインの開発者にお問い合わせください。 ただし、次のエッジ ケースでは、Azure Data Factory によってエラー メッセージとエラー コードが設定されます。

| 状況の説明 | エラー メッセージ | エラー コード |
| --- | --- | --- |
`message` と `errorCode` 内の (動的) コンテンツは正しく解釈されました。 | ユーザーによって設定されたエラー メッセージ | ユーザーによって設定されたエラー コード |
`message` と `errorCode` の両方の動的コンテンツを解釈できません。 | " _<activity_name>_ の失敗メッセージまたはエラー コードを解釈できませんでした" | `ErrorCodeNotString` |
| `message` 内の動的コンテンツを文字列として解釈することはできません。 | " _<activity_name>_ の失敗メッセージ パラメーターを、文字列として解釈できませんでした" | ユーザーによって設定されたエラー コード |
| `message` 内の動的コンテンツは、null、空の文字列、または空白に解釈されます。 | " _<activity_name>_ の失敗メッセージまたはエラー コードを解釈できませんでした" | ユーザーによって設定されたエラー コード |
| `errorCode` 内の動的コンテンツを文字列として解釈することはできません。 | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString` |
| `errorCode` 内の動的コンテンツは、null、空の文字列、または空白に解釈されます。 | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString` |
| ユーザーによって指定された `message` または `errorCode` の値には、文字列を使用できません。* | パイプラインは、"プロパティ <`errorCode`/`message`> の値が無効である" ために "_失敗_" しました | |
| `message` フィールドがありません。* | "失敗メッセージが指定されませんでした" | ユーザーによって設定されたエラー コード |
| `errorCode` フィールドがありません。* | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString` |
| | |

\* パイプラインが Data Factory の Web ユーザー インターフェイス (UI) を使用して開発されている場合、この状況は発生しません。

## <a name="next-steps"></a>次のステップ

サポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
- [パイプライン エラーについて](tutorial-pipeline-failure-error-handling.md)
