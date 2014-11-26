<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# BizTalk サービス: サービスの状態のチャート

BizTalk サービスの現在の状態によっては、BizTalk サービスで実行できる操作と実行できない操作があります。

たとえば、Azure 管理ポータルで新しい BizTalk サービスをプロビジョニングするとします。正常に完了すると、BizTalk サービスが Active 状態になります。Active 状態では、BizTalk サービスを停止できます。停止に成功すると、BizTalk サービスは Stopped 状態になります。停止に失敗すると、BizTalk サービスは StopFailed 状態になります。StopFailed 状態では、BizTalk サービスを再起動できます。許可されていない操作を実行しようとした場合 (BizTalk サービスの再開など)、次のエラーが発生します。

**許可されない操作です**

BizTalk サービスをプロビジョニングするには、[Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング][Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]に関するページを参照してください。

次の表に、BizTalk サービスの各状態で実行できる操作を示します。チェック マークは、その状態で実行できる操作を意味します。空のエントリは、その状態で実行できない操作を意味します。

#### 開始、停止、再起動、中断、再開、および削除の各操作

<table border="1">
<tr>
<th colspan="15">
操作

</th>
</tr>
<tr>
<th rowspan="18">
BizTalk サービスの状態

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
開始

</th>
<th>
停止

</th>
<th>
再起動

</th>
<th>
中断

</th>
<th>
再開

</th>
<th>
削除

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Active<b/>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Disabled<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Suspended<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Stopped<b/>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Service Update Failed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>DisableFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>EnableFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>StartFailed
 StopFailed
 RestartFailed<b/>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>SuspendedFailed
 ResumeFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>CreatedFailed
 RestoreFailed
<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ConfigUpdateFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ScaleFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>

#### スケール、構成の更新、およびバックアップの各操作

<table border="1">
<tr>
<th colspan="15">
操作

</th>
</tr>
<tr>
<th rowspan="18">
BizTalk サービスの状態

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
スケール

</th>
<th>
構成の更新

</th>
<th>
バックアップ

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Active<b/>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Disabled<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Suspended<b/>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Stopped<b/>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Service Update Failed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>DisableFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>EnableFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>StartFailed
 StopFailed
 RestartFailed<b/>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>SuspendedFailed
 ResumeFailed<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>CreatedFailed
 RestoreFailed
<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ConfigUpdateFailed<b/>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ScaleFailed<b/>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## 関連項目

-   [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング][Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]
-   [BizTalk サービス: ダッシュボード、監視、および スケール タブ][BizTalk サービス: ダッシュボード、監視、および スケール タブ]
-   [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート][BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]
-   [BizTalk サービス: バックアップと復元][BizTalk サービス: バックアップと復元]
-   [BizTalk サービス: 調整][BizTalk サービス: 調整]
-   [BizTalk サービス: 発行者名および発行者キー][BizTalk サービス: 発行者名および発行者キー]
-   [Azure BizTalk サービス SDK の使用開始に関するページ][Azure BizTalk サービス SDK の使用開始に関するページ]

  [Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk サービス: ダッシュボード、監視、および スケール タブ]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk サービス: バックアップと復元]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk サービス: 調整]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk サービス: 発行者名および発行者キー]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Azure BizTalk サービス SDK の使用開始に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=302335
