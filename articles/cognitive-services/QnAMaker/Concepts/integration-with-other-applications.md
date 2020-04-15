---
title: 他のアプリケーションとの統合 - QnA Maker
description: QnA Maker は、チャット ボットなどのクライアント アプリケーションや、Language Understanding (LUIS) などの他の自然言語処理サービスに統合されます。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804190"
---
# <a name="design-knowledge-base-for-client-applications"></a>クライアント アプリケーション用にナレッジ ベースを設計する

QnA Maker は、チャット ボットなどのクライアント アプリケーションや、Language Understanding (LUIS) などの他の自然言語処理サービスに統合されます。

## <a name="integration-with-a-conversational-client"></a>会話クライアントとの統合

QnA Maker は、[Microsoft Bot Framework](https://dev.botframework.com/) などの会話クライアント アプリケーションと統合されています。 QnA Maker に送信されたテキストは、除去したり変換したりする必要はありません。 QnA Maker では自然言語を受け入れ、最適な回答を返します。

## <a name="create-a-bot-without-writing-any-code"></a>コードを記述せずにボットを作成する

ナレッジ ベースを公開した後、 **[公開]** ページで **[Create bot]\(ボットの作成\)** ボタンを選択して、ボットを作成します。 [ボットのチュートリアル](../Quickstarts/create-publish-knowledge-base.md)を使用して、ボタンを選択した後の動作について学習します。

## <a name="providing-multi-turn-conversations"></a>複数ターン会話の提供

ボット クライアントによって、ナレッジ ベースから選択された最適な回答が提供され、回答が複数ターンの QnA ペアの一部である場合は、フォローアップ プロンプトを表示できます。 ナレッジ ベースに複数ターンの質問と回答のセットを追加する[方法](../how-to/multiturn-conversation.md)について学習します。

## <a name="natural-language-processing"></a>自然言語処理

QnA Maker では自然言語処理を使用する質問を処理しますが、複数のナレッジ ベースからの質問に回答する大規模なシステムの一部として使用することもできます。 特定のナレッジ ベースを取得する前に自然言語処理を提供するために、QnA Maker を別の Cognitive Service (Language Understanding (LUIS)) と組み合わせることができます。 [LUIS と QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) を一緒に使用するタイミングと方法について学習します。

## <a name="next-steps"></a>次のステップ

QnA Maker のための開発サイクルの[概念](development-lifecycle-knowledge-base.md)について学習します。