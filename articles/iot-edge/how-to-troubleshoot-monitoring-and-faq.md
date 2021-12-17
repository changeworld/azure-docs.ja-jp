---
title: 監視のトラブルシューティングと FAQ - Azure IoT Edge
description: Azure Monitor 統合のトラブルシューティングと FAQ
author: veyalla
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 8c4e74bbd72abdbfaf365afe720149f0eb7fe8ae
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215393"
---
# <a name="faq-and-troubleshooting"></a>FAQ とトラブルシューティング

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>コレクター モジュールが組み込みのエンドポイントからメトリックを収集できない

### <a name="check-if-modules-are-on-the-same-docker-network"></a>モジュールが同じ Docker ネットワーク上にインストールされているかどうかを確認

メトリックコレクター モジュールは、ユーザー定義のネットワークについて Docker の埋め込み DNS リゾルバーに依存します。 DNS リゾルバーは、モジュール名を含むメトリック エンドポイントの IP アドレスを提供します。 これはたとえば、*http://**edgeHub**:9600/metrics* のようになります。

モジュールが同じネットワーク名前空間で実行されていない場合、このメカニズムは機能しません。 たとえば、一部のシナリオでは、モジュールをホスト ネットワークで実行する必要があります。 メトリックコレクター モジュールが別のネットワーク上にある場合、このようなシナリオでは収集に失敗します。

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>*httpSettings__enabled* 環境変数が *false* に設定されていないことを確認

IoT Edge システム モジュールによって公開される組み込みのメトリック エンドポイントでは、http プロトコルが使用されます。 これらは、http が Edge Hub または Edge Agent モジュールの環境変数設定を通じて明示的に無効になっている場合には、モジュール ネットワーク内でも使用できません。

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>http プロキシ サーバーを使用する場合には *NO_PROXY* 環境変数を設定

詳細については、「[プロキシに関する考慮事項](how-to-collect-and-transport-metrics.md#proxy-considerations)」をご覧ください。

### <a name="update-moby-engine"></a>Moby エンジンの更新

Linux ホスト上で、最新バージョンのコンテナー エンジンを使用していることを確認します。 [インストール手順](how-to-provision-single-device-linux-symmetric.md#install-iot-edge)に従って、最新バージョンに更新することをお勧めします。

## <a name="how-do-i-collect-logs-along-with-metrics"></a>メトリックと共にログを収集するにはどうすればよいですか?

[組み込みのログ プル機能](how-to-retrieve-iot-edge-logs.md)を使用できます。 組み込みのログ取得機能を使用するサンプル ソリューションは、[ **https://aka.ms/iot-elms**](https://aka.ms/iot-elms) で確認できます。

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>Azure portal のメトリック ページにデバイスのメトリックが表示されないのはなぜですか?

Azure Monitor の[ネイティブ メトリック](../azure-monitor/essentials/data-platform-metrics.md) テクノロジでは、Prometheus データ形式がまだ直接サポートされていません。 次の理由から、現時点ではログベースのメトリックの方が IoT Edge メトリックに適しています。

* 標準の *InsightsMetrics* テーブルを使用した Prometheus メトリック形式のネイティブ サポート。
* 視覚化とアラートを実現するための、[KQL](/azure/data-explorer/kusto/query/) による高度なデータ処理。

Azure portal の **[メトリック]** ではなく、 **[ログ]** ページにメトリックが表示される理由は、メトリック データベースとして Log Analytics を使用しているためです。

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>多層デプロイでメトリックコレクターを構成するにはどうすればよいですか?

メトリック コレクターには、サービス検出機能は含まれていません。 ベースまたは "*下位*" のデプロイ層にモジュールを含めることをお勧めします。 モジュールの構成内で、モジュールがデプロイされる可能性のあるすべてのメトリック エンドポイントを含めます。 モジュールが最終的なデプロイに表示されないが、そのエンドポイントがコレクションの一覧に表示される場合には、コレクターは収集を試み、失敗し、先に進みます。

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>カスタム メトリックを使用して監視ソリューションを拡張するにはどうすればよいですか?

[カスタム メトリック](how-to-add-custom-metrics.md)に関する記事を参照してください。

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>特定のメトリックが属しているデバイスを認識するにはどうすればよいですか?

メトリック ラベルでデバイス情報をエンコードします。 詳細については、「[名前付け規則](how-to-add-custom-metrics.md#naming-conventions)」を参照してください。

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>複数の IoT ハブからのデバイスにまたがるアラート ルールを作成するにはどうすればよいですか?

[アラート ルールを作成する](how-to-create-alerts.md#create-an-alert-rule)ときに、その[スコープ](how-to-create-alerts.md#select-alert-rule-scope)をリソース グループまたはサブスクリプションに変更できます。 すると、アラート ルールは、そのスコープ内のすべての IoT ハブに適用されます。

## <a name="alerts-arent-firing-when-they-should"></a>アラートが必要な時に発生しない

アラート ルールを作成するときに、プレビュー グラフを確認してアラート ロジックがトリガーされるのを確認します。

問題が見つからなかった場合は、**Log Analytics** サービスの [テクニカル サポート インシデント](https://azure.microsoft.com/support/create-ticket/)を作成します。

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>デバイスが監視ブックに表示されない

ブックでは、デバイス メトリックが *ResourceId* を使用して正しい IoT ハブまたは IoT Central アプリケーションにリンクされていることが必要とされます。 メトリックコレクターが、正しい [ResourceId](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) で *構成されている* ことを確認します。

メトリックコレクター モジュール ログを使用し、選択した時間範囲内にデバイスがメトリックを送信したことを確認します。

メトリックが表示される前に、数分のインジェスト遅延が発生する場合があることに注意してください。

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>バグが見つかったか、ブックに表示されているメトリックに関する質問がある

タイトルに '[monitor-workbook]' を含め、[Azure IoT Edge GitHub リポジトリ](https://github.com/azure/iotedge/issues)で issue を開きます。

ブックのテンプレートは、[GitHub で一般公開されています](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub)。 改善または修正に関する pull request をお待ちしています。

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>パブリック テンプレートにブックが表示されない

Log Analytics ワークスペースではなく、ポータルの IoT ハブまたは IoT Central アプリケーション ページで **[ブック]** ページを表示していることを確認します。

ブックがまだ表示されない場合は、実稼働前の Azure portal 環境を使用してみてください ([`https://ms.portal.azure.com`](https://ms.portal.azure.com))。 ブックの更新が運用環境に表示されるまで時間がかかることがありますが、その場合でも実稼働前の環境で利用できます。

:::zone-end