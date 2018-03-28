---
title: ロジック アプリで Twitter コネクタを使用する方法 | Microsoft Docs
description: Twitter コネクタと REST API パラメーターの概要
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: eb953ee7701d407b9b75a0699f53b9b64828a0e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twitter-connector"></a>Twitter コネクタの使用
Twitter コネクタでは、次のことが可能です。

* ツイートの投稿とツイートの取得
* タイムライン、友達、フォロワーへのアクセス
* この記事で説明するその他のアクションやトリガーの実行

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)から始めることができます。  

## <a name="connect-to-twitter"></a>Twitter への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-twitter"></a>Twitter への接続を作成する
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Twitter トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

この例では、**[新しいツイートが投稿されたら]** トリガーを使用して #Seattle を検索します。 そして #Seattle が見つかった場合は、ツイートのテキストで Dropbox のファイルを更新します。 企業での使用例として、自社の名前を検索し、SQL データベースをツイートのテキストで更新できます。

1. Logic Apps デザイナーの検索ボックスに「*twitter*」と入力し、**[Twitter - When a new tweet is posted (Twitter - 新しいツイートの投稿時)]** トリガーを選択します。   
   ![Twitter トリガーの画像 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. **[Search Text (検索テキスト)]** コントロールに「*#Seattle*」と入力します。  
   ![Twitter トリガーの画像 2](./media/connectors-create-api-twitter/trigger-2.png) 

これで、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。 

> [!NOTE]
> ロジック アプリを機能させるには、少なくとも 1 つのトリガーとアクションが含まれている必要があります。 次のセクションに記載された手順を使用してアクションを追加します。

## <a name="add-a-condition"></a>条件を追加する
興味があるのは、50 人以上のユーザーを持つユーザーからのツイートだけです。 そのため、フォロワー数を確認する条件が最初にロジック アプリに追加されます。  

1. **[+ 新しいステップ]** をクリックして、新しいツイートで #Seattle が見つかったときに実行するアクションを追加します。  
   ![Twitter アクションの画像 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. **[条件の追加]** リンクをクリックします。  
   ![Twitter 条件の画像 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   **[条件]** コントロールが開きます。ここで、*[is equal to (次の値に等しい)]*、*[is less than (次の値より小さい)]*、*[is greater than (次の値より大きい)]*、*[contains (次の値を含む)]* などの条件を確認できます。  
   ![Twitter 条件の画像 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. **[値の選択]** コントロールを選択します。 このコントロールでは、以前のアクションまたはトリガーから 1 つまたは複数のプロパティを選択できます。 このプロパティ値の条件は、true または false に評価されます。
   ![Twitter 条件の画像 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. 使用可能なすべてのプロパティを確認できるように、**[...]** をクリックしてプロパティの一覧を展開します。        
   ![Twitter 条件の画像 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. **[Followers count (フォロワー数)]** プロパティを選択します。    
   ![Twitter 条件の画像 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 値コントロールに [Followers count (フォロワー数)] プロパティが表示されていることを確認します。    
   ![Twitter 条件の画像 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 演算子の一覧から **[is greater than (次の値より大きい)]** を選択します。    
   ![Twitter 条件の画像 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. "*is greater than (次の値より大きい)*" 演算子のオペランドとして「50」と入力します。  
   これで条件が追加されました。 メニューの **[保存]** リンクを使用して作業内容を保存します。    
   ![Twitter 条件の画像 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Twitter アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。  

トリガーを追加したら、トリガーによって見つけられたツイートの内容を含む新しいツイートを投稿するアクションを追加します。 このチュートリアルでは、50 人以上のフォロワーを持つユーザーのツイートだけを投稿します。  

次の手順では、50 人以上のフォロワーを持つユーザーが投稿した各ツイートのプロパティの一部を使用してツイートを投稿する Twitter アクションを追加します。  

1. **[アクションの追加]**を選択します。 この手順で、他のアクションやトリガーを検索できる検索コントロールが開きます。  
   ![Twitter 条件の画像 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 検索ボックスに「*twitter*」と入力し、**[Twitter - Post a tweet (Twitter - ツイートの投稿)]** アクションを選択します。 この手順で、**[Post a tweet (ツイートの投稿)]** コントロールが開きます。ここで、投稿するツイートの詳細をすべて入力します。      
   ![Twitter アクションの画像 1 ～ 5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. **[Tweet text (ツイート テキスト)]** を選択します。 ロジック アプリでの以前のアクションとトリガーのすべての出力が表示されます。 これらの出力のいずれかを選択し、新しいツイートのツイート テキストの一部として使用できます。     
   ![Twitter アクションの画像 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. **[ユーザー名]** を選択します。   
5. ユーザー名の直後に、ツイート テキスト コントロールに「*のコメント:*」と入力します。
6. *[Tweet text (ツイート テキスト)]* を選択します。       
   ![Twitter アクションの画像 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. ワークフローをアクティブ化するには、作業内容を保存し、#Seattle というハッシュタグを含めたツイートを送信します。


## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/twitterconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次の手順
[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)