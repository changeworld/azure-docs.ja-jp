---
title: Azure Application Insights での Statsbeat | Microsoft Docs
description: Application Insights SDK と自動インストルメンテーションに関する統計
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 55e727bc05007c69f0144c7f95e17e38f907742e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426888"
---
# <a name="statsbeat-in-azure-application-insights"></a>Azure Application Insights での Statsbeat

Statsbeat は、Application Insights SDK と自動インストルメンテーションに関する[カスタム メトリック](../essentials/metrics-custom-overview.md)として、重要なものと重要でないものを収集します。 Statsbeat では、Azure Monitor Application Insights のユーザーに対して 3 つの利点が提供されます。
-   サービスの正常性と信頼性 (インジェスト エンドポイントへの接続のアウトサイドイン監視)
-   サポート診断 (セルフヘルプ分析情報と CSS 分析情報)
-   製品の改善 (設計の最適化に関する分析情報)

Statsbeat のデータは Microsoft データ ストアに格納されます。  お客様の全体的な監視量とコストには影響はありません。 

## <a name="what-data-does-statsbeat-collect"></a>Statsbeat によって収集されるデータ

Statsbeat によって収集されるメトリックには、重要なものと重要でないものがあります。

## <a name="supported-languages"></a>サポートされている言語

| C#                        | Java            | JavaScript                | Node.js         | Python          |
|---------------------------|-----------------|---------------------------|-----------------|-----------------|
| 現在サポートされていません   | サポートされています       | 現在サポートされていません   | サポートされています       | サポートされています       |


### <a name="essential-statsbeat"></a>重要な Statsbeat

#### <a name="network-statsbeat"></a>Network Statsbeat

|メトリックの名前|ユニット|サポートされるディメンション|
|-----|-----|-----|
|Request Success Count (要求の成功回数)|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Requests Failure Count (要求の失敗回数)|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Request Duration (要求の実行時間)|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|再試行の回数|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Throttle Count (スロットル回数)|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Exception Count (例外回数)|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|

#### <a name="attach-statsbeat"></a>Attach Statsbeat

|メトリックの名前|ユニット|サポートされるディメンション|
|-----|-----|-----|
|Attach|Count| `Resource Provider`, `Resource Provider Identifier`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

#### <a name="feature-statsbeat"></a>Feature Statsbeat

|メトリックの名前|ユニット|サポートされるディメンション|
|-----|-----|-----|
|特徴量|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Feature`, `Type`, `Operating System`, `Language`, `Version`|

### <a name="non-essential-statsbeat"></a>重要でない Statsbeat

再収集可能なテレメトリにディスク永続化を使用する場合のディスク I/O エラーを追跡する

|メトリックの名前|ユニット|サポートされるディメンション|
|-----|-----|-----|
|読み取りエラー数|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|
|書き込みエラー数|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

### <a name="configure-statsbeat"></a>Statsbeat を構成する

#### <a name="java"></a>[Java](#tab/java)

重要でない Statsbeat を無効にするには、次の構成を構成ファイルに追加します。

```json
{
  "preview": {
    "statsbeat": {
        "disabled": "true"
    }
  }
}
```

また、環境変数 `APPLICATIONINSIGHTS_STATSBEAT_DISABLED` を true に設定してこの機能を無効にすることもできます (これは、JSON 構成に指定されている無効化よりも優先されます)。

#### <a name="node"></a>[Node](#tab/node)

なし

#### <a name="python"></a>[Python](#tab/python)

該当なし

---
