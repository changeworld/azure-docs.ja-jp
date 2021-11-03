---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: aeb4c7ef44791b174940790fa3e03f0f40ed1ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092566"
---
リソースとストレージ コンテナーを構成したら、新しいカスタム NER プロジェクトを作成します。 プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使用されている Azure リソースへの共同作成者アクセス権を持つユーザーのみです。

1. [Language Studio ポータル](https://aka.ms/languageStudio)にサインインします。 サブスクリプションと言語リソースを選ぶためのウィンドウが表示されます。 上の手順で作成したリソースを選びます。 

2. **[エンティティの抽出]** セクションを見つけて、使用可能なサービスから **[Custom named entity recognition]\(カスタム固有表現認識\)** を選びます。

3. プロジェクト ページの上部のメニューで **[Create new project]\(新しいプロジェクトの作成\)** を選びます。 プロジェクトを作成すると、データのタグ付け、モデルのトレーニング、評価、改善、デプロイを実行できます。 

    
    :::image type="content" source="../media/create-project.png" alt-text="プロジェクト作成ページのスクリーンショット。" lightbox="../media/create-project.png":::


4.  **[Create new project]\(新しいプロジェクトの作成\)** をクリックすると、ストレージ アカウントを接続するための画面が表示されます。 ストレージ アカウントが見つからない場合は、前の手順を使ってリソースを作ったことを確認してください。 

    >[!NOTE]
    > * この手順は、使用する新しいリソースごとに 1 回だけ行う必要があります。 
    > * このプロセスは元に戻すことができません。ストレージ アカウントをリソースに接続すると、後で切断することはできません。
    > * リソースは 1 つのストレージ アカウントにのみ接続できます。
    > * ストレージ アカウントを既に接続している場合は、代わりに **[Select project type]\(プロジェクトの種類の選択\)** 画面が表示されます。 次のステップを参照してください。
    
    :::image type="content" source="../media/connect-storage.png" alt-text="ストレージ接続画面を示すスクリーンショット。" lightbox="../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. 名前、説明、プロジェクト内のファイルの言語など、プロジェクト情報を入力します。 プロジェクトの名前は後で変更できません。 

6. 入力したデータを確認し、 **[Create Project]\(プロジェクトの作成\)** を選択します。
