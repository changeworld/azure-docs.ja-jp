---
title: Application Gateway の Azure Monitor メトリック
description: メトリックを使用してアプリケーション ゲートウェイのパフォーマンスを監視する方法について説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 8d75dbe5d4ab819e5bbe64e20ad84eb1c26a87a3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75777820"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway のメトリック

Application Gateway からは、ご利用の Application Gateway インスタンスおよびバックエンド インスタンスのパフォーマンスに関する、メトリックと呼ばれるデータ ポイントが [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) に発行されます。 これらのメトリックは、順序付けられた時系列データ セットに含まれる数値であり、特定の時間におけるご利用のアプリケーション ゲートウェイの一部の側面を記述しています。 Application Gateway を通過する要求がある場合は、それによって 60 秒間隔でメトリックが測定されて送信されます。 Application Gateway を通過する要求がない場合、またはメトリックのデータがない場合、メトリックは報告されません。 詳細については、「[Azure Monitor metrics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)」(Azure Monitor メトリック) を参照してください。

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway V2 SKU でサポートされるメトリック

### <a name="timing-metrics"></a>タイミングのメトリック

要求と応答のタイミングに関連する次のメトリックを利用できます。 特定のリスナーのこれらのメトリックを分析することで、WAN、Application Gateway、Application Gateway とバックエンド アプリケーションの間のネットワーク、あるいはバックエンド アプリケーションのパフォーマンスのいずれが原因でアプリケーションの速度が低下しているかを確認できます。

> [!NOTE]
>
> Application Gateway に複数のリスナーがある場合は、意味のある推論を得るために、異なる待機時間メトリックを比較しながら、常に *[リスナー]* ディメンションでフィルター処理します。

- **クライアント RTT**

  クライアントと Application Gateway の間の平均ラウンドトリップ時間。 このメトリックは、接続が確立され、受信確認が返されるまでにかかる時間を示します。 

- **アプリケーション ゲートウェイの合計時間**

  要求が処理されその応答が送信されるのにかかった平均時間。 これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの平均間隔として計算されます。 これには、通常、Application Gateway の処理時間、要求パケットと応答パケットがネットワーク経由で移動する時間、およびバックエンド サーバーが応答するまでの時間が含まれていることに注意することが重要です。
  
リスナーによってフィルター処理された後、*クライアント RTT* が *Application Gateway の合計時間*を大幅に超える場合は、クライアントによって観測された待機時間はクライアントと Application Gateway 間のネットワーク接続に起因するものだと推測できます。 両方の待機時間が比較できる場合は、次のいずれかが原因で待機時間が長くなる可能性があります。Application Gateway、Application Gateway とバックエンド アプリケーション間のネットワーク、またはバックエンド アプリケーションのパフォーマンス。

- **バックエンド先頭バイト応答時間**

  バックエンド サーバーへの接続の確立を開始してから応答ヘッダーの最初のバイトを受信するまでの時間間隔であり、バックエンド サーバーのおおよその処理時間です。

- **バックエンド最終バイト応答時間**

  バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔です。
  
*Application Gateway の合計時間*が、特定のリスナーの*バックエンド最終バイト応答時間*よりもはるかに多い場合は、Application Gateway が原因で待機時間が長くなっている可能性があることが推測できます。 一方で、2 つのメトリックが比較可能な場合、この問題は、Application Gateway とバックエンド アプリケーション間のネットワーク、またはバックエンド アプリケーションのパフォーマンスによって発生している可能性があります。

- **バックエンド接続時間**

  バックエンド アプリケーションとの接続を確立するために費やされた時間です。 SSL の場合、これにはハンドシェイクに費やされた時間が含まれます。 このメトリックは接続時間のみを測定し、他の待機時間と直接比較すべきでないため、他の待機時間とは異なります。 ただし、*バックエンド接続時間*のパターンを他の待機時間のパターンと比較することで、Application Gateway とバックエンド アプリケーションの間のネットワークのバリエーションが原因で時間が増加したのかどうかを示すことができます。 
  

### <a name="application-gateway-metrics"></a>Application Gateway メトリック

Application Gateway に関しては、次のメトリックを利用できます。

- **受信バイト数**

   Application Gateway がクライアントから受信したバイト数です。

- **送信バイト数**

   Application Gateway からクライアントに送信されたバイト数です。

- **クライアント TLS プロトコル**

   Application Gateway との接続を確立したクライアントによって開始された TLS 要求と非 TLS 要求の数です。 TLS プロトコルの分布を表示するには、ディメンションの TLS プロトコルでフィルター処理します。

- **現在の容量ユニット**

   使用された容量ユニットの数です。 容量ユニットでは、固定費に追加して課金される使用量ベースの費用が測定されます。 容量ユニットには、コンピューティング ユニット、永続的接続、スループットの 3 つの決定項があります。 各容量ユニットは最大で次のものにより構成されます: 1 コンピューティング ユニット、または 2500 永続的接続、または 2.22 Mbps のスループット。

- **現在のコンピューティング ユニット**

   消費されたプロセッサ容量の数です。 コンピューティング ユニットに影響する要因は、1 秒あたりの TLS 接続数、URL 書き換え計算、WAF ルールの処理です。 

- **現在の接続数**

   Application Gateway で確立された現在の接続の数

- **失敗した要求**

   Application Gateway が処理した失敗した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。


- **応答の状態**

   Application Gateway によって返される HTTP 応答の状態です。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。

- **スループット**

   Application Gateway が処理した 1 秒あたりのバイト数

- **要求の合計数**

   Application Gateway が処理した成功した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。

- **Web アプリケーション ファイアウォールの一致ルール**

- **Web アプリケーション ファイアウォールのトリガー ルール**

### <a name="backend-metrics"></a>バックエンド メトリック

Application Gateway に関しては、次のメトリックを利用できます。

- **バックエンド応答の状態**

  バックエンドによって返された HTTP 応答状態コードの数です。 これには、Application Gateway によって生成された応答コードは含まれません。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。

- **正常なホストの数**

  正常性プローブによって正常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。

- **異常なホストの数**

  正常性プローブによって異常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プール内の異常なホストを表示できます。

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway V1 SKU でサポートされるメトリック

### <a name="application-gateway-metrics"></a>Application Gateway メトリック

Application Gateway に関しては、次のメトリックを利用できます。

- **CPU 使用率**

  Application Gateway に割り当てられた CPU の使用率を表示します。  通常の状況では、CPU 使用率が 90% を超えることは望ましくありません。これは Application Gateway の背後でホストされている Web サイトで待機時間が発生し、クライアント エクスペリエンスを乱す可能性があるためです。 インスタンス数を増やすか、SKU のサイズを大きくするか、またはその両方を行うことによって、Application Gateway の構成を変更し、CPU 使用率を間接的に制御または向上させることができます。

- **現在の接続数**

  Application Gateway で確立された現在の接続の数

- **失敗した要求**

  Application Gateway が処理した失敗した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。

- **応答の状態**

  Application Gateway によって返される HTTP 応答の状態です。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。

- **スループット**

  Application Gateway が処理した 1 秒あたりのバイト数

- **要求の合計数**

  Application Gateway が処理した成功した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。

- **Web アプリケーション ファイアウォールの一致ルール**

- **Web アプリケーション ファイアウォールのトリガー ルール**

### <a name="backend-metrics"></a>バックエンド メトリック

Application Gateway に関しては、次のメトリックを利用できます。

- **正常なホストの数**

  正常性プローブによって正常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。

- **異常なホストの数**

  正常性プローブによって異常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プール内の異常なホストを表示できます。

## <a name="metrics-visualization"></a>メトリックの視覚化

アプリケーション ゲートウェイに移動して **[監視]** の **[メトリック]** を選択します。 利用できる値を表示するには、 **[メトリック]** ドロップダウン リストを選択します。

次の画像では、最後の 30 分間に表示された 3 つのメトリックの例を確認できます。

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

現在のメトリックの一覧を確認するには、「[Azure Monitor のサポートされるメトリック](../azure-monitor/platform/metrics-supported.md)」を参照してください。

### <a name="alert-rules-on-metrics"></a>メトリックに基づくアラート ルール

リソースのメトリックに基づいてアラート ルールを開始できます。 たとえば、Application Gateway のスループットが指定した期間にしきい値を上回るか下回る場合、またはしきい値に等しい場合、アラートで webhook を呼び出したり、管理者に電子メールを送信したりできます。

次の例では、スループットのしきい値を超えた後に管理者に電子メールを送信するアラート ルールの作成手順を説明します。

1. **[メトリック アラートの追加]** を選択して **[ルールの追加]** ページを開きます。 このページには、メトリック ページからもアクセスできます。

   ![[メトリック アラートの追加] ボタン][6]

2. **[ルールの追加]** ページで、名前、条件、通知の各セクションに入力して、 **[OK]** を選択します。

   * **[条件]** セレクターに入力できるのは、 **[より大きい]** 、 **[以上]** 、 **[未満]** 、または **[以下]** の 4 つの値です。

   * **[期間]** セレクターで、5 分から 6 時間までの期間を選択します。

   * **[メールの所有者、投稿者、閲覧者]** を選択すると、そのリソースのアクセス権を持つユーザーに基づいて電子メールを動的に送信できます。 それ以外の場合は、ユーザーのコンマ区切りの一覧を **[追加の管理者メール]** ボックスに指定できます。

   ![[ルールの追加] ページ][7]

しきい値を超えた場合、次の図のような電子メールが送信されます。

![しきい値違反の電子メール][8]

メトリック アラートを作成すると、アラートの一覧が表示されます。 この一覧には、すべてのアラート ルールの概要が示されます。

![アラートとルールの一覧][9]

アラート通知の詳細については、「[Microsoft Azure のアラートの概要](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。

webhook の詳細および webhook とアラートを使用する方法については、「[Azure メトリック アラートでの webhook の構成](../azure-monitor/platform/alerts-webhooks.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)を使用して、カウンターとイベント ログを視覚化します。
* [Power BI を使用した Azure アクティビティ ログの視覚化](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログ
* [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
