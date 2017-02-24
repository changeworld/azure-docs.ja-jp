---
title: "初めての Azure ロジック アプリでのワークフローの作成 | Microsoft Docs"
description: "初めてのロジック アプリでアプリと SaaS サービスの接続を開始します"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>SaaS サービスを接続する新しいロジック アプリを作成します。
このトピックでは、初めての方に向けて [Azure Logic Apps](logic-apps-what-are-logic-apps.md)の使い方を簡単に説明します。 電子メール アドレス宛てに興味深いツイートを送信できる簡単なワークフローを紹介します。

このシナリオを使用するには、以下が必要です。

* Azure サブスクリプション
* Twitter アカウント
* Outlook.com またはホストされている Office 365 のメールボックス

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>ツイートを電子メールで送信する新しいロジック アプリを作成する
1. [Azure ポータルのダッシュボード](https://portal.azure.com)で、 **[新規]**を選択します。 
2. 検索バーで "logic app" を検索し、 **[Logic App]**を選択します。 **[新規]**、**[Web + モバイル]**、**[Logic App]** を順に選択してもかまいません。 
3. ロジック アプリの名前を入力し、場所とリソース グループを選択して、 **[作成]**を選択します。  **[ダッシュボードにピン留めする]** を選択した場合、ロジック アプリがデプロイ後に自動的に開きます。  
4. ロジック アプリを初めて開いたときは、どのテンプレートから開始するかを選択できます。  ここでは、 **[Blank Logic App (空のロジック アプリ)]** をクリックして最初から作成します。 
5. 最初に作成する必要があるアイテムは、トリガーです。  これはロジック アプリを起動するイベントです。  トリガー検索ボックスで " **twitter** " を検索して選択します。
6. ここでは、トリガーに設定する検索語句を入力します。  **[頻度]** と **[間隔]** によってロジック アプリで新しいツイートを確認する頻度が決まります (その期間中のすべてのツイートが返されます)。
    ![Twitter の検索](media/logic-apps-create-a-logic-app/twittersearch.png)
7. **[新しいステップ]** を選択して、**[アクションの追加]** または **[条件の追加]** を選択します。
8. **[アクションの追加]** を選択すると、[利用可能なコネクタ](../connectors/apis-list.md)から検索してアクションを選択することができます。 たとえば、**[Outlook.com - Send Email (Outlook.com - 電子メールの送信)]** を選択すると、outlook.com のアドレスから電子メールを送信できます。  
    ![アクション](media/logic-apps-create-a-logic-app/actions.png)
9. 次は、電子メールに使用するパラメーターを入力する必要があります。 ![パラメーター](media/logic-apps-create-a-logic-app/parameters.png)
10. 最後に **[保存]** を選択すると、ロジック アプリがライブ状態となります。

## <a name="manage-your-logic-app-after-creation"></a>作成後にロジック アプリを管理する
ここまでの手順で、ロジック アプリが実行できるようになりました。 このロジック アプリは、入力した検索語句を含むツイートを定期的に確認します。 検索語句に一致するツイートが検出されると、電子メールを送信します。 ここでは、最後にアプリを無効にしたり、動作内容を確認したりする方法を見ていきます。

1. [Azure ポータル](https://portal.azure.com)
2. 画面左側の **[参照]** をクリックし、**[Logic Apps]** を選択します。
3. 新たに作成したロジック アプリをクリックすると、現在の状態や全般的な情報を確認できます。
4. 新しいロジック アプリを編集するには、 **[編集]**をクリックします。
5. アプリを無効にするには、コマンド バーで **[無効化]** をクリックします。
6. ロジック アプリがいつ実行されているかを監視するには、実行とトリガーの履歴を表示します。  **[更新]** をクリックすると、最新のデータを表示できます。

クラウド上で実行できる簡単なロジック アプリを 5 分もしないうちに作成できました。 Logic Apps の機能の使用について詳しくは、「 [ロジック アプリの機能を使用する]」をご覧ください。 ロジック アプリの定義自体の詳細については、「 [ロジック アプリの定義を作成する](../logic-apps/logic-apps-author-definitions.md)」を参照してください。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[ロジック アプリの機能を使用する]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


