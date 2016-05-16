<properties
	pageTitle="ソリューション ギャラリーから Log Analytics ソリューションを追加する | Microsoft Azure"
	description="Log Analytics ソリューションには、特定の問題点に関するメトリックを提供するロジックやビジュアライゼーション、データ取得の規則が集約されています。"
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
	ms.date="04/28/2016"
	ms.author="banders"/>

# ソリューション ギャラリーから Log Analytics ソリューションを追加する

Log Analytics ソリューションには、特定の問題点に関するメトリックを提供する**ロジック**や**ビジュアライゼーション**、**データ取得の規則**が集約されています。この記事では、Log Analytics でサポートされるソリューションを示し、ソリューション ギャラリーを使用してそれらを追加および削除する方法を説明します。

ソリューションを使用すると、次の点についてより深い知識が得られます。
- 運用上の問題を迅速に調査して解決する
- さまざまな種類のマシン データを収集して関連付ける
- 容量計画や修正プログラムの状況報告、セキュリティ監査などのアクティビティに早い段階で対応する


>[AZURE.NOTE] OMS には基本的なログ検索機能が含まれているので、ログ検索を有効にするためにソリューションをインストールする必要はありません。ただし、ソリューション ギャラリー ページからソリューションを追加することで、追加の機能を入手できます。

ソリューションを追加すると、データはインフラストラクチャのサーバーから収集され、OMS サービスに送信されます。OMS サービスによる処理は数分から数時間かかることがあります。サービスによってデータが処理されると、OMS でそのデータを表示できます。

不要になったソリューションは、簡単に削除できます。ソリューションを削除すると、データは OMS に送信されなくなります。それにより、1 日あたりのクォータがある場合は、クォータに計上されるデータの量が減少します。


## Microsoft Monitoring Agent でサポートされているソリューション

現時点では、Microsoft Monitoring Agent を使用して OMS に直接接続されているサーバーでは、次のような利用可能なソリューションの多くを使用できます。

- システムの更新
- マルウェア対策
- 変更の追跡
- SQL の評価
- Active Directory の評価
- アラートの管理 (SCOM アラートを含まない)

ただし、次のソリューションは、Microsoft Monitoring Agent でサポート*されておらず*、System Center Operations Manager (SCOM) エージェントが必要です。

- 容量管理
- アラートの管理 (SCOM アラートを含む)
- 構成の評価

SCOM エージェントを Log Analytics に接続する方法については、「[Connecting Operations Manager to Log Analytics](log-analytics-om-agents.md)」 (Operations Manager を Log Analytics に接続する) を参照してくだい。

### ソリューション ギャラリーを使用してソリューションを追加するには

1. OMS の [概要] ページで、**[ソリューション ギャラリー]** タイルをクリックします。![ソリューション ギャラリー](./media/log-analytics-add-solutions/sol-gallery.png)
2. OMS ソリューション ギャラリー ページでは、利用可能な各ソリューションの詳細を確認できます。OMS に追加するソリューションの名前をクリックします。
3. 選択したソリューションのページに、ソリューションに関する詳細情報が表示されます。**[追加]** をクリックします。
4. 追加したソリューションの新しいタイルが、OMS の [概要] ページに表示されます。OMS サービスによってデータが処理されると、そのタイルが使用できるようになります。

## ソリューションを構成するには
1. ソリューションの中には、構成が必要なものもあります。たとえば、Automation ソリューション、Azure Site Recovery ソリューション、および Backup ソリューションを使用するには、事前に構成する必要があります。
2. [概要] ページで、ソリューションのタイルをクリックします。![ソリューションの構成](./media/log-analytics-add-solutions/configure-additional.png)
3. 次に、必要な情報を使用してソリューションを構成し、**[保存]** をクリックします。![ソリューションの構成](./media/log-analytics-add-solutions/configure.png)

### ソリューション ギャラリーを使用してソリューションを削除するには

1. OMS の [概要] ページで、**[設定]** タイルをクリックします。
2. [設定] ページの [ソリューション] タブで、削除するソリューションの **[削除]** をクリックします。
3. 確認ダイアログで **[はい]** をクリックすると、ソリューションが削除されます。

## OMS の機能とソリューションにおけるデータ収集の詳細

次の表は、OMS の機能とソリューションにおけるデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

|データ型| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|---|
|AD 評価|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 日|
|AD レプリケーションの状態|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 日|
|アラート (Nagios)|Linux|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|着信時|
|アラート (Zabbix)|Linux|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 分|
|アラート (Operations Manager)|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 分|
|マルウェア対策|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|容量管理|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|変更の追跡|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|変更の追跡|Linux|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|時間単位|
|構成の評価 (従来の Advisor)|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 日 2 回|
|ETW|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|IIS ログ|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|ネットワーク セキュリティ グループ|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分|
|Office 365|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|通知時|
|パフォーマンス カウンター|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|スケジュールに基づく頻度 (間隔は 10 秒以上)|
|パフォーマンス カウンター|Linux|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|スケジュールに基づく頻度 (間隔は 10 秒以上)|
|Service Fabric|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|SQL の評価|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 日|
|SurfaceHub|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|着信時|
|Syslog|Linux|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合)|
|システムの更新|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後|
|Windows セキュリティ イベント ログ|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)| 10 分 (Azure Storage の場合) または着信時 (エージェントの場合)|
|Windows ファイアウォール ログ|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)| 着信時|
|Windows イベント ログ|Windows|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合)|
|送信データ|Windows (2012 R2/8.1 以降)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)| 1 分ごと|


## 次のステップ

- [ログを検索](log-analytics-log-searches.md)して、ソリューションによって収集された詳細情報を確認します。

<!---HONumber=AcomDC_0504_2016-->