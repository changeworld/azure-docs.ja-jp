---
title: "Azure Logic Apps でのオンプレミス データ ゲートウェイによる SAP コネクタの使用 | Microsoft Docs"
description: "Logic Apps では、オンプレミス SAP システムにワークフローの一部として簡単に接続することができます。"
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>SAP コネクタの使用 

ハイブリッド クラウドの接続は、Logic Apps の中核です。 オンプレミス データ ゲートウェイを使用すると、データを管理し、Logic Apps からオンプレミスのリソースに安全にアクセスできます。 この記事では、HTTP 経由で IDOC を要求し返信を送信するという単純なシナリオで、オンプレミスの SAP システムに接続する方法について説明します。    

 > [!NOTE]
 > この SAP コネクタは、次の SAP システムをサポートします。現在、Logic Apps には 90 秒を超える要求をブロックするというタイムアウトの制限があります。現在、ファイル ピッカーに表示できるフィールドの数には制限があります (パスは手動で追加できます)。
 >
 >

## <a name="prerequisites"></a>前提条件
- 最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/en-us/download/details.aspx?id=53127)をインストールして構成します。  

    まだインストールしていない場合は、最新のオンプレミス データ ゲートウェイのバージョン 1.15.6150.1 以降をインストールします。 詳しくは、[こちらの記事](http://aka.ms/logicapps-gateway)をご覧ください。 残りの手順を続行する前に、オンプレミス コンピューターにゲートウェイをインストールする必要があります。

- ユニバーサル ゲートウェイをインストールしたのと同じコンピューターに最新の SAP クライアント ライブラリをダウンロードし、インストールします。

## <a name="use-sap-connector"></a>SAP コネクタを使用する

1. HTTP 要求トリガーを作成し、検索フィールドに「SAP」と入力して SAP コネクタ アクションを選択します。    
 ![SAP を検索する](media/logic-apps-using-sap-connector/picture1.png)

2. [SAP] (SAP の設定に基づいて [ApplicationHost] または [MessagingHost]) を選択し、その接続をユニバーサル ゲートウェイを使用して作成します。
 - 既存の接続がない場合は、作成するよう求めるメッセージが表示されます。
 - [Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)] オプションをオンにします。チェックボックスをオンにすると、追加のフィールドが表示されます。
 - 接続名文字列を指定する
 - 前の手順でインストールしたゲートウェイを選択するか、[ゲートウェイのインストール] を選択して新しいゲートウェイをインストールします。   
 ![SAP への接続文字列を追加する](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > [Application Host](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 用と [Messaging host](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) 用の&2; 種類の SAP 接続があります。
  >
  >

3. すべての詳細を指定したら、[作成] をクリックします。 Logic Apps により、接続が構成およびテストされ、適切に機能していることが確認されます。 すべてが確認されると、前に選択したカードのオプションが表示されます。ファイル ピッカーを使用して適切な IDOC カテゴリを見つけるか、パスに手動で入力し、本文フィールドで HTTP 応答を選択します。    
 ![SAP アクション](media/logic-apps-using-sap-connector/picture3.png)

4. 新しいアクションを追加して HTTP 応答を作成します。応答メッセージは SAP 出力から作成される必要があります。

5. Logic App を保存し、HTTP トリガー URL を通じて IDOC を送信してテストします。

6. IDOC が送信されたら、Logic App からの応答を待機します。   

  > [!TIP]
  > [Logic Apps を監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)方法を確認してください。
  >
  >

7. これで完了です。SAP コネクタを使用した Logic App が機能するようになりました。 提供されるその他の機能の探索を開始できます。
  - BAPI
  - RFC

## <a name="next-steps"></a>次のステップ
- [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) についての詳細情報をご覧ください。 
- Logic Apps への[オンプレミス接続](../logic-apps/logic-apps-gateway-connection.md)を作成します。


<!--HONumber=Jan17_HO3-->


