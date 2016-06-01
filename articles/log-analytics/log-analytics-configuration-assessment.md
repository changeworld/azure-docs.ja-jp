<properties
	pageTitle="Log Analytics の構成評価ソリューション | Microsoft Azure"
	description="Operations Manager エージェントまたは Operations Manager 管理グループを使用する際、Log Analytics の構成評価ソリューションによって、System Center Operations Manager サーバー インフラストラクチャの現在の状態に関する詳細な情報が得られます。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="banders"/>

# Log Analytics の構成評価ソリューション

Log Analytics の構成評価ソリューションは、アラートとサポート技術情報に基づく推奨事項によって潜在的なサーバー構成の問題を見つけるために役立ちます。

このソリューションには、System Center Operations Manager が必要です。直接接続型のエージェントのみを使用している場合、構成評価は利用できません。

構成評価ソリューションの一部の情報は、ブラウザーに Silverlight プラグインをインストールしなければ表示されません。

>[AZURE.NOTE] 構成評価ソリューションは米国東部リージョンのワークスペースでのみご利用いただけます。他のリージョンには追加されません。構成評価ソリューションの機能は、Active Directory の評価や SQL Server の評価など、ワークロード別の評価機能に置き換えられる予定です。

![Configuration Assessment tile](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

構成データは、監視対象のサーバーから収集され、クラウド内の OMS サービスに送信されて処理されます。受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。それらのサーバーに関して処理されたデータが次の観点から表示されます。

- **アラート:** 監視対象のサーバーに対する構成に関連した事前対応型のアラートを表示します。これらは、Microsoft Customer and Support (Microsoft 顧客とサポート) 組織 (CSS) が現場からのベスト プラクティスで作成したルールによって生成されます。
- **サポート技術情報に基づく推奨事項:** インフラストラクチャ内に存在するワークロードについて、Microsoft サポート技術情報の推奨記事が表示されます。推奨記事は、実際の構成から Machine Learning を通じて自動的に提案されます。
- **解析対象のサーバーとワークロード:** 現在 OMS によって監視されているサーバーとワークロードが表示されます。

![Configuration Assessment dashboard](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### 構成評価で分析できるテクノロジ

OMS の構成評価は、以下のワークロードを分析します。

- Windows Server 2012 と Microsoft HYPER-V Server 2012
- 以下を含む Windows Server 2008 と Windows Server 2008 R2
    - Active Directory
	- HYPER-V ホスト
	- 汎用オペレーティング システム
- SQL Server 2008 以降
    - SQL Server データベース エンジン
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 と Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 と Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

SQL Server では、分析用に次の 32 ビットと 64 ビット エディションがサポートされています。

- SQL Server 2016 - すべてのエディション
- SQL Server 2014 - すべてのエディション
- SQL Server 2008 および 2008 R2 - すべてのエディション

サポートされているすべてのエディションで、SQL Server データベース エンジンが分析されます。また、WOW64 実装で実行する場合は、SQL Server の32 ビット エディションがサポートされています。

## ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

- Operations Manager は、構成評価ソリューションに必須です。
- 構成を評価する各コンピューターに、 Operations Manager エージェントが必要です。
- 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って構成評価ソリューションを OMS ワークスペースに追加します。 さらに手動で構成する必要はありません。

## 構成評価データ収集の詳細

次の表は、構成評価におけるデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|
|Windows|![いいえ](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![あり](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![なし](./media/log-analytics-configuration-assessment/oms-bullet-red.png)| ![はい](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![はい](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| 1 日 2 回|


## 構成評価アラート
[アラート] ページで、構成評価のアラートを表示して管理できます。アラートは、検出された問題とその原因、および問題の解決方法を通知します。パフォーマンスの問題を引き起こす可能性のある環境内の構成設定についての情報も得られます。

![alerts view](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] 構成評価アラートは、Log Analytics の他のアラートとは異なります。アラートを表示するには、ブラウザーに Silverlight プラグインをインストールする必要があります。

[アラート] ページの項目や項目のカテゴリを選択すると、サーバーまたはワークロードの一覧と、各項目に適用されるアラートが表示されます。

![alert list](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

各アラートには、マイクロソフト サポート技術情報の記事へのリンクが含まれます。この記事で、そのアラートに関する追加情報が得られます。

>[AZURE.TIP] 既定では、表示されるアラートの最大数は 2,000 です。さらにアラートを表示するには、アラートの一覧の上にある通知バーをクリックします。

一覧の項目をクリックすると、アラートが生成された問題の原因を解決するために役立つサポート技術情報記事が表示されます。

![KB article](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

特定のルールまたはルールのクラスを無視するアラート ルールを管理することができます。

![manage alert rules](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## サポート技術情報の推奨記事
サポート技術情報の推奨記事を表示すると、そのワークロードとコンピューターに対して推奨される Microsoft サポート技術情報記事の検索結果が一覧表示され、アラートに関する追加情報を得ることができます。

![search results for knowledge recommendations](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## 分析したサーバーとワークロード
サポート技術情報の推奨記事を表示すると、ログ検索結果に、OMS の Operations Manager が認識するすべてのサーバーとワークロードが一覧表示されます。

![Servers and workloads](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## 次のステップ

- [Log Analytics のログ検索機能](log-analytics-log-searches.md)を使用して、詳細な構成評価データを確認してください。

<!---HONumber=AcomDC_0518_2016-->