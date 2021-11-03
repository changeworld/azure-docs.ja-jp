---
title: カスタム固有表現認識 (NER) 用のデータにタグを付ける方法
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) で使うためにデータにタグを付ける方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 2328971a659bd4d4b147f9a708c1b5910e8ce12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091487"
---
# <a name="tag-your-data-for-custom-named-entity-recognition-ner-in-language-studio"></a>Language Studio でカスタム固有表現認識 (NER) 用のデータにタグを付ける

カスタム エンティティ抽出モデルを構築する前に、データにタグを付ける必要があります。 データにまだタグが付けられていない場合は、Language Studio でタグを付けることができます。 データにタグを付けるには、[プロジェクトを作成](../quickstart.md)しておく必要があります。

## <a name="prerequisites"></a>前提条件

データにタグを付けるには、次のものが必要です。

* 構成済みの Azure Blob Storage アカウントで正常に[作成されたプロジェクト](create-project.md)
    * ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#application-development-lifecycle)に関する記事をご覧ください。

## <a name="tag-your-data"></a>データにタグを付ける

トレーニング データを Azure ストレージ アカウントにアップロードした後は、それにタグを付けて、必要なクラスに関連付けられる単語をモデルで認識できるようにする必要があります。 Language Studio でデータにタグを付けると (または、手動でデータにタグを付けると)、これらのタグはトレーニング中にモデルによって使われる [JSON](../concepts/data-formats.md) 形式で格納されます。  

データにタグを付けるときは、次のことに注意してください。

* **正確にタグを付ける**: 各エンティティを適切な型に常にタグ付けします。 抽出するデータのみを含め、タグに不要なデータを含めないでください。
* **一貫性があるようにタグ付けをする**: 同一のエンティティは、すべてのファイルで同じタグを持つ必要があります。
* **完全にタグ付けする**: すべてのファイル内のエンティティのすべてのインスタンスにタグを付けます。

タグを付けられたデータの正確さ、整合性、完全性は、モデルのパフォーマンスを決定する重要な要因です。 データにタグを付けるには:

1. [Language Studio](https://aka.ms/custom-extraction) でプロジェクトのページに移動し、プロジェクトを選びます。

2. 左側のメニューから、 **[Tag data]\(データにタグを付ける\)** を選びます

3. プロジェクトで使用できるすべての `.txt` ファイルの一覧を、左側で確認できます。 タグ付けを始めるファイルを選択したり、ページの下部にある **[戻る]** と **[次へ]** ボタンを使って移動したりできます。

4. タグ付けを始めるには、右上隅にある **[エンティティの追加]** をクリックします。 **[Viewing]\(表示\)** ドロップダウンから表示を変更して、すべてのファイルまたはタグ付けされたファイルのみを表示できます。

>[!TIP]
> * 必要なタグの数に標準はありません。エンティティごとに 50 個のタグから始めてみてください。 必要なタグの数は、エンティティのわかりやすさと、エンティティ間の区別のしやすさによって異なります。 また、タグ付けにも依存し、一貫性があって完全なものにする必要があります。

:::image type="content" source="../media/tagging-screen.png" alt-text="データにタグを付ける Language Studio の画面を示すスクリーンショット。" lightbox="../media/tagging-screen.png":::

プロジェクトで複数の言語を有効にした場合は、 **[言語]** ドロップダウンが表示され、ドキュメントごとに言語を選ぶことができます。

タグ付けの間、変更は定期的に同期され、まだ保存されていない場合は、ページの上部に警告が表示されます。 手動で保存する場合は、ページの上部にある **[Save tags]\(タグの保存\)** ボタンをクリックします。

## <a name="tagging-options"></a>タグ付けのオプション

ドキュメントにタグを付けるには、2 つのオプションがあります。


|オプション |説明  |
|---------|---------|
|ブラシを使ってタグを付ける     | 画面の右上隅のエンティティの横にあるブラシ アイコンを選んでから、エンティティと関連付けるドキュメント内の単語を強調表示にします           |
|メニューを使ってタグを付ける    | エンティティとしてタグ付けする単語を強調表示にすると、メニューが表示されます。 このエンティティに割り当てるタグを選びます。        |

次のスクリーンショットは、ブラシを使ったタグ付けを示したものです。

:::image type="content" source="../media/tag-options.png" alt-text="カスタム NER で提供されるタグ付けオプションを示すスクリーンショット。" lightbox="../media/tag-options.png":::

## <a name="remove-tags"></a>タグを削除する

タグを削除するには

1. タグを削除するエンティティを選びます。
2. 表示されるメニューをスクロールして、 **[Remove Tag]\(タグの削除\)** を選びます。

## <a name="delete-or-rename-entities"></a>エンティティの削除または名前の変更

エンティティを削除または名前変更するには:

1. メニューの右上隅で、編集するエンティティを選びます。
2. エンティティの横にある 3 つのドットをクリックし、ドロップダウン メニューから目的のオプションを選びます。


## <a name="next-steps"></a>次の手順

データのタグ付けが済むと、データに基づいて学習する[モデルのトレーニング](train-model.md)を始めることができます。
