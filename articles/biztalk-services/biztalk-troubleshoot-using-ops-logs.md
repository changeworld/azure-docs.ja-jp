<properties 
	pageTitle="BizTalk サービス:操作ログを使用したトラブルシューティング | Azure" 
	description="操作ログを使用して BizTalk Services のトラブルシューティングを行います。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="nitinme" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>


# BizTalk サービス: 操作ログを使用したトラブルシューティング

操作ログは、Azure の管理ポータルで使用できる管理サービス機能で、BizTalk サービスを含む Azure サービスで実行された操作の履歴ログを表示することができます。BizTalk サービス サブスクリプションにおける管理操作に関する履歴データを 180 日間分、確認できます。

<div class="dev-callout"><b>注</b>
<p>この機能は、サービスの開始やバックアップなど、BizTalk サービスでの管理操作のログのみを記録します。このような管理操作は、Azure 管理ポータルから実行したか、<a href="http://msdn.microsoft.com/library/windowsazure/dn232347.aspx">BizTalk サービスの REST API</a> を使用して実行したかを問わず、常に追跡されます。管理サービスを使用して追跡される操作の完全な一覧については、「<a href="#bizops">Azure 管理サービスを使用して追跡される操作</a>」を参照してください。</p>
<p>この機能は、BizTalk サービスのランタイムに関連するアクティビティのログは記録しません (ブリッジが処理するメッセージなど)。このようなログを表示するには、BizTalk サービス ポータルの [追跡] ビューを使用する必要があります。詳細については、「<a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Tracking Messages (メッセージの追跡)</a>」を参照してください。</p>
</div>

##<a name="viewlogs"></a>BizTalk Services の操作ログの表示
1. Azure 管理ポータルで、[管理サービス] をクリックし、[操作ログ] タブをクリックします。
2. さまざまなパラメーターに基づいてログをフィルター処理できます。パラメーターの例としては、サブスクリプション、日付の範囲、サービスの種類 (例: BizTalk サービス)、サービス名、操作の状態 (例: 成功、失敗) があります。
3. フィルター処理された一覧を表示するには、チェックマークをクリックします。次の図は、testbiztalkservice に関連するアクティビティを示します。
	![View operation logs][ViewLogs] 
4. 特定の操作について詳細を表示するには、操作の行を選択して、ページの下部にある <b>[詳細]</b> をクリックします。


##<a name="bizops"></a>Azure 管理サービスを使用して追跡される操作
Azure 管理サービスを使用して追跡される操作の一覧を次の表に示します。

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">新しい BizTalk サービスを作成する操作</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">BizTalk サービスを削除する操作</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">BizTalk サービスを再起動する操作</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">BizTalk サービスを開始する操作</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">BizTalk サービスを停止する操作</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">BizTalk サービスを無効にする操作</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">BizTalk サービスを有効にする操作</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">BizTalk サービスをバックアップする操作</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">指定されたバックアップから BizTalk サービスを復元する操作</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">BizTalk サービスを中断する操作</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">BizTalk サービスを再開する操作</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">BizTalk サービスのスケールを拡大または縮小する操作</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">BizTalk サービスの構成を更新する操作</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">BizTalk サービスを別のバージョンにアップグレードまたはダウングレードする操作</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">保持期間を超えた BizTalk サービスのバックアップを消去する操作</td> 
</tr>
</table>


## 関連項目
- [BizTalk サービスのバックアップに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [バックアップからの BizTalk サービスの復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk サービス: プロビジョニングの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

<!--HONumber=46--> 
 