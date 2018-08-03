---
title: Conversation Learner モデルでバージョン管理とタグ付けを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでバージョン管理とタグ付けを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170547"
---
# <a name="how-to-use-versioning-and-tagging"></a>バージョン管理とタグ付けの使用方法

このチュートリアルでは、Conversation Learner モデルのバージョンにタグ付けし、どのバージョンが "ライブ" であるかを設定する方法を示します。  

## <a name="requirements"></a>必要条件
このチュートリアルでは、[Log Dialog]\(会話記録\) の Web UI ではなく、ボット エミュレーターを使用して会話記録を作成する必要があります。  

このチュートリアルでは、一般的なチュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

編集するときは常に、"マスター" と呼ばれるタグを編集することになります。タグ付けされたバージョンをマスターから作成する (実質的にはマスターのスナップショットを作成する) ことはできますが、タグ付けされたバージョンを編集することはできません。

## <a name="steps"></a>手順

### <a name="install-the-bot-framework-emulator"></a>Bot Framework エミュレーターのインストール

- [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) に移動します。
- エミュレーターをダウンロードしてインストールします。

### <a name="create-an-model"></a>モデルを作成する

1. [新しいモデル] をクリックする
2. [名前] フィールドに「Tutorial-16-Versioning」と入力します。
3. Click Create 
4. Click Settings
5. モデル ID をコピーする

### <a name="configure-the-emulator"></a>エミュレーターの構成

- Conversation Learner のルート フォルダーで .env ファイルを開きます。
- CONVERSATION_LEARNER_MODEL_ID の値としてモデル ID を貼り付けます。
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
    - タグ付けされているバージョンのモデルは、編集 (アクションの変更、エンティティの変更、トレーニング会話の追加) によって影響を受けません。  
    - モデルに対する編集 (アクションの変更、エンティティの変更、トレーニング会話の追加) は、常に "マスター" タグに対して実行されます。  つまり "マスター" は変更できる唯一のタグであり、その他のタグは固定されたスナップショットと考えてください。
    - Conversation Learner UI の会話記録には常に (ライブ タグではなく) マスターが使用されます。

![](../media/tutorial16_v1_create.PNG)

このバージョンは設定で作成されています。

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
2. ボットが "hi there (version 1)" で応答します。
    - このことからバージョン 1 が "ライブ" であることがわかります。 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Web UI に切り替えます。

1. [会話記録] をクリックします (会話がまったく表示されない場合は、更新ボタンをクリックします)。
2. [hi there (version 2)] をクリックします。

> [!NOTE]
> 現在使用可能なすべてのアクションから選択することによって、修正を行うことができます。 それらの編集はマスターに対して実行されます。

以上、バージョン管理の動作について、また、Bot Framework エミュレーターを使ってボットを操作する方法について見てきました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - パスワードのリセット](./demo-password-reset.md)
