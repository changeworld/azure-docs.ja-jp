---
title: 複数の言語のナレッジ ベースを作成する
description: このチュートリアルでは、複数の言語でナレッジ ベースを作成する方法について説明します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 41b15b70434c2515349074088d3b049de7482b1a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235270"
---
# <a name="create-knowledge-bases-in-multiple-languages"></a>複数の言語のナレッジ ベースを作成する

このチュートリアルでは、複数の言語のナレッジ ベースを作成するプロセスについて説明します。 [Surface Pen の FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) の URL を使用して、ドイツ語と英語のナレッジ ベースを作成します。 次に、ナレッジ ベースを公開し、[GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer) を使用してクエリを実行して、目的の言語で FAQ に対する回答を取得します。

## <a name="create-knowledge-base-in-german"></a>ドイツ語のナレッジ ベースを作成する

複数の言語でナレッジ ベースを作成するには、QnA サービスの最初のナレッジ ベース (KB) の作成時に言語設定を行う必要があります。

> [!NOTE]
> サービスに複数の言語のナレッジ ベースを追加できるようにするオプションは、Text Analytics の機能であるカスタム質問と回答の一部としてのみ提供されています。
>
> GA バージョンの QnA Maker を使用している場合は、言語ごとに個別の QnA Maker リソースを作成する必要があります。

> [!div class="mx-imgBorder"]
> [ ![[Connect your QnA service to your KB]\(QnA サービスを KB に接続する\) で、[Add knowledge bases in multiple languages to this service]\(このサービスに複数の言語のナレッジ ベースを追加する\) を選択している UI のスクリーンショット。]( ../media/multiple-languages/add-knowledge-bases.png) ]( ../media/multiple-languages/add-knowledge-bases.png#lightbox)

**手順 2** で、[Add knowledge bases in multiple languages to this service]\(このサービスに複数の言語のナレッジ ベースを追加する\) をオンにして、[Language]\(言語\) ドロップダウン リストから、KB の言語として **[German]\(ドイツ語\)** を選択します。
手順 3 と 4 で関連する詳細を入力し、最後に **[Create your KB]\(KB の作成\)** を選択します。
 
この手順では、QnA Maker がドキュメントを読み取り、ソース URL から QnA ペアを抽出して、ドイツ語でナレッジ ベースを作成します。 ナレッジ ベースのページが開き、ここでナレッジ ベースの内容を編集できます。

> [!div class="mx-imgBorder"]
> [ ![ドイツ語の質問と回答を示す UI のスクリーンショット]( ../media/multiple-languages/german.png) ]( ../media/multiple-languages/german.png#lightbox)

## <a name="create-knowledge-base-in-english"></a>英語のナレッジ ベースを作成する

上記の手順を繰り返し、手順 2 と手順 4 で言語固有の変更を加えて、英語のナレッジ ベースを作成します。
1.  **手順 2**: 言語として **[English]\(英語\)** を選択します。
2.  **手順 4**: 選択した言語のソース ファイルを選択して、英語のナレッジ ベースを作成します。
ナレッジ ベースが作成されると、次に示すように、QnA Maker によって生成された関連する QnA ペアが英語で表示されます。

> [!div class="mx-imgBorder"]
> [ ![REST API の応答を示すスクリーンショット]( ../media/multiple-languages/english.png) ]( ../media/multiple-languages/english.png#lightbox)

## <a name="publish-and-query-knowledge-base"></a>ナレッジ ベースを公開してクエリを実行する

これで、2 つのナレッジ ベースを公開し、[GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer) を使用して目的の言語でクエリを実行する準備ができました。 ナレッジ ベースが公開されると、ナレッジ ベースに対してクエリを実行するための詳細を示す、次のページが表示されます。

> [!div class="mx-imgBorder"]
> [ ![英語の質問と回答を示す UI のスクリーンショット]( ../media/multiple-languages/postman.png) ]( ../media/multiple-languages/postman.png#lightbox)

受信ユーザー クエリの言語は [Language Detection API](../../text-analytics/how-tos/text-analytics-how-to-language-detection.md) で検出できます。ユーザーは、検出された言語に応じて適切なエンドポイントとナレッジ ベースを呼び出すことができます。