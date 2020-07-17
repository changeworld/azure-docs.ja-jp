---
title: ナレッジ ベースの設計 - QnA Maker
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA セットに関連付けられている省略可能なメタデータで構成されます。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756794"
---
# <a name="question-and-answer-set-concepts"></a>質問と回答セットの概念

ナレッジ ベースは、複数の質問と回答 (QnA) セットで構成されています。  各セットには 1 つの回答が含まれています。また、1 つのセットには、その "_回答_" に関連付けられているすべての情報が含まれます。 回答は、おおまかに言うと、データベースの行またはデータ構造インスタンスと似ています。

## <a name="question-and-answer-sets"></a>質問と回答セット

質問と回答 (QnA) セットで**必須の**設定は次のとおりです。

* **質問** - ユーザー クエリのテキスト。表現は異なっても回答は同じになるユーザーの質問のテキストに合わせるために、QnA Maker の機械学習で使用されます
* **回答** - セットの回答は、ユーザーのクエリが関連付けられている質問と一致するときに返される応答です

各セットは、**ID** で表されます。

セットの**省略可能な**設定は次のとおりです。

* **別の形式の質問** - これは、QnA Maker からさまざまな質問の表現に対して正しい回答を返すために役立ちます
* **Metadata**:メタデータは、QnA ペアに関連付けられたタグであり、キーと値のペアとして表されます。 メタデータ タグは、QnA ペアをフィルター処理してクエリとのマッチングを行う対象を制限するために使用されます。
* **複数ターンのプロンプト**は複数ターンの会話を続けるために使用されます

![QnA Maker ナレッジ ベース](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>編集という形でナレッジ ベースに追加する

ナレッジ ベースの内容にする既存のコンテンツがない場合は、QnA Maker ポータルで編集という形で QnA ペアを追加することができます。 ナレッジ ベースの更新方法については、[こちら](../How-To/edit-knowledge-base.md)を参照してください。

## <a name="editing-your-knowledge-base-locally"></a>ナレッジ ベースのローカルな編集

ナレッジ ベースを作成した後は、ローカル ファイルにエクスポートして再インポートするのではなく、[QnA Maker ポータル](https://qnamaker.ai)でナレッジ ベースのテキストを編集することをお勧めします。 ただし、ローカル環境でナレッジ ベースを編集することが必要になる場合があります。

**[設定]** ページからナレッジ ベースをエクスポートした後、Microsoft Excel でナレッジ ベースを編集します。 エクスポートしたファイルを別のアプリケーションで編集した場合、アプリケーションが TSV に完全に準拠していないため、構文エラーが発生する可能性があります。 Microsoft Excel の TSV ファイルでは一般に、書式エラーが発生することはありません。

編集が終わったら、 **[設定]** ページから TSV ファイルを再インポートします。 これにより、現在のナレッジ ベースが、インポートしたナレッジ ベースに完全に置き換えられます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker におけるナレッジ ベースのライフサイクル](./development-lifecycle-knowledge-base.md)