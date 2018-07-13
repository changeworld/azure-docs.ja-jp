---
title: Conversation Learner アプリケーションでのバージョン管理とタグ付けの使用方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner アプリケーションでのバージョン管理とタグ付けの使用方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376285"
---
# <a name="how-to-use-versioning-and-tagging"></a>バージョン管理とタグ付けの使用方法

このチュートリアルでは、Conversation Learner アプリのバージョンにタグ付けし、どのバージョンが "ライブ" であるかを設定する方法について説明します。  

## <a name="requirements"></a>必要条件
このチュートリアルでは、[Log Dialog]\(会話記録\) の Web UI ではなく、ボット エミュレーターを使用して会話記録を作成する必要があります。  

このチュートリアルでは、一般的なチュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

編集するときは常に、"マスター" と呼ばれるタグを編集することになります。タグ付けされたバージョンをマスターから作成する (実質的にはマスターのスナップショットを作成する) ことはできますが、タグ付けされたバージョンを編集することはできません。

## <a name="steps"></a>手順

### <a name="install-the-bot-framework-emulator"></a>Bot Framework エミュレーターのインストール

- [[]https://github.com/Microsoft/BotFramework-Emulator[](https://github.com/Microsoft/BotFramework-Emulator)] に移動します。
- エミュレーターをダウンロードしてインストールします。

### <a name="create-an-app"></a>アプリの作成

1. [新しいアプリ] をクリックします。
2. [名前] フィールドに「Tutorial-16-Versioning」と入力します。
3. Click Create 
4. Click Settings
5. [アプリ ID] をコピーします。

### <a name="configure-the-emulator"></a>エミュレーターの構成

- Conversation Learner のルート フォルダーで .env ファイルを開きます。
- CONVERSATION_LEARNER_APP_ID の値としてアプリ ID を貼り付けます。
- コマンド プロンプトを終了し、次のコマンドを再度実行して、Conversation Learner サービスを再起動します。
 
    npm run tutorial-general 

### <a name="actions"></a>アクション

アクションを作成しましょう。

1. Web UI に切り替えます。
1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「hi there (version 1)」と入力します。
3. [保存] をクリックします。


![](../media/tutorial16_action1.PNG)

次の手順で新しいタグを作成します。

3. [設定] をクリックし、新しい "タグ" を作成します。
    - "version 1" という名前を付けます。
4. "version 1" を "ライブ" に設定します。  
    - ライブ タグを "version 1" に設定した結果、このボットを使用するチャンネルには "version 1" タグが使われるようになります。
    - タグ付けされているバージョンのアプリケーションは、編集 (アクションの変更、エンティティの変更、トレーニング会話の追加) の影響を受けません。  
    - アプリケーションに対する編集 (アクションの変更、エンティティの変更、トレーニング会話の追加) は常に "マスター" タグに対して行われます。  つまり "マスター" は変更できる唯一のタグであり、その他のタグは固定されたスナップショットと考えてください。
    - Conversation Learner UI の会話記録には常に (ライブ タグではなく) マスターが使用されます。

![](../media/tutorial16_v1_create.PNG)

設定を見ると、対応するバージョンが作成されていることがわかります。

![](../media/tutorial16_settings.PNG)

アクションをもう 1 つ追加しましょう。

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「bye bye (version 2)」と入力します。

1 つ目のアクションを編集します。

1. [アクション] をクリックします。
2. [アクション] に表示される [hi there (version 1)] をクリックします。
3. [応答] を "hi there (version 2)" に変更します。

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>ボット エミュレーターに切り替えます。

1. ボットの UI で「goodbye」と入力します。
2. ボットから "hi there (version 1)" という応答が返されることに注意してください。
    - このことからバージョン 1 が "ライブ" であることがわかります。 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Web UI に切り替えます。

1. [Log Dialogs]\(会話記録\) をクリックします (会話がまったく表示されない場合は、アプリを最新の情報に更新してください)。
2. [hi there (version 2)] をクリックします。

現在利用可能なすべてのアクションから選んで修正を加えることができることに注意してください。 それらの編集はマスターに対して実行されます。

以上、バージョン管理の動作について、また、Bot Framework エミュレーターを使ってボットを操作する方法について見てきました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - パスワードのリセット](./demo-password-reset.md)
