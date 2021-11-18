---
title: Azure Healthcare API の月次リリース
description: この記事では、Azure Healthcare API の毎月の機能と機能強化について詳しく説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 40afc404e3121ba4b35da895d804c475f9b28d95
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719358"
---
# <a name="release-notes-azure-healthcare-apis"></a>リリース ノート: Azure Healthcare API

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。 

Azure Healthcare API は、医療業界向けオープン標準とフレームワークに基づく一連のマネージド API サービスです。 保護された医療情報 (PHI) データセットを組み合わせて、機械学習、分析、AI 用のツールでエンドツーエンドに接続することで、スケーラブルで安全な医療ソリューションを構築できます。 このドキュメントでは、シームレスに連携するさまざまなサービスの種類 (FHIR サービス、DICOM サービス、IoT コネクタ) など、Azure Healthcare API に対して行われた機能と機能強化について詳しく説明します。

## <a name="september-2021"></a>2021 年 9 月

### <a name="fhir-service"></a>FHIR サービス

#### <a name="feature-enhancements"></a>**機能強化**

|機能強化 | 説明 |
|:------------------- | -----------:|

|条件付き修正プログラムのサポートを追加しました | [条件付き修正プログラム](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -----------:|
|条件付き修正プログラム | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|条件付きパッチ監査イベントを追加する | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|バンドルで JSON パッチを許可する | [バンドル内の JSON パッチ](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -----------:|
|Patch 要求を含む検索履歴バンドルを許可します |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|バイナリ リソースを使用してバンドルで JSON パッチを有効にする |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|新しい監査イベント [OperationName サブ型を追加しました](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| インデックス再作成ジョブの実行 | [インデックスの再作成の機能強化](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -----------:|
|インデックスを [再作成するパラメーターの境界を追加](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) しました|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|パラメーターの境界のインデックスを再作成するエラー メッセージを更新する|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|最後のインデックス再作成数チェックを追加します |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**バグの修正**

|修正された修正プログラムのバグ | 説明 |
|:------------------- | -----------:|

| パッチの適用中の例外に対するより広範なキャッチ | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------:|
|STU3 で PATCH を使用して履歴を修正する |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|カスタム検索のバグ |説明 |
|:------------------- | -----------:|
|Custom Search パラメーターを使用して削除エラーにアドレスを指定します |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Search パラメーターの削除中に再試行ロジックを追加しました | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager の検索オプションで最大項目数を設定する |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|検索パラメーターに悪い式がある場合の例外の向上 |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|1 つのSQL失敗した場合にバッチのインデックスを再作成する問題を解決しました |説明 |
|:------------------- | -----------:|
|バッチSQLインデックス再作成ロジックの更新 |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHubの解決 |説明 |
|:------------------- | -----------:|
|ID を使用しない条件付き作成の不明瞭なエラー メッセージ |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM サービス**

|バグの修正 | 説明 |
|:------------------- | -----------:|

|QIDO ページング順序の問題を解決するための修正を実装しました |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -----------:|

### <a name="iot-connector"></a>**IoT コネクタ**

|バグの修正 | 説明 |
|:------------------- | -----------:|
| IoT コネクタでは、正常性データの標準化をサポートおよび強化する計算による改善が正規化されました。 | 参照:[デバイス マッピングと計算関数](./../healthcare-apis/iot/how-to-use-device-mappings.md)[の使用](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>次の手順

このページの機能とバグ修正については、Azure API for FHIRしてください。

>[!div class="nextstepaction"]
>[リリース ノート: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
