---
title: Azure API Management と Azure Application Insights の統合
titleSuffix: Azure API Management
description: Azure Application Insights の Azure API Management でイベントをログに記録し、表示する方法について説明します。
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/27/2021
ms.author: danlep
ms.openlocfilehash: 0833e80c17294bc50489f37b21a0b605a0b0ac9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717508"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management と Azure Application Insights を統合する方法

Azure Application Insights を Azure API Management と簡単に統合できます。 Azure Application Insights は、複数のプラットフォームでアプリを構築および管理する Web 開発者向けの拡張可能なサービスです。 このガイドで行うこと:
* Application Insights を API Management に統合する方法を手順を追って説明します。
* API Management サービス インスタンスに対するパフォーマンスへの影響を軽減する戦略について説明します。

## <a name="prerequisites"></a>前提条件

Azure API Management インスタンスが必要です。 まず、[作成](get-started-create-service-instance.md)できます。

## <a name="create-an-application-insights-instance"></a>Application Insights インスタンスを作成する

Application Insights を使用するには、[Application Insights サービスのインスタンスを作成](../azure-monitor/app/create-new-resource.md)します。 Azure portal を使用してインスタンスを作成するには、「[ワークスペースベースの Application Insights リソース](../azure-monitor/app/create-workspace-resource.md)」を参照してください。

## <a name="create-a-connection-between-application-insights-and-api-management"></a>Application Insights と API Management 間の接続を作成する

1. **Azure Portal** で **Azure API Management サービス インスタンス** に移動します。
1. 左にあるメニューから **[Application Insights]** を選択します。
1. **[+ 追加]** を選択します。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="新しい接続を追加する場所が示されているスクリーンショット":::
1. 前に作成した **Application Insights** インスタンスを選択し、簡単な説明を入力します。
1. Application Insights で API Management インスタンスの [可用性監視](../azure-monitor/app/monitor-web-app-availability.md)を有効にするには、 **[可用性の監視の追加]** のチェックボックスを選択します。
    * この設定では、API Management サービス エンドポイントが応答しているかどうかが定期的に検証されます。 
    * 結果は Application Insights インスタンスの **[可用性]** ペインに表示されます。
1. **［作成］** を選択します
1. インストルメンテーション キーを持つ新しい Application Insights ロガーが一覧に表示されるのを確認します。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="インストルメンテーション キーを持つ、新しく生成された Application Insights ロガーが表示される場所を示すスクリーンショット":::

> [!NOTE]
> バックグラウンドで、API Management インスタンスのインストルメンテーション キーを含む[ロガー](/rest/api/apimanagement/2020-12-01/logger/create-or-update) エンティティが Application Insights インスタンス内に作成されます。

## <a name="enable-application-insights-logging-for-your-api"></a>API で Application Insights ログを有効にする

1. **Azure Portal** で **Azure API Management サービス インスタンス** に移動します。
1. 左にあるメニューから **[API]** を選択します。
1. API をクリックします。今回は **Demo Conference API** です。 構成されている場合は、バージョンを選択します。
1. 上のバーで **[設定]** 他部に移動します。
1. 下にスクロールし、 **[診断ログ]** セクションを表示します。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App Insights ロガー":::
1. **[有効化]** ボックスをオンにします。
1. **[Destination]\(出力先\)** ドロップダウンで、接続済みのロガーを選択します。
1. **[Sampling (%)]\(サンプリング (%)\)** に **100** と入力し、 **[Always log errors]\(エラーは常に記録する\)** チェックボックスをオンにします。
1. 残りの設定はそのままにします。

    > [!WARNING]
    > **[Number of payload bytes to log]\(ログに記録するペイロードのバイト数\)** の既定の値 **0** を上書きすると、API のパフォーマンスが大幅に低下する可能性があります。

1. **[保存]** を選択します。
1. バックグラウンドで、`applicationinsights` という名前の[診断](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update)エンティティが API レベルで作成されます。

> [!NOTE]
> 要求は、API Management が応答全体をクライアントに送信すると成功します。


| 設定名                        | 値の型                        | 説明                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 有効化                              | boolean                           | この API のログ記録が有効になっているかどうかを指定します。                                                                                                                                                                                                                                                                                                |
| 宛先                         | Azure Application Insights ロガー | 使用する Azure Application Insights ロガーを指定します。                                                                                                                                                                                                                                                                                           |
| [Sampling (%)]\(サンプリング (%)\)                        | decimal                           | 0 から 100 までの値 (パーセント)。 <br/> Application Insights に記録する要求のパーセンテージを指定します。 0% サンプリングの場合、要求はまったく記録されません。100% サンプリングの場合、すべての要求が記録されます。 <br/> Application Insights に要求を記録するときのパフォーマンスへの影響を軽減するには、この設定を使用します。 「[パフォーマンス上の影響とログ サンプリング](#performance-implications-and-log-sampling)」を参照してください。 |
| [Always log errors]\(エラーは常に記録する\)                   | boolean                           | この設定が選択されている場合、 **[サンプリング]** 設定に関係なく、すべてのエラーが Application Insights に記録されます。   
| [Log client IP address]\(クライアント IP アドレスを記録する\) | |  この設定を選択すると、API 要求のクライアント IP アドレスが Application Insights に記録されます。                                         |
| 詳細度         |                                   | 詳細レベルを指定します。 重大度レベルが高いカスタム トレースだけがログに記録されます。 既定値はInformation です。      | 
| [Correlation protocol]\(相関プロトコル\) |  |  複数のコンポーネントによって送信されたテレメトリを関連付けるために使用されるプロトコルを選択します。 既定: [Legacy]\(レガシ\) <br/>詳細については、「[Application Insights におけるテレメトリの相関付け](../azure-monitor/app/correlation.md)」を参照してください。  |
| 基本オプション: [Headers to log]\(ログに記録するヘッダー\)              | list                              | Application Insights に記録する、要求と応答のヘッダーを指定します。  既定値: ヘッダーはログに記録されません。                                                                                                                                                                                                             |
| 基本オプション: [Number of payload bytes to log]\(ログに記録するペイロードのバイト数\) | 整数 (integer)                           | Application Insights に記録する、要求本文と応答本文の最初のバイト数を指定します。  既定値は0。                                                                                                                                                                                                    |
| 詳細オプション: [Frontend Request]\(フロントエンド要求\)  |                                   | "*フロントエンド要求*" を Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド要求* は、Azure API Management サービスで受信する要求です。                                                                                                                                                                        |
| 詳細オプション: [Frontend Response]\(フロントエンド応答\) |                                   | "*フロントエンド応答*" を Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド応答* は、Azure API Management サービスから発信される応答です。                                                                                                                                                                   |
| 詳細オプション: [Backend Request]\(バックエンド要求\)   |                                   | "*バックエンド要求*" を Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド要求* は、Azure API Management サービスから発信される要求です。                                                                                                                                                                        |
| 詳細オプション: [Backend Response]\(バックエンド応答\)  |                                   | "*バックエンド応答*" を Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド応答* は、Azure API Management サービスで受信する応答です。                                                                                                                                                                       |

> [!NOTE]
> ロガーは、さまざまなレベルで指定できます。 
> + 単一の API ロガー。
> + すべての API のロガー。
>  
> 次の両方を指定します。
> + ロガーが異なる場合、両方が使用されます (ログの多重化)。
> + 設定が異なる同じロガーの場合、単一の API ロガー (詳細度が上) が、すべての API のロガーよりも優先されます。

## <a name="what-data-is-added-to-application-insights"></a>Application Insights に追加されるデータ

Application Insights に届くデータ:

| テレメトリ項目 | 説明 |
| -------------- | ----------- |
| *Request* | 受信要求ごと: <ul><li>*フロントエンド要求*</li><li>*フロントエンド応答*</li></ul> |
| *依存関係* | バックエンド サービスに転送される要求ごと: <ul><li>*バックエンド要求*</li><li>*バックエンド応答*</li></ul> |
| *Exception* | 失敗した要求ごと: <ul><li>クライアント接続が閉じられたため、失敗した要求</li><li>API ポリシーの "エラー状態" セクションをトリガーした要求</li><li>応答 HTTP ステータス コードが 4xx または 5xx に一致する要求</li></ul> |
| *トレース* | [トレース](api-management-advanced-policies.md#Trace) ポリシーを構成した場合。 <br /> `trace` ポリシーの `severity` 設定は、Application Insights ログの `verbosity` 設定と同じかそれ以上である必要があります。 |

### <a name="emit-custom-metrics"></a>カスタム メトリックを出力する
[`emit-metric`](api-management-advanced-policies.md#emit-metrics) ポリシーを構成することで、カスタム メトリックを出力できます。 

Application Insights の事前に集計されたメトリックを API Management で使用するには、サービスでカスタム メトリックを手動で有効にする必要があります。
1. [`emit-metric`](api-management-advanced-policies.md#emit-metrics)ポリシーを [API の作成または更新](/rest/api/apimanagement/2021-04-01-preview/api-diagnostic/create-or-update)で使用します。
1. ペイロードに、他のプロパティと共に `"metrics":true` を追加します。

> [!NOTE]
> Application Insights インスタンスあたりのメトリックとイベントに設定されている最大のサイズと数については、[Application Insights の制限](../azure-monitor/service-limits.md#application-insights)に関する記事を参照してください。

## <a name="performance-implications-and-log-sampling"></a>パフォーマンス上の影響とログ サンプリング

> [!WARNING]
> すべてのイベントを記録すると、受信要求の頻度によっては、パフォーマンスに重大な影響を与える可能性があります。

社内負荷テストによると、要求のペースが毎秒 1,000 件を超えるとき、このログ機能を有効にすると、スループットに 40% から 50% の減少がありました。 Application Insights は、アプリケーション パフォーマンスの評価に統計分析を利用するように設計されています。 そうじゃありません：
* 監査システムを意図しています。
* ハイボリューム API の個々の要求をログに記録する場合に適しています。

[ **[サンプリング]** 設定](#enable-application-insights-logging-for-your-api)を調整することで、ログに記録された要求の数を操作できます。 値が 100% の場合、すべての要求が記録されます。値が 0% の場合、何も記録されません。 

**サンプリング** を利用することで利用統計情報の量が減り、パフォーマンスの大きな低下を効果的に回避しながら、ログ利用の長所を維持できます。

パフォーマンスの問題を改善するには、以下をスキップします。
* 要求ヘッダーと応答ヘッダー。
* 本文のログ記録。

## <a name="video"></a>ビデオ

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>次のステップ

+ [Azure Application Insights](/azure/application-insights/) についてさらに学習します。
+ [Azure Event Hubs によるログ記録](api-management-howto-log-event-hubs.md)を検討します。