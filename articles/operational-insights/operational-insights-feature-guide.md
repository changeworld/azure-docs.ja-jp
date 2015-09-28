<properties
	pageTitle="Operational Insights 機能ガイド"
	description="Operational Insights は分析サービスで、IT 管理者は、オンプレミス環境やクラウド環境を詳しく把握できるようになります。これによりリアルタイムのデータやマシンの履歴データとやり取りし、独自の洞察を迅速に作成したり、データ分析用にMicrosoft やコミュニティで開発されたパターンを提供できるようになります。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015"
	ms.author="banders"/>

# Operational Insights 機能ガイド

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

この記事では、このガイドでは、Operational Insights で解決できる問題、Operational Insights 環境の構成、および Operational Insights アカウントを作成してサービスにサインインする方法について説明します。

## マシン データを変換する

Operational Insights は分析サービスで、IT 管理者は、オンプレミス環境やクラウド環境を詳しく把握できるようになります。これによりリアルタイムのデータやマシンの履歴データとやり取りし、独自の洞察を迅速に作成したり、データ分析用にMicrosoft やコミュニティで開発されたパターンを提供できるようになります。

Operational Insights では、次の機能を使用して、マシン データを運用上のインテリジェンスに変換できます。


|**アイコン** | **使用目的** | **説明**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [容量計画](operational-insights-capacity.md) | Microsoft Azure Operational Insights の容量管理ソリューションを使用して、サーバー インフラストラクチャの容量を効率よく把握できます。 |
| ![](./media/operational-insights-feature-guide/update.png) | [システムの更新の評価](operational-insights-updates.md) | Microsoft Azure Operational Insights で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。 |
| ![](./media/operational-insights-feature-guide/log-search.png) | [ログ検索](operational-insights-search.md) | ログ検索機能を使用して、検索結果に対して変換やフィルター処理、レポート作成などを実行するクエリを作成できます。検索では、Operational Insights 全体でログを検索するためにイベント データと IIS ログが使用されます。 |
| ![](./media/operational-insights-feature-guide/malware.png) | [マルウェアの評価](operational-insights-antimalware.md) | Microsoft Azure Operational Insights のマルウェア対策ソリューションを使用して、インフラストラクチャ内のサーバーをマルウェアから保護できます。 |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [セキュリティと監査](operational-insights-security-audit.md) | セキュリティと監査ソリューションを使用して、注意する必要がある重要な問題を検索する組み込みの検索クエリによって、組織の IT セキュリティ対策の包括的なビューを取得できます。 |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Active Directory と SQL の評価](operational-insights-assessment.md) | 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。 |
| ![](./media/operational-insights-feature-guide/alert.png) | [アラートの管理](operational-insights-alerts.md) | 警告管理ソリューションを使用して、Operations Manager によって監視されているサーバーからの警告を管理できます。 |


さらに、以下を実行できます。

- **マシン データをシステムに送信します。エージェントを使用するかどうか、System Center Operations Manager と連携させるかどうかを選択できます。**詳細については、次を参照してください。
	- [System Center Operations Manager から Operational Insights に接続する](operational-insights-connect-scom.md)
	- [Operational Insights にコンピューターを直接接続する](operational-insights-direct-agent.md)
	- [Microsoft Azure でサーバーのデータを分析する](operational-insights-analyze-data-azure.md)
- **上記のすべてをモバイル アプリケーションを使用して出先で実行します。**
	- Windows Phone アプリケーションの詳細については、[Operational Insights モバイル アプリ](http://www.windowsphone.com/ja-JP/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)に関するページを参照してください。

## オペレーション インサイト環境

オペレーション インサイト環境は次で構成されています。

- Azure アカウントのコンテナーとなる Microsoft Azure でホストされるワークスペース
- クラウドでホストされるオペレーション インサイト Web サービス
- Web サービスに直接接続する個別エージェント
- または、System Center Operations Manager に関連付けられているサービス (必須ではありません)


System Center Advisor と呼ばれる以前のバージョンのオペレーション インサイトを使用した場合は、ローカル環境に Advisor ソフトウェアがインストールされている可能性があります。ですが Advisor ソフトウェアはオペレーション インサイトではサポートされていません。

Operations Manager サービスとしてオペレーションインサイト ソフトウェアを使用する場合、1 つ以上の管理グループと管理グループごとに少なくとも 1 つのエージェントが構成されます。Operations Manager エージェントは、サーバーからデータを収集し、ソリューションを使用して分析します (ソリューションは System Center Operations Manager の管理パックに似ています)。データは、Operations Manager から Operational Insights Web サービスに定期的に送信され (必要であればプロキシ サーバー経由で送信され)、Operations Manager データベースには何も保存されないため、負荷が追加されることはありません。

同様に、個々のコンピューターにインストールされたエージェントは Web サービスに直接接続し、収集したデータを送信し処理できます。

各ソリューションのデータは分析され、インデックスが作成され、Operational Insights ポータルに表示されます。アラートや関連する修復ガイダンス、構成の評価、インフラストラクチャの容量の問題、システムの更新ステータス、マルウェア対策の警告、ログ データを表示できます。さらに、詳細なアドホック ログ検索と調査を実行できます。

![Operational Insights の概要図](./media/operational-insights-feature-guide/environment.png)

### Operational Insights を利用できる地域
Microsoft Azure Operational Insights は米国内でホストされています。Operational Insights の言語は英語ですが、サービスは英語圏以外の多数の市場で使用できます。詳細については、「[世界各国/地域でのご利用の可否](http://go.microsoft.com/fwlink/?LinkId=229842)」をご覧ください。

<!---HONumber=Sept15_HO3-->