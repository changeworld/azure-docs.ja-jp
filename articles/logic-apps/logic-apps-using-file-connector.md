---
title: オンプレミスのファイル システムに接続する - Azure Logic Apps | Microsoft Docs
description: オンプレミスのデータ ゲートウェイおよびファイル システム コネクタを使用して、ロジック アプリ ワークフローからオンプレミスのファイル システムに接続します
keywords: ファイルシステム, オンプレミス
services: logic-apps
author: derek1ee
manager: jeconnoc
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 019b5fcd218ddd471c5f02d0332b8f5b5bf0edb3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300822"
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>ファイル システム コネクタを使用した、Logic Apps からオンプレミスのファイル システムへの接続

データを管理し、オンプレミスのリソースに安全にアクセスするには、ロジック アプリでオンプレミスのデータ ゲートウェイを使用します。 この記事では、Dropbox にアップロードされているファイルをファイル共有にコピーし、電子メールを送信するという基本的なシナリオ例で、オンプレミスのファイル システムに接続する方法について説明します。

## <a name="prerequisites"></a>前提条件

* 最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)をダウンロードします。

* 最新のオンプレミス データ ゲートウェイのバージョン 1.15.6150.1 以降をインストールし、設定します。 手順については、[オンプレミスのデータ ソースへの接続](http://aka.ms/logicapps-gateway)に関するページを参照してください。 これらの手順を続行する前に、オンプレミス コンピューターにゲートウェイをインストールする必要があります。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>ファイル システムに接続するためのトリガーとアクションの追加

1. 空のロジック アプリを作成します。 **[Dropbox - ファイルが作成されたとき]** を最初の手順として追加します。 

2. トリガーで、**[+ 次のステップ]** > **[アクションの追加]** の順に選択します。 

3. 検索ボックスに、フィルターとして「file system」と入力します。 ファイル システム コネクタのすべてを表示するときは、**[ファイル システム - ファイルの作成]** アクションを選択します。 

   ![ファイル コネクタの検索](media/logic-apps-using-file-connector/search-file-connector.png)

4. ファイル システムへの接続がまだない場合は、接続を作成するように求められます。 

5. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。 接続のプロパティが表示されたら、表の説明に従って接続を設定します。

   ![接続の構成](media/logic-apps-using-file-connector/create-file.png)

   | Setting | 説明 |
   | ------- | ----------- |
   | **ルート フォルダー** | ファイル システムのルート フォルダーを指定します。 このフォルダーには、オンプレミスのデータ ゲートウェイがインストールされているコンピューターのローカル フォルダー、またはコンピューターがアクセス可能なネットワーク共有を指定できます。 <p>**ヒント:** ルート フォルダーはメインの親フォルダーで、すべてのファイル関連のアクションの相対パスに使用されます。 | 
   | **認証の種類** | ファイル システムで使用される認証の種類 | 
   | **ユーザー名** | 以前にインストールしたゲートウェイのユーザー名 {*domain*\\*username*} を指定します。 | 
   | **パスワード** | 以前にインストールしたゲートウェイのパスワードを指定します。 | 
   | **ゲートウェイ** | 以前にインストールしたゲートウェイを選択します。 | 
   ||| 

6. すべての接続の詳細を入力したら、**[作成]** を選択します。 

   Logic Apps により、接続の構成とテストが行われ、適切に機能していることが確認されます。 
   接続が正しくセットアップされると、事前に選択したアクションのオプションが表示されます。 
   これで、ファイル システム コネクタを使用する準備ができました。

7. Dropbox からオンプレミスのファイル共有のルート フォルダーにファイルをコピーする場合の**ファイルの作成**アクションを設定します。

   ![ファイル アクションの作成](media/logic-apps-using-file-connector/create-file-filled.png)

8. ファイルをコピーするこのアクションの後に、電子メールを送信する Outlook アクションを追加し、関連のユーザーに新しいファイルについて知らせます。 受信者、タイトル、および電子メールの本文を入力します。 

   **動的コンテンツ** リストで、ファイル コネクタからのデータ出力を選択し、電子メールに詳細を追加できます。

   ![電子メール送信アクション](media/logic-apps-using-file-connector/send-email.png)

9. ロジック アプリを保存し、 Dropbox にファイルをアップロードして、アプリをテストします。 ファイルはオンプレミスのファイル共有にコピーされ、操作に関す電子メール通知が届きます。

これで完了です。オンプレミスのファイル システムに接続するロジック アプリが機能するようになりました。 

コネクタが提供するその他の機能をお試しください。機能の例は、次のとおりです。

- ファイルを作成する
- フォルダー内のファイルを一覧表示する
- ファイルを追加する
- ファイルを削除する
- ファイルの内容を取得する
- パスを使用してファイルの内容を取得する
- ファイルのメタデータを取得する
- パスを使用してファイルのメタデータを取得する
- ルート フォルダー内のファイルを一覧表示する
- ファイルを更新する

## <a name="view-the-swagger"></a>Swagger の表示

[Swagger の詳細](/connectors/fileconnector/)を参照してください。 

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。

* [Azure Logic Apps User Voice サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次の手順

* [オンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md) 
* [ロジック アプリを監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [B2B 向けエンタープライズ統合シナリオ](../logic-apps/logic-apps-enterprise-integration-overview.md)
