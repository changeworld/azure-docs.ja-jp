<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk サービス: 調整

Azure BizTalk サービスは、メモリ使用率およびメッセージの同時処理数という 2 つの条件に基づいてサービスの調整を実装します。このトピックでは、調整のしきい値を示し、調整条件が発生したときのランタイムの動作について説明します。

## 調整のしきい値

調整の対象としきい値の一覧を次の表に示します。

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<td>
<b>説明<b/>

</td>
<td>
<b>低いしきい値<b/>

</td>
<td>
<b>高いしきい値<b/>

</td>
</tr>
<tr>
<td>
メモリ

</td>
<td>
使用可能な合計システム メモリ/PageFileBytes の割合。<br/><br/>
 使用可能な合計 PageFileBytes は、システムの RAM の約 2 倍です。

</td>
<td>
60%

</td>
<td>
70%

</td>
</tr>
<tr>
<td>
メッセージ処理

</td>
<td>
メッセージの同時処理数

</td>
<td>
40 * コアの数

</td>
<td>
100 * コアの数

</td>
</tr>
</table>
高いしきい値に達すると、調整が開始されます。低いしきい値に達すると、調整は停止します。たとえば、サービスで 65% のシステム メモリを使用しているとします。この状況では、調整は行われません。一方、サービスで 70% のシステム メモリの使用を開始するとします。この状況では、調整が行われ、サービスによるシステム メモリの使用が 60% (低いしきい値) になるまで調整が継続されます。

Azure BizTalk サービスでは、調整の状態 (通常と調整された状態) および調整の期間が追跡されます。

## ランタイムの動作

Azure BizTalk サービスが調整状態になると、次のことが発生します。

-   調整はロール インスタンス単位で行われます。次に例を示します。
    RoleInstanceA は調整中です。RoleInstanceB は調整されていません。この状況では、RoleInstanceB のメッセージは予期したとおりに処理されます。RoleInstanceA のメッセージは破棄され、次のエラーで失敗します。
<br/><br/>
    サーバーがビジー状態です。もう一度実行してください。
<br/><br/>
-   どの取り出し元も、メッセージのポーリングやダウンロードを行いません。次に例を示します。<br/>
    パイプラインは外部の FTP ソースからメッセージを取り出します。取り出しを実行するロール インスタンスは調整状態になります。この状況では、パイプラインは、ロール インスタンスが調整を停止するまで追加のメッセージのダウンロードを停止します。
-   応答がクライアントに送信され、クライアントはメッセージを再送信できるようになります。
-   調整が解決されるまで待つ必要があります。具体的には、低いしきい値に達するまで待つ必要があります。

## 重要

-   調整を無効にすることはできません。
-   調整のしきい値を変更することはできません。
-   調整はシステム全体で実装されます。
-   Azure SQL データベース サーバーには、組み込みの調整もあります。

## Azure BizTalk サービスに関するその他のトピック

-   [Azure BizTalk サービス SDK のインストール][Azure BizTalk サービス SDK のインストール]
-   [チュートリアル: Azure BizTalk サービス][チュートリアル: Azure BizTalk サービス]
-   [Azure BizTalk サービス SDK の使用開始に関するページ][Azure BizTalk サービス SDK の使用開始に関するページ]
-   [Azure BizTalk サービス][Azure BizTalk サービス]

## 関連項目

-   [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート][BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]
-   [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング][BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング]
-   [BizTalk サービス: プロビジョニングの状態のチャート][BizTalk サービス: プロビジョニングの状態のチャート]
-   [BizTalk サービス: ダッシュボード、監視、および スケール タブ][BizTalk サービス: ダッシュボード、監視、および スケール タブ]
-   [BizTalk サービス: バックアップと復元][BizTalk サービス: バックアップと復元]
-   [BizTalk サービス: 発行者名および発行者キー][BizTalk サービス: 発行者名および発行者キー]

  [Azure BizTalk サービス SDK のインストール]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [チュートリアル: Azure BizTalk サービス]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Azure BizTalk サービス SDK の使用開始に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Azure BizTalk サービス]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk サービス: プロビジョニングの状態のチャート]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk サービス: ダッシュボード、監視、および スケール タブ]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk サービス: バックアップと復元]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk サービス: 発行者名および発行者キー]: http://go.microsoft.com/fwlink/p/?LinkID=303941
