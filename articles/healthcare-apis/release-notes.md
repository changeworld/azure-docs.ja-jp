---
title: Azure の医療 Api の毎月のリリース
description: この記事では、Azure の医療 Api の月単位の機能と機能強化について詳しく説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.author: cavoeg
ms.openlocfilehash: afb8596a4614f3866751a0f7cafecd4bec6ae1c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555339"
---
# <a name="release-notes-azure-healthcare-apis"></a>リリースノート: Azure の医療 Api

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。 

Azure の医療 Api は、医療業界のオープンな標準とフレームワークに基づいた、管理された API サービスのセットです。 保護された医療情報 (PHI) のデータセットをまとめて、機械学習、分析、AI 用のツールをエンドツーエンドに接続することで、スケーラブルで安全な医療ソリューションを構築できます。 このドキュメントでは、相互にシームレスに連携するさまざまなサービスの種類 (FHIR サービス、DICOM サービス、IoT コネクタ) など、Azure の医療 Api に加えられた機能と機能強化について詳しく説明します。

## <a name="october-2021"></a>2021 年 10 月

### <a name="azure-healthcare-apis-feature-enhancements"></a>Azure の医療 Api 機能の機能強化


| 増強 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 関連情報           |
|------------- | -----------------------------: |
|テストデータジェネレーターツール |synthea データを使用して [テストデータジェネレーターツール](https://github.com/microsoft/healthcare-apis-samples/blob/main/docs/HowToRunPerformanceTest.md)を含めるように、医療 api GitHub samples リポジトリを更新しました。 このツールは、Apache JMeter に基づくオープンソース [パブリックテストプロジェクト](https://github.com/ShadowPic/PublicTestProjects)の機能強化で、パフォーマンステストのために Azure AKS にデプロイできます。 |

### <a name="fhir-service"></a>FHIR サービス

#### <a name="feature-enhancements"></a>**機能強化**

|機能強化 | 関連情報 |
|------------------------ | -------------------------------: |
|文字列と dateTime に対する [_sort](././../healthcare-apis/fhir/overview-of-search.md#search-result-parameters) のサポートが追加されました。 |[#2169](https://github.com/microsoft/fhir-server/pull/2169)  |

#### <a name="bug-fixes"></a>**バグの修正**

|バグの修正 | 関連情報 |
|------------------------ | -------------------------------: |
|[条件付き削除](././../healthcare-apis/fhir/fhir-rest-api-capabilities.md#conditional-delete)が無限ループになる可能性がある問題を修正しました。 | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |
|解決済み500エラーは、バンドル投稿のトランザクション本文の形式が間違っていることが原因で発生する可能性があります。 [トランザクションバンドル](././..//healthcare-apis/fhir/fhir-features-supported.md#rest-api)要求に URL が設定されていることを確認しました。 | [#2255](https://github.com/microsoft/fhir-server/pull/2255) |

### <a name="dicom-service"></a>**DICOM サービス**

|追加されたサポート | 関連情報 |
|------------------------ | -------------------------------: |
|リージョン |南ブラジルおよび中央カナダ |
|拡張クエリタグ |DateTime (DT) と Time (TM) 値表現 (VR) 型 |

|バグの修正 | 関連情報 |
|------------------------ | -------------------------------: |
|ワークスペース名に修正プログラムを実装 |名前が文字で始まるワークスペースで、DICOM サービスを使用できるようにします。 |

## <a name="september-2021"></a>2021 年 9 月

### <a name="fhir-service"></a>FHIR サービス

#### <a name="feature-enhancements"></a>**機能強化**

|機能強化 | 関連情報 |
|------------------------ | -------------------------------: |

|条件付きパッチのサポートを追加しました | [条件付きパッチ](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|------------------------ | -------------------------------: |
|条件付きパッチ | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|条件付きパッチ監査イベントを追加しました。 | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|バンドルで JSON 修正プログラムを許可する | [バンドルの JSON パッチ](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|------------------------ | -------------------------------: |
|パッチ要求のある検索履歴バンドルを許可します。 |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|バイナリリソースを使用してバンドルで JSON 修正プログラムを有効にしました。 |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|新しい監査イベント [の OperationName サブタイプが](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)追加されました。 | [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|再インデックスジョブの実行 | [再インデックスの機能強化](./././fhir/how-to-run-a-reindex.md)|
|------------------------ | -------------------------------: |
|[Reindex パラメーターの境界を](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations)追加しました。 |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|パラメーター境界の再インデックスのエラーメッセージを更新しました。|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|最後の reindex カウントチェックを追加します。 |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**バグの修正**

|解決された修正プログラムのバグ | 関連情報 |
|------------------------ | -------------------------------: |
|パッチを適用するときの例外のために、より広い範囲でキャッチします。 | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|STU3 の PATCH を使用して履歴を修正します。 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|カスタム検索のバグ | 関連情報 |
|------------------------ | -------------------------------: |
|カスタム検索パラメーターを使用して削除エラーを解決します。 |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|検索パラメーターの削除中に再試行ロジックが追加されました。 | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager の検索オプションに最大項目数を設定します。 |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|検索パラメーターに不適切な式がある場合は、より優れた例外を提供します。 |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|1つのリソースに障害が発生した場合にバッチの再インデックス処理を SQL 解決 | 関連情報 |
|------------------------ | -------------------------------: |
|バッチの再インデックス再試行ロジック SQL 更新します。 |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub 終了した問題 | 関連情報 |
|------------------------ | -------------------------------: |
|ID のない条件付き作成の不明なエラーメッセージです。 |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM サービス**

|バグの修正 | 関連情報 |
|------------------------ | -------------------------------: |
|QIDO ページングの順序付けに関する問題を解決するための修正プログラムを実装します。 |  [#989](https://github.com/microsoft/dicom-server/pull/989) |


### <a name="iot-connector"></a>**IoT コネクタ**

|バグの修正 | 関連情報 |
|------------------------ | -------------------------------: |
|IoT コネクタは、正常性データの標準化をサポートおよび強化するための計算機能を強化しました。 | 参照: デバイスマッピングと[計算関数](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md)[を使用する](./../healthcare-apis/iot/how-to-use-device-mapping-iot.md) |

## <a name="next-steps"></a>次の手順

FHIR の Azure API の機能とバグ修正の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[リリースノート: FHIR 用 Azure API](./azure-api-for-fhir/release-notes.md)


