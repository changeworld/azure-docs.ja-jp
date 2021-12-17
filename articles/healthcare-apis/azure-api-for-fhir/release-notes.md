---
title: Azure API for FHIR 月間リリース
description: この記事では、Azure API for FHIR の月単位の機能と機能強化について詳しく説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: cavoeg
ms.openlocfilehash: 0acbf2efdb65ff5376da9be918eaead2169cd5d6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547914"
---
# <a name="release-notes-azure-api-for-fhir"></a>リリースノート: FHIR 用 Azure API

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、Azure API for FHIR に加えられた機能と機能強化について詳しく説明します。


## <a name="october-2021"></a>2021 年 10 月

### <a name="bug-fixes"></a>**バグの修正**

| 無限ループのバグ | 関連情報          |
| ----------------- | ----------------------------: |
|[条件付き削除](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete)が無限ループになる可能性がある問題を修正しました。 | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## <a name="september-2021"></a>2021 年 9 月 

### <a name="features-and-enhancements"></a>**機能と機能強化**

|増強 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |関連情報 |
|------------------- | --------------- |

|条件付きパッチのサポートを追加しました | [条件付きパッチ](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| ----------------------------------- | ------: |
|条件付きパッチ |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|条件付きパッチ監査イベントを追加しました。 |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|バンドルで JSON 修正プログラムを許可する | [バンドルの JSON パッチ](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| ----------------------------------- | ------: |
|パッチ要求のある検索履歴バンドルを許可します。 |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|バイナリリソースを使用してバンドルで JSON 修正プログラムを有効にしました。 |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|新しい監査イベントのサブタイプ |関連情報 |
| ----------------------------------- | ---------------: |
|新しい audit [OperationName サブタイプ](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)が追加されました。| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[再インデックスの機能強化](how-to-run-a-reindex.md) |関連情報 |
| ----------------------------------- | ---------------: |
|[Reindex パラメーターの境界を](how-to-run-a-reindex.md)追加しました。 |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|パラメーター境界の再インデックスのエラーメッセージを更新します。 |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|最終的な再インデックス数のチェックを追加しました。 |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**バグの修正**

|解決された修正プログラムのバグ |関連情報 |
| :----------------------------------- | ---------------: |
|パッチを適用するときの例外のために、より広い範囲でキャッチします。 |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|STU3 の PATCH を使用して履歴を修正します。| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|カスタム検索のバグ |関連情報 |
| ----------------------------------- | ---------------: |
|カスタム検索パラメーターを使用して削除エラーを解決します。 | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|検索パラメーターの削除中に再試行ロジックが追加されました。 | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager の検索オプションに最大項目数を設定します。 | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|検索パラメーターに不適切な式がある場合は、より優れた例外を提供します。 |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|解決された再試行503エラー |関連情報 |
| ----------------------------------- | ---------------: |
|Cosmos DB からの再試行503エラー。 |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Storeprocedures からの429の処理を修正します。 |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub 終了した問題 |関連情報 |
| ----------------------------------- | ---------------: |
|CarePlan 医療デバイスのカスタム検索パラメーターを作成できません。 |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|ID のない条件付き作成の不明なエラーメッセージです。 | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>FHIR 用 IoT コネクタ (プレビュー)

|バグの修正 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|関連情報 |
| ----------------------------------- | ---------------: |
|壊れたリンクを修正します。| Azure API for FHIR ポータルの IoT コネクタ Azure ドキュメントへのリンクが更新されました。 |

## <a name="next-steps"></a>次の手順

Azure の医療 Api (FHIR サービス、DICOM サービス、IoT コネクタ) の機能とバグ修正の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[リリースノート: Azure の医療 Api](../release-notes.md)
