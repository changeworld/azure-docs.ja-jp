---
title: "Log Analytics の変更の追跡ソリューション | Microsoft Docs"
description: "環境内のソフトウェアや Windows サービスに対して行われた変更は、Log Analytics の構成変更の追跡ソリューションを使用して容易に特定できます。こうした構成の変更を特定することによって、運用上の問題点をピンポイントで突き止めることができます。"
services: operations-management-suite
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 56faeccbb25d2f0e3dbe6b104b5e9bc95a06ca36


---
# <a name="change-tracking-solution-in-log-analytics"></a>Log Analytics の変更の追跡ソリューション
この記事では、Log Analytics の変更の追跡ソリューションを使用して、環境の変更箇所を簡単に識別する方法を説明します。 このソリューションは、ソフトウェア、Windows サービス、Linux デーモン、および Linux パッケージに対する変更を追跡します。 構成の変更を識別することで、運用上の問題を特定できるようになります。 特定の Windows ファイルに対する変更を追跡するようにソリューションを構成することもできます。

このソリューションをインストールすると、インストールしたエージェントの種類が更新されます。 監視対象サーバーにインストールされているソフトウェア、Windows サービス、および Linux デーモンの変更が読み取られた後、そのデータがクラウドの Log Analytics サービスに送信され、処理されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。 変更が見つかると、変更があるサーバーが [変更の追跡] ダッシュボードに表示されます。 [変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* 変更を監視する各コンピューターに、[Windows](log-analytics-windows-agents.md)、[Operations Manager](log-analytics-om-agents.md)、または [Linux](log-analytics-linux-agents.md) エージェントが必要です。
* 「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って変更の追跡ソリューションを OMS ワークスペースに追加します。  さらに手動で構成する必要はありません。

### <a name="configure-windows-files-to-track"></a>追跡する Windows ファイルの構成
次の手順を使用して、Windows コンピューター上で追跡するファイルを構成します。

1. OMS ポータルで、**[設定]** (歯車アイコン) をクリックします。
2. **[設定]** ページで **[データ]** をクリックし、**[Windows ファイルの追跡]** をクリックします。
3. [Windows ファイルの変更追跡] の下に、追跡するファイルのファイル名を含むパス全体を入力して、**[追加]** の記号をクリックします。 例えば、C:\Program Files (x86)\Internet Explorer\iexplore.exe または C:\Windows\System32\drivers\etc\hosts のように入力します。
4. [ **Save**] をクリックします。  
   ![Windows ファイルの変更追跡](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="limitations"></a>制限事項
現在、変更の追跡ソリューションでは以下に対応していません。

* フォルダー (ディレクトリ)
* 再帰
* ワイルド カード
* パス変数
* ネットワーク ファイル システム

その他の制限事項:

* **[最大ファイル サイズ]** 列と値は現在の実装では使用されません。
* 30 分間の収集サイクルで収集するファイル数が 2500 を超えると、ソリューションのパフォーマンスが低下する可能性があります。
* ネットワーク トラフィックが高いとき、変更レコードが表示されるまでに最長で 6 時間かかることがあります。
* コンピューターのシャットダウン中に構成を変更した場合、そのコンピューターからは前の構成に対応するファイル変更が送信される可能性があります。

## <a name="change-tracking-data-collection-details"></a>変更の追跡データ収集の詳細
変更の追跡では、有効になっているエージェントを使用して、ソフトウェア インベントリと Windows サービス メタデータを収集します。

次の表は、変更の追跡におけるデータの収集手段と、データ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Linux エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows および Linux |![あり](./media/log-analytics-change-tracking/oms-bullet-green.png) |![あり](./media/log-analytics-change-tracking/oms-bullet-green.png) |![あり](./media/log-analytics-change-tracking/oms-bullet-green.png) |![なし](./media/log-analytics-change-tracking/oms-bullet-red.png) |![いいえ](./media/log-analytics-change-tracking/oms-bullet-red.png) |![あり](./media/log-analytics-change-tracking/oms-bullet-green.png) |時間単位 |

## <a name="use-change-tracking"></a>変更の追跡を使用する
ソリューションをインストールした後で、OMS の **[概要]** ページにある **[変更の追跡]** タイルを使用すると、監視対象サーバーの変更の概要を確認できます。

![[変更の追跡] タイルの画像](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

インフラストラクチャに対する変更を確認し、変更の詳細を次のカテゴリ別に表示することができます。

* ソフトウェアや Windows サービスに対する構成の種類ごとの変更
* アプリケーションに対するソフトウェアの変更および個々のサーバーに対する更新
* 各アプリケーションのソフトウェア変更の合計数
* Linux パッケージ
* 個々のサーバーでの Windows サービスの変更
* Linux デーモンの変更

![[変更の追跡] ダッシュボードの画像](./media/log-analytics-change-tracking/oms-changetracking01.png)

![[変更の追跡] ダッシュボードの画像](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>変更の種類別に変更を表示するには
1. **[概要]** ページで、**[変更の追跡]** タイルをクリックします。
2. **[変更の追跡]** ダッシュボードにあるいずれかの変更の種類ブレードで概要情報を確認し、**ログの検索**ページで、詳細情報を表示する変更の 1 つをクリックします。
3. どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="next-steps"></a>次のステップ
* [Log Analytics のログ検索機能](log-analytics-log-searches.md) を使用して、詳細な変更追跡データを確認してください。




<!--HONumber=Nov16_HO3-->


