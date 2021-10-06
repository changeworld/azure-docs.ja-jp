---
title: Fail アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics の Fail アクティビティは、意図的にパイプラインでエラーをスローします
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 95c4a7245a39ad717ad47ac9e8a7c72b2b5365fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061951"
---
# <a name="execute-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Azure Data Factory および Synapse Analytics で Fail アクティビティ を実行する (プレビュー)

場合によっては、パイプラインでエラーを意図的にスローする必要が生じることあります。たとえば、[lookup アクティビティ](control-flow-lookup-activity.md)によって一致するデータが返されない場合や、[custom アクティビティ](transform-data-using-dotnet-custom-activity.md)で 200 個が返されても、内部エラーがスローされた場合などです。 理由は何であっても、パイプラインで Fail アクティビティを使用して、エラーコードとエラーメッセージの両方をカスタマイズできるようになりました。

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

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | `Fail` アクティビティの名前。 | String | はい
type | **Fail** に設定する必要があります。 | String | はい
message | Fail アクティビティで表示されるエラーメッセージ。 実行時に評価される動的コンテンツにすることができます。 | String | はい
errorCode | Fail アクティビティのエラーの種類を分類するエラー コード。 実行時に評価される動的コンテンツにすることができます。 | String | はい

## <a name="understand-fail-activity-error-code"></a>Fail アクティビティのエラー コードを理解する

通常、Fail アクティビティのエラー コードとエラー メッセージは、顧客によって設定されます。 エラー コードの具体的な意味については、パイプラインの開発者にお問い合わせください。 ただし、次のエッジ ケースでは、ADF によってエラー コードまたはエラー メッセージが設定されます。

状況の説明 | エラー メッセージ | エラー コード
-------- | ----------- | --------------
`message` と `errorCode` 内の (動的) コンテンツは正しく解釈されました | ユーザーによって設定されたエラー メッセージ | ユーザーによって設定されたエラー コード
`message` と `errorCode` の両方の動的コンテンツを解釈できません | _<activity_name>_ の失敗メッセージまたはエラー コードを解釈できませんでした | `ErrorCodeNotString`
`message` 内の動的コンテンツを文字列として解釈することはできません | _<activity_name>_ の失敗メッセージ パラメーターを、文字列として解釈できませんでした | ユーザーによって設定されたエラー コード
`message` 内の動的コンテンツは、null、空の文字列、または空白に解釈されます | _<activity_name>_ の失敗メッセージまたはエラー コードを解釈できませんでした | ユーザーによって設定されたエラー コード
`errorCode` 内の動的コンテンツを文字列として解釈することはできません | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString`
`errorCode` 内の動的コンテンツは、null、空の文字列、または空白に解釈されます | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString`
ユーザーによって指定された `message` または `errorCode` の値には、文字列を使用できません* | パイプラインが __失敗__ しました。プロパティ <`errorCode`/`message`> の値が無効です
欠落している `message` フィールド * | Fail メッセージが指定されていませんでした | ユーザーによって設定されたエラー コード
欠落している `errorCode` フィールド * | ユーザーによって設定されたエラー メッセージ | `ErrorCodeNotString`

パイプラインが Data Factory の Web ユーザー インターフェイス (UI) を使用して開発されている場合、* でマークされた状況は発生しません。

## <a name="next-steps"></a>次のステップ

サポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
