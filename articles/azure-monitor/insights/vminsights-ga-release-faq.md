---
title: Azure Monitor for VMs (GA) についてよく寄せられる質問 | Microsoft Docs
description: Azure Monitor for VMs は、Azure VM オペレーティング システムの正常性およびパフォーマンスの監視と、アプリケーション コンポーネントおよび他のリソースとの依存関係の自動検出を組み合わせ、それらの間の通信をマップする Azure のソリューションです。 この記事では、GA リリースに関する一般的な質問への回答を示します。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 88634777897341f4bd4d8c12b5f9d3b6d9982758
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671532"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor for VMs の一般提供 (GA) についてよく寄せられる質問

最近、[Azure の更新](https://azure.microsoft.com/blog/)ブログで、いくつかの計画変更が 2019 年 10 月と 11 月に行われることを発表しました。 これらの変更に関する詳細については、この一般提供に関する FAQ で説明されています。

## <a name="updates-for-azure-monitor-for-vms"></a>Azure Monitor for VMs 用の更新プログラム

11 月に Azure Monitor for VMs の新しいバージョンをリリースします。 このリリース後に Azure Monitors for VMs を有効にしたお客様は、新しいバージョンを自動的に受信しますが、既に Azure Monitor for VMs を使用している既存のお客様はアップグレードするように求められます。  この FAQ とドキュメントでは、複数のワークスペースとの間で大規模なデプロイを行っている場合に、一括アップグレードを実行するためのガイダンスを提供します。

このアップグレードにより、Azure Monitor for VMs のパフォーマンス データセットは、[コンテナーの Azure Monitor](container-insights-overview.md) と同じ `InsightsMetrics` テーブルに格納され、2 つのデータ セットのクエリを簡単に実行できるようになります。 また、以前に使用したテーブルに格納できない多様なデータ セットを格納することもできます。  この新しいテーブルを使用するように、パフォーマンス ビューも更新されます。

接続データ セットのために、新しいデータ型に移行します。 カスタム ログ テーブルを使用していて、現在 `ServiceMapComputer_CL` および `ServiceMapProcess_CL` に格納されているデータは、`VMComputer` と `VMProcess` という名前の専用のデータ型に移行されます。  専用のデータ型に移行することによって、これらの優先順位をデータ インジェストに与えることができ、テーブル スキーマがすべての顧客にわたって標準化されます。

Microsoft では、既存のお客様にアップグレードをお願いすることで、お客様のワークフローの中断が生じることを認識しています。このことが、一般提供リリースの後ではなく、パブリック プレビュー中の今、アップグレードをお願いする理由です。

## <a name="what-will-change"></a>変更内容

現在、Azure Monitor for VMs のオンボード プロセスを完了すると、監視データを格納するために選択したワークスペースで Service Map ソリューションを有効にし、Microsoft が、お使いの VM から収集したデータ用にパフォーマンス カウンターを構成します。 今後数週間で、**VMInsights**  という名前の新しいソリューションがリリースされる予定です。これには、データ収集のための追加機能と、Log Analytics にこのデータを格納するための新しい場所が含まれています。

ワークスペースでパフォーマンス カウンターを使用する現在のプロセスでは、Log Analytics の Perf テーブルにデータが送信されます。  この新しいソリューションでは、コンテナーの Azure Monitor によっても使用される `InsightsMetrics` という名前のテーブルにデータが送信されます。 このテーブル スキーマを使用すると、Perf テーブル形式と互換性のない追加のメトリックとサービス データ セットを格納できます。

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>VMInsights ソリューションをインストールした場合、自分のワークスペースのパフォーマンス カウンターには何が必要ですか?

Azure Monitor for VMs を有効にする現在のメソッドでは、ご利用のワークスペースでパフォーマンス カウンターを使用します。 新しいメソッドでは、このデータを `InsightsMetrics` という名前の新しいテーブルに格納します。

InsightsMetrics のデータを使用するように、ユーザー インターフェイスを更新したら、ドキュメントを更新し、Azure portal でのバナーの表示など、複数のチャネルを使ってこのお知らせを伝えます。 その時点で、これらの[パフォーマンス カウンター](vminsights-enable-overview.md#performance-counters-enabled)を使用する必要がなくなった場合は、ご利用のワークスペースで無効にすることができます。 

>[!NOTE]
>Perf テーブルにこれらのカウンターを参照するアラート ルールがある場合は、`InsightsMetrics` テーブル内の新しいデータを参照するように更新する必要があります。  このテーブルを参照する使用可能なログ クエリの例については、Microsoft のドキュメントを参照してください。
>

パフォーマンス カウンターを有効にしたままにする場合は、取り込まれたデータに対して課金され、[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/) に基づいて Perf テーブルに保持されます。

## <a name="how-will-this-change-affect-my-alert-rules"></a>この変更はアラート ルールにどのように影響しますか?

ワークスペースで有効になっていたパフォーマンス カウンターを対象にする `Perf` テーブルをクエリする[ログ アラート](../platform/alerts-unified-log.md)を作成した場合、代わりに `InsightsMetrics` テーブルを参照するように、これらのルールを更新する必要があります。 これらのデータ セットは `VMComputer` テーブルと `VMProcess` テーブルに移動するため、このガイダンスは `ServiceMapComputer_CL` と `ServiceMapProcess_CL` を使用したログ検索規則にも適用されます。

この FAQ とドキュメントを更新して、収集するデータ セットに対するログ検索のアラート ルールの例を含めます。

## <a name="will-there-be-any-changes-to-billing"></a>課金に変更はありますか?

課金は引き続き取り込まれたデータに基づいており、Log Analytics ワークスペースに保持されます。

収集するマシン レベルのパフォーマンス データは同じで、Perf テーブルに格納されているデータと同様のサイズになり、ほぼ同じ額のコストがかかります。

## <a name="what-if-i-only-want-to-use-service-map"></a>Service Map のみを使用する場合はどうなりますか?

問題はありません。  今後の更新プログラムについて Azure Monitor for VMs 表示するときに、Azure portal にプロンプトが表示されます。 リリースされると、新しいバージョンへの更新を要求するプロンプトが表示されます。 [Maps](vminsights-maps.md) 機能のみを使用する場合は、アップグレードしないことを選択して、Azure Monitor for VMs と Service Map ソリューションでワークスペースまたはダッシュボード タイルからアクセスした Maps 機能を使用し続けることができます。

ワークスペースでパフォーマンス カウンターを手動で有効にすることを選択した場合は、Azure Monitor から表示されたパフォーマンス グラフの一部にデータが表示されることがあります。 新しいソリューションがリリースされたら、`InsightsMetrics` テーブルの格納データのクエリを実行するようにパフォーマンス グラフを更新します。 これらのグラフにそのテーブルからのデータを表示したい場合は、新しいバージョンの Azure Monitor for VMs にアップグレードする必要があります。

`ServiceMapComputer_CL` と `ServiceMapProcess_CL` からデータを移動するための変更は、Service Map と Azure Monitor for VMs の両方に影響するため、この更新に対して引き続き計画する必要があります。

**VMInsights** ソリューションにアップグレードしないことを選択した場合は、`Perf` テーブルのデータを参照するレガシ バージョンのパフォーマンス ブックを引き続き提供します。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Service Map データ セットも InsightsMetrics に格納されますか?

両方のソリューションを使用する場合、データ セットは複製されません。 両方のオファリングでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` テーブルに格納されるデータ セットを共有して、収集するマップ データ セットを格納します。  

`InsightsMetrics` テーブルは、収集する VM、プロセス、サービス データ セットの格納に使用され、Azure Monitor for VMs を使用している場合にのみ設定されます。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>ワークスペースに Service Map と VMInsights のソリューションがある場合、二重に課金されますか?

いいえ、2 つのソリューションでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` に格納されているマップ データ セットを共有しています。  ワークスペースに両方のソリューションがある場合、このデータに対して二重に課金されることはありません。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Service Map または VMInsights ソリューションのいずれかを削除した場合、Log Analytics の自分のデータは削除されますか?

いいえ、2 つのソリューションでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` に格納されているマップ データ セットを共有しています。  ソリューションのいずれかを削除すると、これらのデータ セットでは、データを使用するソリューションが配置されたままで、Log Analytics に保持されていることがわかります。  Log Analytics ワークスペースから削除されるデータについては、ワークスペースから両方のソリューションを削除する必要があります。

## <a name="when-will-this-update-be-released"></a>この更新プログラムはいつリリースされますか?

Azure Monitor for VMs の更新プログラムは、11 月中旬にリリースされる予定です。 リリース日に近づくにつれて、ここに更新情報を投稿します。また、Azure Monitor に移動すると、Azure portal に通知が表示されます。

## <a name="health-feature-to-enter-limited-public-preview"></a>制限付きパブリック プレビューに入る正常性の機能

VM 正常性の機能セットに関して、お客様から多くのすばらしいフィードバックを受け取りました。  この機能にはさまざまな関心があり、監視ワークフローをサポートする可能性にご期待いただいています。 Microsoft では、機能を追加するための一連の変更を行い、お客様からお寄せいただいたフィードバックに対処する予定です。 これらの変更が新しい顧客に与える影響を最小限に抑えるために、この機能を制限付きパブリック プレビューに移行します。

この移行は 10 月上旬に開始され、10 月末までに完了します。

注目する領域の一部を次に示します。

- 正常性モデルとそのしきい値に対するコントロールの追加
- VM のスコープに適用される正常性モデルの大規模な作成
- 正常性ベースのアラート ルールを管理するためのアラート プラットフォームのネイティブでの使用
- 1 つ以上のサブスクリプションなど、より広い範囲にわたって正常性を表示する機能
- 正常性遷移とアラート通知での待機時間の削減

## <a name="how-do-existing-customers-access-the-health-feature"></a>既存のお客様が正常性機能にアクセスする方法

正常性機能を使用している既存のお客様は引き続きアクセスできますが、新しいお客様には提供されません。  

この機能にアクセスするには、次の機能フラグ `feature.vmhealth=true` をポータル URL [https://portal.azure.com](https://portal.azure.com) に追加します。 例: `https://portal.azure.com/?feature.vmhealth=true`。

次の短い URL を使用することもできます。これにより、機能フラグが自動的に設定されます: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

既存のお客様は、正常性機能を備えた既存のワークスペースのセットアップに接続されている VM 上で、正常性機能を使用し続けることができます。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>現在、1 つの環境で VM の正常性を使用しており、新しい環境にデプロイしたいと考えています

正常性機能を使用している既存のお客様であり、新しいロール アウトに使用する場合は、vminsights@microsoft.com にお問い合わせの上、指示を依頼してください。

## <a name="next-steps"></a>次の手順

ご利用の仮想マシンを監視する上で役立つ要件と方法については、[Azure Monitor for VMs のデプロイ](vminsights-enable-overview.md)に関するページをご覧ください。
