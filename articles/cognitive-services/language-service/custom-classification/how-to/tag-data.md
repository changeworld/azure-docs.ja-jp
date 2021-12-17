---
title: カスタム分類用にデータにタグを付ける - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: カスタム テキスト分類 API で使用するためにデータにタグを付ける方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 390e5fabe5c23c3b258e898eab0691f88c1828c0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057178"
---
# <a name="tag-text-data-for-training-your-model"></a>モデルをトレーニングするためにテキスト データにタグを付ける 

カスタム テキスト分類モデルを作成する前に、まずタグ付けされたデータが必要です。 データにまだタグが付けられていない場合は、Language Studio でタグを付けることができます。 タグ付けされたデータは、テキストの解釈方法をモデルに示し、トレーニングと評価に使用されます。

## <a name="prerequisites"></a>前提条件

データにタグを付けるには、次のものが必要です。
* 構成済みの Azure Blob Storage アカウントで[正常に作成されたプロジェクト](create-project.md) 
* ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#project-development-lifecycle)に関する記事をご覧ください。

<!--Tagging your data will let you [train your model](train-model.md), [evaluate it](train-model.md), and use it to [classify text](call-api.md).-->

## <a name="tag-your-data"></a>データにタグを付ける

トレーニング データを Azure ストレージ アカウントにアップロードした後は、それにタグを付けて、必要なクラスに関連付けられる単語をモデルで認識できるようにする必要があります。 Language Studio でデータにタグを付けると (または、手動でデータにタグを付けると)、これらのタグはトレーニング中にモデルによって使われる [JSON](../concepts/data-formats.md) 形式で格納されます。 

データにタグを付けるときは、次のことに注意してください。

* 一般に、データが正確にタグ付けされていれば、タグ付けされたデータが多いほど良い結果が得られます。

* クラスごとに約 50 のタグ付けされたファイルを準備することをお勧めしていますが、モデルのパフォーマンスは、[スキーマ](design-schema.md)の潜在的なあいまいさやタグ付けされたデータの品質にも依存するため、モデルの最高のパフォーマンスを保証できる決まった数はありません。

次の手順に従って、データをタグ付けします

1. [Language Studio](https://aka.ms/custom-classification) でプロジェクトのページに移動します。

1. 左側のメニューから、 **[Tag data]\(データにタグを付ける\)** を選びます

3. プロジェクトで使用できるすべての .txt ファイルの一覧が左側に表示されます。 タグ付けを始めるファイルを選んだり、ページの下部にある [戻る] と [次へ] ボタンを使って移動したりできます。  
    
4.  **[Viewing]\(表示\)** ドロップダウン メニューから表示を変更して、すべてのファイルまたはタグ付けされたファイルのみを表示できます。 

    > [!NOTE]
    > プロジェクトに対して複数の言語を有効にした場合は、追加の **[言語]** ドロップダウン メニューが表示されます。 各ドキュメントの言語を選びます。

5. タグ付けを開始する前に、右上隅からプロジェクトにクラスを追加します


    :::image type="content" source="../media/tag-1.png" alt-text="データのタグ付け画面を示すスクリーンショット" lightbox="../media/tag-1.png":::

6. ファイルのタグ付けを開始します。

    * **単一ラベル分類**: ファイルを 1 つのクラスでのみタグ付けできます。これを行うには、このファイルをタグ付けするクラスの横にあるラジオ ボタンのいずれかをオンにします。

      :::image type="content" source="../media/tag-single.png" alt-text="単一ラベル分類メニューを示すスクリーンショット" lightbox="../media/tag-single.png":::

    * **複数ラベル分類**: ファイルを複数のクラスでタグ付けできます。これを行うには、このファイルをタグ付けするクラスの横にある該当するチェック ボックスをすべてオンにします。

      :::image type="content" source="../media/tag-multi.png" alt-text="複数ラベル分類メニューを示すスクリーンショット" lightbox="../media/tag-multi.png":::

タグ付けの間、変更は定期的に同期され、まだ保存されていない場合は、ページの上部に警告が表示されます。 手動で保存する場合は、ページの上部にある [タグの保存] ボタンをクリックします。

## <a name="remove-tags"></a>タグを削除する

タグを削除する場合は、クラスの横にあるボタンをオフにします。

## <a name="delete-or-classes"></a>クラスの削除または名前変更

クラスを削除または名前変更するには

1. 右側のメニューから編集するクラスを選びます
2. 3 つのドットをクリックし、ドロップダウン メニューから目的のオプションを選びます。

## <a name="next-steps"></a>次のステップ

データのタグ付けが済むと、データに基づいて学習する[モデルのトレーニング](train-model.md)を始めることができます。
