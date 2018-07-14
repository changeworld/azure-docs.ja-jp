---
title: 'Conversation Learner のデモ アプリケーション: 仮想現実アプリ ランチャー - Microsoft Cognitive Services | Microsoft Docs'
titleSuffix: Azure
description: Conversation Learner のデモ アプリケーションの作成方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376325"
---
# <a name="demo-virtual-reality-app-launcher"></a>デモ: 仮想現実アプリ ランチャー

このデモでは、「Skype を起動して壁に配置」のようなコマンドをサポートする仮想現実アプリケーション ランチャーを示します。 ユーザーがアプリを起動するためには、アプリの名前と場所を言う必要があります。 アプリの起動は、API 呼び出しによって処理されます。 ユーザーが言ったアプリの名前が認識されると、entityDetectionCallback は、要求されたアプリがインストール済みアプリの一覧で 1 つまたは複数のアプリと一致するかどうかを調べます。 それは、要求されたアプリがインストールされていないケースとアプリ名があいまいなケース (2 つ以上のインストール済みアプリと一致) を処理します。

## <a name="requirements"></a>必要条件

このチュートリアルでは、VRAppLauncher ボットが実行されている必要があります。

    npm run demo-vrapp

### <a name="open-the-demo"></a>デモを開く

Web UI のアプリ一覧で、[VRAppLauncher] をクリックします。 

## <a name="entities"></a>エンティティ

エンティティは 4 つ作成してあります。

- AppName: たとえば Skype
- PlacementLocation: たとえば壁
- UnknownAppName: ユーザーが言ったエンティティ名を (インストールされていないなどの理由で) 認識できないときに、システムが設定するプログラムによるエンティティ
- DisAmbigAppNames: ユーザーが言ったものと一致する、2 つ以上のインストール済みのアプリ名の配列 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>アクション

LaunchApp と呼ばれる API を含むアクションのセットを作成してあります。これは、アプリを起動するための関数呼び出しを開始します。

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>トレーニング会話
いくつかのトレーニング会話が定義されています。

![](../media/tutorial_vrapplauncher_dialogs.PNG)

例として、学習セッションを試してみましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「hi」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [which app do you want to start?] をクリックして選択します。
4. 「Outlook」と入力します。
    - LUIS はそれをエンティティとして認識します。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [where do you want it placed?] をクリックして選択します。
4. 「on the wall」と入力します。
    - LUIS はそれを PlacementLocation として認識します。
2. 「Score Actions」と入力します。
6. [LaunchApp] を選択します。
7. システム: "starting outlook on the wall"
    - これは、API 呼び出しがトリガーされたことを示します。 この呼び出しのコードは C:\<\installedpath>\src\demos\demoVRAppLauncher.ts にあります。 ただし、このデモでは、outlook を起動するためのロジックは実際には含まれていません。
    - AppName エンティティと PlacementLocation エンティティがクリアされます。 応答として上記の文字列が返ります。
4. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

不明なエンティティとあいまいなエンティティを処理するための別のトレーニング セッションを開始しましょう。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「start OneNote」と入力します。 
    - コード内に定義された EntityDetectionCallback が、ユーザーが入力した名前をコード内に定義されたアプリの一覧と照合してアプリ名に解決するため、それはアプリ名として認識されます。 一致するすべてのアプリのセットが返されます。 
    - 一致の一覧に何も含まれていない場合は、アプリがインストールされていないことを意味します。 UnknownAppName 状態になります。
    - 1 つ以上のアプリが検出された場合は、それらが DisambigAppNames 内にコピーされ、AppName エンティティがクリアされます。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Sorry, I don't know the app $UknownAppName.] をクリックして選択します。
4. 「start amazon」と入力します。 もう一方のケースを試してみましょう。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
    - 現在、DisambigAppNames メモリ内には、Amazon Video と Amazon Music があります。 OneNote はクリアされています。
3. [There are few apps that sound like that...] をクリックして選択します。
    - この時点までのトレーニング会話は少ないため、スコアはあまり高くなりません。 モデルをもっと決定的にするには、ここでさらに多くの会話が必要であると思われます。
2. 「Score Actions」と入力します。
4. [Done Teaching]\(学習の完了\) をクリックします。

ここでは、エンティティを解決する方法を見てきました。 このデモでは、API コールバックと、情報を集め、存在とあいまいさをチェックし、それに基づいて適切なアクションを行うためのテンプレートを示しました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Conversation Learner ボットのデプロイ](../deploy-to-bf.md)
