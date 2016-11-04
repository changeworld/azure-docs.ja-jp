---
title: BizTalk Services における発行者名と発行者キー | Microsoft Docs
description: BizTalk サービスの Service Bus または Access Control (ACS) の発行者名と発行者キーを取得する方法について説明します。MABS、WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''

ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: mandia

---
# BizTalk サービス: 発行者名および発行者キーに関するページ
Azure BizTalk Services では、サービス バス発行者名および発行者キーと Access Control 発行者名および発行者キーが使用されます。具体的には次の処理が行われます。

| タスク | 発行者名および発行者キー |
| --- | --- |
| Visual Studio からアプリケーションをデプロイする |Access Control 発行者名および発行者キー |
| Azure BizTalk サービス ポータルを構成する |Access Control 発行者名および発行者キー |
| Visual Studio で BizTalk Adapter サービスを使用して LOB リレーを作成する |Service Bus 発行者名および発行者キー |

このトピックでは、発行者名と発行者キーを取得する手順を示します。

## Access Control 発行者名および発行者キー
Access Control 発行者名および発行者キーは次のコンポーネントで使用されます。

* Visual Studio で作成された Azure BizTalk サービス アプリケーション: Visual Studio で BizTalk サービス アプリケーションを Azure にデプロイするには、Access Control 発行者名と発行者キーを入力する必要があります。
* Azure BizTalk サービス ポータル: BizTalk サービスを作成して BizTalk サービス ポータルを開くと、Access Control 発行者名と発行者キーが、同じ Access Control 値で展開に自動的に登録されます。

### Access Control 発行者名および発行者キーをコピーして貼り付けるには
1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左のナビゲーション ウィンドウで、**[BizTalk サービス]** を選択します。
3. BizTalk サービスを選択します。
4. タスク バーの **[接続情報]** をクリックします。Access Control 名前空間、既定の発行者 (発行者名)、既定のキー (発行者キー) が一覧に表示され、コピーして貼り付けることができます。

まとめると次のようになります: 発行者名 = 既定の発行者 発行者キー = 既定のキー

[**ACS 管理ポータルを開く**] を選択して Access Control の値を取得することもできます。

1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左のナビゲーション ウィンドウで、**[BizTalk サービス]** を選択します。
3. BizTalk サービスを選択します。
4. [接続情報] をクリックし、**[ACS 管理ポータルを開く]** をクリックします。
5. [**サービスの設定**] にあるポータルで、[**サービス ID**] を選択します。これにより、サービス ID が表示されます。これは Access Control 発行者名の値です。サービス ID リンクを選択すると、パスワードが表示されます。これは発行者キーの値です。これらの値はコピーできます。<br/><br/> たとえば、[**サービス ID**] に "所有者" と表示されているとします。"所有者" は Access Control 発行者名です。"所有者" リンクをクリックすると、**パスワード**が表示されます。"パスワード" リンクをクリックすると、値が表示されます。このパスワード値は Access Control 発行者キーです。

まとめると次のようになります。発行者名 = サービス ID 名 発行者キー = パスワード値

左側のナビゲーション ウィンドウで **[Active Directory]** をクリックして Access Control の値を取得することもできます。

> [!IMPORTANT]
> **Active Directory** を使用して Access Control 名前空間を作成したときに、サービス ID が自動的に作成されることは**ありません**。BizTalk サービスをプロビジョニングすると、Access Control 名前空間、"所有者" (発行者名) という名前のサービス ID、パスワード (発行者キー)、および対称キーが自動的に作成されます。<br /> 「[方法: ACS 管理サービスを使用してサービス ID を構成する](http://go.microsoft.com/fwlink/p/?LinkID=303942)」に、Access Control サービス ID に関する詳しい情報が記載されています。
> 
> 

## Service Bus 発行者名および発行者キー
サービス バス発行者名および発行者キーは BizTalk Adapter サービスで使用されます。Visual Studio の BizTalk サービス プロジェクトでは、BizTalk Adapter サービスを使用して内部設置型の基幹業務 (LOB) システムに接続します。接続するには、LOB リレーを作成し、LOB システムの詳細を入力します。このとき、サービス バス発行者名と発行者キーも入力します。

### Service Bus 発行者名および発行者キーを取得するには
1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左のナビゲーション ウィンドウで、[**Service Bus**] を選択します。
3. 名前空間を選択します。タスク バーで [**接続情報**] をクリックします。これにより、**既定の発行者** (発行者名) と**既定のキー** (発行者キー) が表示されます。これらの値はコピーできます。

まとめると次のようになります:  
発行者名 = 既定の発行者  
発行者キー = 既定のキー

## 次へ
Azure BizTalk Services に関するその他のトピック:

* [Azure BizTalk サービス SDK のインストール](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [チュートリアル: Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## 関連項目
* [方法: ACS 管理サービスを使用してサービス ID を構成する](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk サービス: Azure クラシック ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: プロビジョニングの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk サービス: バックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

<!---HONumber=AcomDC_0817_2016-->