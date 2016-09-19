<properties
	pageTitle="Microsoft Azure での監視の概要 | Microsoft Azure"
	description="アラート、webhook、自動スケールなど Microsoft Azure での監視と診断の最上位レベルの概要。"
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="robb"/>

# Microsoft Azure での監視の概要

この記事では、Azure リソースの監視について、概念的側面から概要を説明します。また、特定のタイプに該当するリソースについては詳しい情報のある場所も紹介しています。Azure 以外の観点からアプリケーションを監視する場合の概要については、「[監視と診断のガイダンス](../best-practices-monitoring.md)」をご覧ください。

クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。そのような洞察は、パフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするために役立ちます。

次の図は、Azure での監視の概念、収集できるログの種類、データを使用して行うことができることを示しています。

![非コンピューティング リソースに対する監視と診断の論理モデル](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

図 1: 非コンピューティング リソースに対する監視と診断の概念モデル

<br/>

![コンピューティング リソースに対する監視と診断の論理モデル](./media/monitoring-overview/monitoring-azure-resources-compute.png)

図 2: コンピューティング リソースに対する監視と診断の概念モデル


## 監視のソース
### インフラストラクチャ アクティビティ ログ
リソースの情報については、Azure インフラストラクチャで確認できるものと同じ監査ログを検索できます (監査ログは操作ログまたはアクティビティ ログと呼ばれることもあります)。このログには、リソースが作成された時点や破棄された時点などの情報が記載されています。

### ホスト VM
**コンピューティングのみ**

現時点では、ホスト VM に関するメトリックを収集することはできません。この内容は、情報の網羅性を確保するために記載しています。

Cloud Services、Virtual Machines、Service Fabric など、一部のコンピューティング リソースには、やり取りの相手となる専用のホスト VM が存在します。ホスト VM は、Hyper-V ハイパーバイザー モデルのルート VM と同じものです。

他の Azure サービスでは、リソースと特定のホスト VM の間に必ずしも 1 対 1 のマッピングが存在するわけではありません。


### リソース - メトリックと診断ログ
収集可能なメトリックは、リソースのタイプによって異なります。たとえば、Virtual Machines は、ディスク IO や CPU 使用率に関する統計情報を提供します。しかし、Service Bus キューにはこのような統計情報が存在しません。キューでは、代わりにキューのサイズやメッセージのスループットのようなメトリックが提供されます。

コンピューティング リソースでは、ゲスト OS のほか、Azure 診断のような診断モジュールのメトリックが得られます。Azure 診断は、診断データを収集し、Azure Storage をはじめとする他の場所にルーティングする際に役立ちます。

### アプリケーション - 診断ログ、アプリケーション ログ、メトリック
**コンピューティングのみ**

コンピューティング リソース用のモデルに示したとおり、アプリケーションはゲスト OS で実行できます。各アプリケーションからは、それぞれに固有のログとメトリックのセットが出力されます。

メトリックのタイプには、以下のものがあります。

- パフォーマンス カウンター
- Application Logs
- Windows イベント ログ
- .NET イベント ソース
- IIS ログ
- マニフェスト ベースの ETW
- クラッシュ ダンプ
- カスタム エラー ログ


## 監視データの用途

### 視覚化
グラフィックスやチャートを使って監視データを視覚化すると、データ自体を見るよりも格段に早く傾向を把握できます。

視覚化の方法には、次のようなものがあります。

- Azure ポータルの使用
- Azure Application Insights にデータをルーティングする
- Microsoft PowerBI にデータをルーティングする
- ライブ ストリーミングを使ってサード パーティ製の視覚化ツールにデータをルーティングするか、ツールに Azure Storage のアーカイブからデータを読み込ませる

### アーカイブ
監視データは通常、Azure ストレージに書き込まれ、削除するまでの間保持されます。

このデータを使用する方法はいくつかあります。

- データが書き込まれたら、それを Azure の内部または外部の他のツールに読み込ませ、処理する。
- データをローカルにダウンロードしてアーカイブするか、クラウドのアイテム保持ポリシーを変更して、データを長期間保持する。
- Azure ストレージにいつまでもデータを置いておくAzure Storage で保持しているデータの量に応じて料金の支払いが必要になりますのでご注意ください。

### クエリ
Insights REST API、共通言語インターフェイス (CLI) コマンド、PowerShell コマンドレット、または .NET SDK を使えば、システム ストレージまたは Azure Storage 内のデータにアクセスできます。

たとえば、次のようになります。

-  独自に作成したカスタム監視アプリケーションのためのデータを取得する
-  カスタム クエリを作成し、そのデータをサード パーティ製アプリケーションに送信する

### ルート
監視データは他の場所にリアルタイムでストリーミングできます。

たとえば、次のようになります。

- データを Application Insights に送り、そこで視覚化ツールを使用する
- データを Event Hubs に送り、さらにリアルタイム分析を実行するサード パーティ製ツールにルーティングする

### 自動化
イベントはもとより、プロセス全体をトリガーする場合にも、監視データを利用できます。以下に例を示します。

- データを使用し、アプリケーションの負荷に応じてコンピューティング インスタンスを自動でスケールアップまたはスケールダウンする
- メトリックが事前に定義されているしきい値に達したときにメールを送信する
- Web URL (webhook) を呼び出して Azure 外部のシステムでアクションを実行する
- Azure Automation で Runbook を開始し、さまざまなタスクを実行する

## 使用方法
一般に、次のいずれかの方法で、データの追跡、ルーティング、および取得の操作を行うことができます。ただし、アクションやデータの種類によっては利用できない方法もあります。

- [Azure ポータル](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [クロスプラットフォーム コマンド ライン インターフェイス (CLI)](insights-cli-samples.md)
- [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Azure の監視ソリューション
Azure では、ベアメタル インフラストラクチャからアプリケーション テレメトリに至るまで、サービスを監視するためのソリューションをいくつか用意しています。最善の監視戦略は、サービスの正常性に関して包括的かつ詳細な洞察を得るために、以下の 3 つをすべて使用することです。

- [Azure Insights (プラットフォーム) による監視](http://aka.ms/azmondocs) – Azure インフラストラクチャ (アクティビティ ログ) と個々の Azure リソース (診断ログ) から得られたデータを基に視覚化、クエリ、ルーティング、アラート、自動スケール、自動化を行います。この記事は、Azure Insights ドキュメントの一部です。
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – Azure の監視により得られたデータを活用して、サービスのアプリケーション レイヤーの問題を検出および診断するものです。
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) ([Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) の一部) – オンプレミスとクラウドベースのどちらのインフラストラクチャにも使える総合的 IT 管理ソリューションであり、Azure リソースからログの検索や分析などを行えます。


## 次のステップ
項目ごとに詳しい情報を確認できます。

- [Azure Insights](http://aka.ms/azmondocs) 左側にある目次のリンクからさまざまなトピックに飛べるほか、リンク先のページではビデオを視聴できます。
- [Azure 診断](../azure-diagnostics.md) Cloud Services、Virtual Machines、または Service Fabric のアプリケーションに発生した問題を診断する場合には、こちらをご覧ください。
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
- [Azure Storage のトラブルシューティング](../storage/storage-e2e-troubleshooting.md) Storage Blobs、テーブル、またはキューを使用している場合には、こちらをご覧ください。
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) と [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0907_2016-->