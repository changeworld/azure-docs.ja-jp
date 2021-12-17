---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71810497c555a33fcf8a7359c32c8397cb6cec8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090008"
---
リソースとストレージ コンテナーが構成されたら、新しいテキスト分類プロジェクトを作成します。 プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使用されている Azure リソースへの共同作成者アクセス権を持つユーザーのみです。

1. [Language Studio](https://aka.ms/languageStudio) にログインします。 サブスクリプションと言語リソースを選ぶためのウィンドウが表示されます。 上の手順で作成したリソースを選びます。

2. Language Studio の **[Classify text]\(テキストの分類\)** セクションで、利用可能なサービスから **[カスタム テキスト分類]** を選択します。

3. プロジェクト ページの上部メニューから、 **[Create new project]\(新しいプロジェクトの作成\)** を選択します。 プロジェクトを作成すると、データのタグ付け、モデルのトレーニング、評価、改善、デプロイを実行できます。 

    :::image type="content" source="../media/create-project.png" alt-text="プロジェクト作成ページのスクリーンショット。" lightbox="../media/create-project.png":::

4. 上記の手順を使用してリソースを作成した場合は、プロジェクトに関する情報 (名前など) を追加し、ストレージ コンテナーを選択する必要があります。

    1. プロジェクトの種類を選択します。 このクイックスタートでは、複数ラベル分類プロジェクトを作成します。 続けて、 **[次へ]** をクリックします。

    2. 名前、説明、プロジェクト内のファイルの言語など、プロジェクトの情報を入力します。 プロジェクトの名前は後で変更できません。

        >[!TIP]
        > データセットは、すべて同じ言語である必要はありません。 それぞれ異なるサポート言語の複数のファイルを含めることができます。 異なる言語のファイルがデータセットに含まれている場合、または実行時に異なる言語を想定している場合は、プロジェクトの基本情報を入力するときに **[enable multi-lingual dataset]\(多言語データセットを有効にする\)** を選択します。

    3. データをアップロードしたコンテナーを選択します。 このクイックスタートでは、コンテナー内にある既存のタグ ファイルを使用します。 続けて、 **[次へ]** をクリックします。
 
    4. 入力したデータを確認し、 **[Create Project]\(プロジェクトの作成\)** を選びます。
