<properties 
	pageTitle="BizTalk サービス:サービスの状態のチャート | Azure" 
	description="TMABS のさまざまな状態で許可されるアクションと操作: 停止、開始、再起動、中断、再開、削除、拡張、上の構成、およびバックアップの更新" 
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
	ms.date="02/01/2015" 
	ms.author="mandia"/>



# BizTalk サービス: サービスの状態のチャート
BizTalk サービスの現在の状態によっては、BizTalk サービスで実行できる操作と実行できない操作があります。

たとえば、Azure の管理ポータルで新しい BizTalk サービスをプロビジョニングするとします。正常に完了すると、BizTalk サービスが Active 状態になります。Active 状態では、BizTalk サービスを停止できます。停止に成功すると、BizTalk サービスは Stopped 状態になります。停止に失敗すると、BizTalk サービスは StopFailed 状態になります。StopFailed 状態では、BizTalk サービスを再起動できます。許可されていない操作を実行しようとした場合 (BizTalk サービスの再開など)、次のエラーが発生します。

**許可されない操作です**

BizTalk サービスをプロビジョニングするには、「[Create a BizTalk Service using Azure management portal (Azure の管理ポータルを使用した BizTalk サービスの作成)](http://go.microsoft.com/fwlink/p/?LinkID=302280)」を参照してください。

次の表に、BizTalk サービスの各状態で実行できる操作を示します。チェック マークは、その状態で実行できる操作を意味します。空のエントリは、その状態で実行できない操作を意味します。

#### 開始、停止、再起動、中断、再開、および削除の各操作
<table border="1">
<tr>
        <th colspan="15">操作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk サービスの状態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>開始</th>
        <th>停止</th>
        <th>再起動</th>
        <th>中断</th>
        <th>再開</th>
        <th>削除</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>○</center></td>
<td><center>○</center></td>
<td><center>○</center></td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停止済み</b></td>
<td><center>○</center></td>
<td> </td>
<td><center>○</center></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>○</center></td>
<td><center>○</center></td>
<td><center>○</center></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
<td><center>○</center></td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
</table>
<br/>

####スケール、構成の更新、およびバックアップの各操作
<table border="1">
<tr>
        <th colspan="15">操作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk サービスの状態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>スケール</th>
        <th>構成の更新</th>
        <th>バックアップ</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>○</center></td>
<td><center>○</center></td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停止済み</b></td>
<td> </td>
<td> </td>
<td><center>○</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>○</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>○</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>○</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## 関連項目
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: バックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)



<!--HONumber=46--> 
