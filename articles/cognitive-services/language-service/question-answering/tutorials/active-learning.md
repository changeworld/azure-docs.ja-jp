---
title: アクティブ ラーニングを使用してプロジェクトを強化する
description: このチュートリアルでは、アクティブ ラーニングを使用して質問応答プロジェクトを強化する方法について説明します
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: cec2c7bd2f44d918de4392be90b2be0210931afb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091787"
---
# <a name="enrich-your-project-with-active-learning"></a>アクティブ ラーニングを使用してプロジェクトを強化する

このチュートリアルでは、以下の内容を学習します。

<!-- green checkmark -->
> [!div class="checklist"]
> * アクティブ ラーニング テスト ファイルをダウンロードする
> * 既存のプロジェクトにテスト ファイルをインポートする
> * アクティブ ラーニングの提案を受け入れるか拒否する
> * 代わりの質問を追加する

このチュートリアルでは、アクティブ ラーニングを使用して質問応答プロジェクトを強化する方法について説明します。 顧客がプロジェクトに含まれない質問をしていることに気付く場合があります。 多くの場合、質問には異なる言い方のバリエーションがあります。

これらのバリエーションを、関連する質問と回答のペアに代替の質問として追加すると、実際のユーザーの問い合わせに回答するようプロジェクトを最適化するのに役立ちます。 エディターを使用して、質問と回答のペアに代替の質問を手動で追加できます。 同時に、アクティブ ラーニング機能を使用して、ユーザーの問い合わせに基づいてアクティブ ラーニングの提案を生成できます。 ただし、アクティブ ラーニング機能で提案を生成するには、ナレッジ ベースが規則正しくユーザー トラフィックを受信する必要があります。

## <a name="enable-active-learning"></a>アクティブ ラーニングを有効にする

アクティブ ラーニングは、質問応答に対して既定で有効になっています。

アクティブ ラーニングの提案を試すために、次のファイルを新しいプロジェクトとしてプロジェクにインポートできます: [SampleActiveLearning.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv)。

## <a name="download-file"></a>ファイルのダウンロード

コマンド プロンプトから次のコマンドを実行して、`SampleActiveLearning.tsv` ファイルのローカル コピーをダウンロードします。

```cmd
curl "https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv" --output SampleActiveLearning.tsv
```

## <a name="import-file"></a>ファイルのインポート

プロジェクトの [edit knowledge base]\(ナレッジ ベースの編集\) ウィンドウで、メニューから `...` (省略記号) アイコン > **[Import questions and answers]\(質問と回答をインポートする\)**  >  **[Import as TSV]\(TSV としてインポート\)** の順に選択します。 **[ファイルの選択]** を選択して、前の手順でコンピューターにダウンロードした の `SampleActiveLearning.tsv` コピーを参照して [完了] を選択します。

> [!div class="mx-imgBorder"]
> [ ![[edit knowledge base]\(ナレッジ ベースの編集\) メニュー バーのスクリーンショット。[Import as TSV]\(TSV としてインポート\) オプションが表示されている。]( ../media/active-learning/import-questions.png) ]( ../media/active-learning/import-questions.png#lightbox)

## <a name="view-and-addreject-active-learning-suggestions"></a>アクティブ ラーニングの提案を表示し、追加または拒否する

テスト ファイルのインポートが完了すると、[レビューの提案] ウィンドウにアクティブ ラーニングの提案を表示できます。

> [!div class="mx-imgBorder"]
> [ ![[レビューの提案] ページが表示されているスクリーンショット。]( ../media/active-learning/review-suggestions.png) ]( ../media/active-learning/review-suggestions.png#lightbox)

これで、メニュー バーの **[Accept all suggestions]\(すべての提案を受け入れる\)** または **[Reject all suggestions]\(すべての提案を拒否する\)** のオプションを使用して、これらの提案を受け入れるか拒否することができます。

または、個々の提案を受け入れるか拒否するには、 **[Review suggestions]\(提案の確認\)** ページで個々の質問の横に表示されるチェックマーク (受け入れる場合) またはごみ箱マーク (拒否する場合) を選択します。

> [!div class="mx-imgBorder"]
> [ ![受け入れまたは拒否のオプションが赤で強調表示されているスクリーンショット。]( ../media/active-learning/accept-reject.png) ]( ../media/active-learning/accept-reject.png#lightbox)

## <a name="add-alternate-questions"></a>代わりの質問を追加する

アクティブ ラーニングでは、プロジェクトにヒットするユーザー クエリに基づいて代わりの質問が自動的に提案されますが、ナレッジ ベースの編集ページで **[Add alternate phrase]\(代わりの言い回しを追加する\)** を選択して、質問と回答のペアに質問のバリエーションを追加することもできます。

アクティブ ラーニングとともに代わりの質問を追加することで、質問のバリエーションによってユーザーの問い合わせに対して一貫性のある回答を提供でき、プロジェクトがさらに強化されます。

> [!NOTE]
> 代わりの質問に多くのストップ ワードが含まれる場合、回答の正確性に悪影響を与える可能性があります。 そのため、代替の質問の違いがストップ ワードだけである場合、これらの代替の質問は必要ありません。
> ストップ ワードの一覧を調べるには、[ストップ ワードに関する記事](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [同意語で応答の品質を向上させる](adding-synonyms.md)
