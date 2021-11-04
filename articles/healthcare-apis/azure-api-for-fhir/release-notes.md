---
title: Azure API for FHIR 月間リリース
description: この記事では、Azure API for FHIR の月単位の機能と機能強化について詳しく説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 201de8f7bbae01c708757ee2290f6005979410c4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477539"
---
# <a name="release-notes-azure-api-for-fhir"></a>リリースノート: FHIR 用 Azure API

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、Azure API for FHIR に加えられた機能と機能強化について詳しく説明します。

## <a name="september-2021"></a>2021 年 9 月 

### <a name="features-and-enhancements"></a>**機能と機能強化**

|機能強化 | 説明 |
|:------------------- | -----------:|

|条件付きパッチのサポートを追加しました | [条件付きパッチ](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| :----------------------------------- | ------: |
|条件付きパッチ |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|条件付きパッチ監査イベントを追加しました |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|バンドルで JSON 修正プログラムを許可する | [バンドルの JSON パッチ](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| :----------------------------------- | ------: |
|パッチ要求での検索履歴バンドルを許可する |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|バイナリリソースを使用してバンドルの JSON 修正プログラムを有効にする |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|新しい監査イベントのサブタイプ| 説明|
| :----------------------------------- | ------: |
|新しい audit [OperationName サブタイプ](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)を追加しました| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|再[インデックスの機能強化](how-to-run-a-reindex.md): | 説明|
| :----------------------------------- | ------: |
|[Reindex パラメーターの境界を](how-to-run-a-reindex.md)追加しました|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|パラメーター境界の再インデックスのエラーメッセージを更新する|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|最終的な再インデックス数のチェックを追加します |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**バグの修正**

|解決された修正プログラムのバグ| 説明|
| :----------------------------------- | ------: |
|修正プログラムの適用中に発生する例外のより広範なキャッチ |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|STU3 の PATCH を使用して履歴を修正します。| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|カスタム検索のバグ| 説明|
| :----------------------------------- | ------: |
|カスタム検索パラメーターを使用して削除エラーに対処します| [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|検索パラメーターの削除中に再試行ロジックを追加しました| [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager の検索オプションに最大項目数を設定します| [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|検索パラメーターに不適切な式がある場合の例外の向上|[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|解決された再試行503エラー| 説明|
| :----------------------------------- | ------: |
|Cosmos DB からの再試行503エラー |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Storeprocedures からの429の処理を修正します。|[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub 終了した問題| 説明|
| :----------------------------------- | ------: |
|CarePlan 医療デバイスのカスタム検索パラメーターを作成できません |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|ID のない条件付き作成の不明なエラーメッセージ| [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>FHIR 用 IoT コネクタ (プレビュー)

|バグの修正| 説明|
| :-----------------------------------| ------: |
|IoT コネクタの Azure ドキュメントへのリンクを修正|Azure API for FHIR ポータルでの壊れたリンク |

## <a name="next-steps"></a>次のステップ

Azure の医療 Api (FHIR サービス、DICOM サービス、IoT コネクタ) の機能とバグ修正の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[リリースノート: Azure の医療 Api](../release-notes.md)
