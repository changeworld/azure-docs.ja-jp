---
title: カスタム メトリックを追加する方法 - Azure IoT Edge
description: カスタム モジュールのシナリオ固有のメトリックを使用して組み込みのメトリックを強化する
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8e9c5b74b19af00228f03b26450b987d87283376
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015489"
---
# <a name="add-custom-metrics-preview"></a>カスタム メトリックを追加する (プレビュー)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

システム モジュールによって提供される組み込みのメトリックに加えて、IoT Edge モジュールからカスタム メトリックを収集します。 [組み込みのメトリック](how-to-access-built-in-metrics.md)は、デプロイの正常性を把握するための優れた基準を提供します。 ただし、画像を完成させるには、カスタム モジュールの追加情報が必要になる場合があります。 カスタム モジュールは、適切な [Prometheus クライアント ライブラリ](https://prometheus.io/docs/instrumenting/clientlibs/)を使用してメトリックを生成することで、監視ソリューションに統合できます。 この追加情報を使用すると、要件に特化した新しいビューやアラートを有効にすることができます。

## <a name="sample-modules-repository"></a>サンプル モジュール リポジトリ

メトリックを生成するためにインストルメント化されたカスタム モジュールの例については、[azure-samples リポジトリ](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics)を参照してください。 選択した言語のサンプルがまだ使用できない場合でも、一般的なアプローチが役に立ちます。

## <a name="naming-conventions"></a>名前付け規則

一般的なガイダンスについては、Prometheus のドキュメントの[ベスト プラクティス](https://prometheus.io/docs/practices/naming/)を参照してください。 次の追加のレコメンデーションは、IoT Edge シナリオに役立ちます。

* メトリック名の先頭にモジュール名を含めて、どのモジュールがメトリックを生成したかを明確にします。

* IoT Hub 名または IoT Central アプリケーション名、IoT Edge デバイス ID、モジュール ID をラベル ("*タグ*"/"*ディメンション*" とも呼ばれる) としてすべてのメトリックに含めます。 この情報は、IoT Edge エージェントによって開始されたすべてのモジュールで環境変数として使用できます。 このアプローチは、サンプル リポジトリの例で[示されて](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49)います。 このコンテキストがないと、特定のメトリック値を特定のデバイスに関連付けることはできません。

* ラベルにインスタンス ID を含めます。 インスタンス ID には、モジュールの起動時に生成される [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) のような一意の ID を指定できます。 インスタンス ID 情報は、バックエンドでモジュールのメトリックを処理するときに、モジュールの再起動を調整するのに役立ちます。

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>カスタム メトリックを収集するようにメトリック コレクターを構成する

カスタム モジュールでメトリックを生成したら、次の手順は、カスタム メトリックを収集して転送するように[メトリック コレクター モジュール](how-to-collect-and-transport-metrics.md#metrics-collector-module)を構成することです。

環境変数 `MetricsEndpointsCSV` を更新して、カスタム モジュールのメトリック エンドポイントの URL を含める必要があります。 環境変数を更新するときは、[メトリック コレクターの構成](how-to-collect-and-transport-metrics.md#metrics-collector-configuration)の例で示されているように、システム モジュールのエンドポイントを必ず含めてください。

>[!NOTE]
>既定では、カスタム モジュールのメトリック エンドポイントは、メトリック コレクターがアクセスできるようにホスト ポートにマップする必要はありません。 明示的にオーバーライドされない限り、Linux では、両方のモジュールが、*azure-iot-edge* という名前の [ユーザー定義の Docker ブリッジ ネットワーク](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)で開始されます。
>
>ユーザー定義の Docker ネットワークには、モジュール (コンテナー) 名を使用したモジュール間通信を可能にする既定の DNS リゾルバーが含まれています。 たとえば、*module1* というカスタム モジュールが、http ポート *9600* でパス */metrics* のメトリックを生成している場合、エンドポイント *http://module1:9600/metrics* から収集するようにコレクターを構成する必要があります。

IoT Edge デバイスで次のコマンドを実行して、http ポート *9600* のカスタム モジュールによってパス */metrics* で生成されたメトリックにアクセスできるかどうかをテストします。

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>カスタムの視覚化を追加する

Log Analytics でカスタム メトリックを受け取ると、カスタムの視覚化とアラートを作成できます。 監視ブックを拡張して、クエリを使用した視覚化を追加することができます。

すべてのメトリックは、IoT Hub または IoT Central アプリケーションのリソース ID に関連付けられています。 そのため、バッキング Log Analytics ワークスペースではなく、関連する IoT Hub または IoT Central アプリケーションの **[ログ]** ページから、カスタム メトリックが正しく取り込まれているかどうかを確認できます。 次の基本的な KQL クエリを使用して確認します。

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

インジェストを確認したら、新しいブックを作成するか、既存のブックを拡張することができます。 [ブックのドキュメント](../azure-monitor/visualize/workbooks-overview.md)とキュレーション [IoT Edge ブック](how-to-explore-curated-visualizations.md)のクエリをガイドとして使用します。

結果に満足した場合は、チームと[ブックを共有](../azure-monitor/visualize/workbooks-access-control.md)したり、組織のリソース デプロイの一部として[プログラムでデプロイ](../azure-monitor/visualize/workbooks-automate.md)したりできます。

## <a name="next-steps"></a>次のステップ

[キュレーション ブック](how-to-explore-curated-visualizations.md)を使用して、その他のメトリックの視覚化オプションを確認します。
