<properties 
	pageTitle="操作ログを用いた BizTalk Services のトラブルシューティング | Azure" 
	description="操作ログを使用して BizTalk Services のトラブルシューティングを行います。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>


# BizTalk サービス: 操作ログを使用したトラブルシューティング

## 操作ログとは
操作ログは、Azure 管理ポータルで使用できる管理サービス機能で、BizTalk サービスを含む Azure サービスで実行された操作の履歴ログを表示することができます。BizTalk サービス サブスクリプションにおける管理操作に関する履歴データを 180 日間分、確認できます。

> [AZURE.NOTE]この機能で記録されるのは、BizTalk サービスに対する管理操作 (サービスの開始やバックアップなど) のログのみです。このような管理操作は、Azure 管理ポータルから実行されたか、[BizTalk サービスの REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) を使用して実行されたかを問わず、常に追跡されます。管理サービスを使用して追跡される操作の完全な一覧については、「[Azure 管理サービスを使用して追跡される操作](#bizops)」を参照してください。<br/><br/> この機能は、BizTalk サービスのランタイムに関連するアクティビティのログは記録しません (ブリッジが処理するメッセージなど)。このようなログを表示するには、BizTalk サービス ポータルの [追跡] ビューを使用します。詳細については、「[メッセージの追跡](http://msdn.microsoft.com/library/azure/hh949805.aspx)」を参照してください。

## BizTalk Services の操作ログの表示
1. Azure 管理ポータルで、[**管理サービス**] を選択し、[**操作ログ**] タブを選択します。
2. さまざまなパラメーターに基づいてログをフィルター処理できます。パラメーターの例としては、サブスクリプション、日付の範囲、サービスの種類 (例: BizTalk サービス)、サービス名、操作の状態 (成功、失敗) があります。
3. チェックマークを選択すると、フィルター処理された一覧が表示されます。次の図は、testbiztalkservice に関連するアクティビティを示します。![操作ログの表示][ViewLogs] 
4. 特定の操作について詳細を表示するには、操作の行を選択して、下部のタスク バーにある [**詳細**] をクリックします。


## <a name="bizops"></a>Azure 管理サービスを使用して追跡される操作
Azure 管理サービスを使用して追跡される操作の一覧を次の表に示します。

操作の名前 | タスク
--- | ---
CreateBizTalkService | 新しい BizTalk サービスを作成する操作
DeleteBizTalkService | BizTalk サービスを削除する操作
RestartBizTalkService | BizTalk サービスを再起動する操作
StartBizTalkService | BizTalk サービスを開始する操作
StopBizTalkService | BizTalk サービスを停止する操作
DisableBizTalkService | BizTalk サービスを無効にする操作
EnableBizTalkService | BizTalk サービスを有効にする操作
BackupBizTalkService | BizTalk サービスをバックアップする操作
RestoreBizTalkService | 指定されたバックアップから BizTalk サービスを復元する操作
SuspendBizTalkService | BizTalk サービスを中断する操作
ResumeBizTalkService | BizTalk サービスを再開する操作
ScaleBizTalkService | BizTalk サービスのスケールを拡大または縮小する操作
ConfigUpdateBizTalkService | BizTalk サービスの構成を更新する操作
ServiceUpdateBizTalkService | BizTalk サービスを別のバージョンにアップグレードまたはダウングレードする操作
PurgeBackupBizTalkService | 保持期間を超えた BizTalk サービスのバックアップを消去する操作


## 関連項目
- [BizTalk サービスのバックアップ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [バックアップからの BizTalk サービスの復元](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk サービス: Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk サービス: サービスの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 

<!---HONumber=August15_HO6-->