---
title: クイックスタート - SDK テレメトリ データを Application Insights にエクスポートする
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services SDK テレメトリ データを Application Insights にエクスポートする方法について説明します。
author: peiliu
manager: vravikumar
services: azure-communication-services
ms.author: peiliu
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: data
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 71f294cb42977b89fc36d8f750aa43baadb31f47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609500"
---
# <a name="quickstart-using-azure-opentelemetry-exporter-to-export-sdk-telemetry-data-to-application-insights"></a>クイックスタート: Azure OpenTelemetry Exporter を使用して SDK テレメトリ データを Application Insights にエクスポートする

Azure OpenTelemetry Exporter は [Azure Monitor](../../azure-monitor/index.yml) 内の SDK です。 OpenTelemetry を使用してトレース データをエクスポートし、そのデータを [Application Insights](../../azure-monitor/app/app-insights-overview.md) に送信することができます。 OpenTelemetry は、アプリケーションやフレームワークでテレメトリ情報を収集するための標準化された手段となります。

Azure Application Insights は、実行中のアプリケーションの監視に使用される Azure Monitor の機能です。 Microsoft Azure リソース内のアプリケーションに関するテレメトリ データを表示します。 テレメトリ モデルを標準化して、プラットフォームと言語に依存しない監視を作成できます。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/telemetry-app-insights-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/telemetry-app-insights-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/telemetry-app-insights-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/telemetry-app-insights-python.md)]
::: zone-end

アプリの出力に、完了した各アクションが表示されます。
<!---cSpell:disable --->
```console
Created an identity with ID: <identity-id>
Issued an access token with 'chat' scope that expires at <expiry-data>
```
<!---cSpell:enable --->

## <a name="view-the-telemetry-data-in-application-insights"></a>Application Insights でテレメトリ データを表示する
SDK からのテレメトリ データを分析するには、[`Performance`]\(パフォーマンス\) タブの [`Dependencies`]\(依存関係\) に移動します。 追跡した [`Create User Activity`]\(ユーザー アクティビティの作成\) と [`Get Token Activity`]\(トークン アクティビティの取得\) を確認できます。

:::image type="content" source="media/application-insights-dependencies.png" alt-text="Application Insights のテレメトリ データ項目を示すスクリーンショット。":::

さらに詳しい情報を確認したければ、サンプルの詳細を表示してください。

:::image type="content" source="media/application-insights-samples-drill-down.png" alt-text="サンプルのドリルダウン ビューを示すスクリーンショット":::

ドリルダウン ビューには、アクティビティに関する詳しい情報、たとえばアクティビティの呼び出し元やそのタイムスタンプ、名前、パフォーマンス、タイプが表示されます。前出のサンプル コード スニペットで定義したクラウド ロール名やインスタンス ID を確認することもできます。 追跡されたカスタム プロパティもここに表示されることに注目してください。

:::image type="content" source="media/application-insights-e2e-details.png" alt-text="トランザクションの詳細のエンド ツー エンド ビュー":::

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Telemetry Exporter をセットアップする
> * テレメトリ データを Application Insights にエクスポートする
> * エクスポートしたデータを Application Insights で表示する

次のことも実行できます。

- [Application Insights でのデータ分析について理解を深める](/powerapps/maker/canvas-apps/application-insights)