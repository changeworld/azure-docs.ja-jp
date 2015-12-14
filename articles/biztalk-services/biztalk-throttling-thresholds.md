<properties 
	pageTitle="BizTalk Services での調整について | Microsoft Azure" 
	description="調整のしきい値についてと、結果として生じる BizTalk Services のランタイムの動作について説明します。調整は、メモリ使用率とメッセージの数に基づいて行われます。MABS、WABS" 
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
	ms.date="12/02/2015" 
	ms.author="mandia"/>





# BizTalk Services: 調整

Azure BizTalk Services は、メモリ使用率およびメッセージの同時処理数という 2 つの条件に基づいてサービスの調整を実装します。このトピックでは、調整のしきい値を示し、調整条件が発生したときのランタイムの動作について説明します。

## 調整のしきい値

調整の対象としきい値の一覧を次の表に示します。

||説明|低いしきい値|高いしきい値| |---|---|---|---| |メモリ|使用可能な合計システム メモリ/PageFileBytes の割合。<p><p>使用可能な合計 PageFileBytes は、システムの RAM の約 2 倍です。|60%|70%| |メッセージ処理|メッセージの同時処理数|40 * コアの数|100 * コアの数|

高いしきい値に達すると、調整が開始されます。低いしきい値に達すると、調整は停止します。たとえば、サービスで 65% のシステム メモリを使用しているとします。この状況では、調整は行われません。一方、サービスで 70% のシステム メモリの使用を開始するとします。この状況では、調整が行われ、サービスによるシステム メモリの使用が 60% (低いしきい値) になるまで調整が継続されます。

Azure BizTalk Services では、調整の状態 (通常と調整された状態) および調整の期間が追跡されます。


## ランタイムの動作

Azure BizTalk Services が調整状態になると、次のことが発生します。

- 調整はロール インスタンス単位で行われます。次に例を示します。<br/>RoleInstanceA は調整中です。RoleInstanceB は調整されていません。この状況では、RoleInstanceB のメッセージは予期したとおりに処理されます。RoleInstanceA のメッセージは破棄され、次のエラーで失敗します。<br/><br/> **サーバーがビジー状態です。もう一度実行してください。**<br/><br/>
- どの取り出し元も、メッセージのポーリングやダウンロードを行いません。次に例を示します。<br/>パイプラインは外部の FTP ソースからメッセージを取り出します。取り出しを実行するロール インスタンスは調整状態になります。この状況では、パイプラインは、ロール インスタンスが調整を停止するまで追加のメッセージのダウンロードを停止します。
- 応答がクライアントに送信され、クライアントはメッセージを再送信できるようになります。
- 調整が解決されるまで待つ必要があります。具体的には、低いしきい値に達するまで待つ必要があります。

## 重要
- 調整を無効にすることはできません。
- 調整のしきい値を変更することはできません。
- 調整はシステム全体で実装されます。
- Azure SQL Database サーバーには、組み込みの調整もあります。

## Azure BizTalk Services に関するその他のトピック

-  [Azure BizTalk Services SDK のインストール](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [チュートリアル: Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## 関連項目
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: Azure クラシック ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: プロビジョニングの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: バックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=AcomDC_1203_2015-->