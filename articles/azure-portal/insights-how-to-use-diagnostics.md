<properties 
	pageTitle="監視と診断の有効化" 
	description="Azure でリソースの診断を設定する方法について説明します。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="stepsic"/>

# 監視と診断の有効化

[Azure ポータル](http://portal.azure.com)では、リソースに関する監視と診断の頻度の高い豊富なデータを構成することができます。また、[REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用して、プログラムで診断を構成することもできます。

Azure の診断、監視、およびメトリック データは、選択したストレージ アカウントに保存されます。これにより、ストレージ エクスプローラーから、Power BI、サード パーティ製ツールまで、どれでも好きなツールを使用してデータを読み取ることができます。

## リソースを作成する場合

ほとんどのサービスでは、[Azure ポータル](http://portal.azure.com)でサービスを最初に作成するときに診断を有効にすることができます。

1. **[新規]** に移動し、興味のあるリソースを選択します。 

2. **[オプションの構成]** を選択します。![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. **[診断]** を選択して、**[オン]** をクリックします。診断の保存先となるストレージ アカウントを選択する必要があります。診断データをストレージ アカウントに送信する際には、ストレージおよびトランザクションの通常のデータ料金が発生します。

4. **[OK]** をクリックしてリソースを作成します。

## 既存のリソースの設定の変更

リソースを既に作成済みで、診断設定を変更する (データ コレクションのレベル変更など) 必要がある場合は、Azure ポータルで実行できます。

1. リソースに移動して、**[設定]** コマンドをクリックします。

2. **[診断]** を選択します。

3. **[診断]** ブレードには、そのリソースに可能な診断と監視コレクション データが表示されます。リソースによっては、データの **[保持] **ポリシーを選択して、ストレージ アカウントからクリーンアップすることもできます。![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 設定を選択したら、**[保存]** コマンドをクリックします。監視データは、初めて有効にする場合に表示されるまで少し時間がかかることがあります。

### 仮想マシンのデータ収集のカテゴリ
仮想マシンでは、メトリックとログがすべて 1 分間隔で記録されるので、ご使用のマシンに関する情報はいつでも最新のものとなります。

- **基本メトリック**: プロセッサやメモリなどの仮想マシンについての正常性のメトリック 
- **ネットワークおよび Web メトリック**: ネットワーク接続と Web サービスに関するメトリック
- **.NET メトリック**: 仮想マシン上で実行されている .NET アプリケーションおよび ASP.NET アプリケーションについてのメトリック
- **SQL メトリック**: Microsoft SQL Service を実行している場合はそのパフォーマンス メトリック
- **Windows イベント アプリケーション ログ**: アプリケーション チャネルに送信される Windows イベント
- **Windows イベント システム ログ**: システム チャネルに送信される Windows イベントこれには、[Microsoft マルウェア対策](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)からのすべてのイベントも含まれます。 
- **Windows イベント セキュリティ ログ**: セキュリティ チャネルに送信される Windows イベント
- **診断インフラストラクチャ ログ**: 診断コレクション インフラストラクチャについてのログ
- **IIS ログ**: IIS サーバーについてのログ

現時点では、Linux の特定のディストリビューションはサポートされていません。また、ゲスト エージェントを仮想マシンにインストールする必要があります。

## 次のステップ

* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](insights-receive-alert-notifications.md)ます。
* [サービス メトリックスを監視](insights-how-to-customize-monitoring.md)して、サービスの可用性と応答性を確認します。
* [インスタンス数を自動的にスケール変更](insights-how-to-scale.md)して、必要に応じてサービスのスケールが変更されるようにします。
* コードがクラウドで実行される方法を正確に理解するには、[アプリケーションのパフォーマンスを監視](insights-perf-analytics.md)します。
* [イベントと監査ログを表示](insights-debugging-with-events.md)して、サービスで発生したすべてのことを理解します。
* [サービス正常性を追跡](insights-service-health.md)して、Azure でパフォーマンスの低下やサービスの中断が発生したことを検出します。 
 

<!---HONumber=62-->