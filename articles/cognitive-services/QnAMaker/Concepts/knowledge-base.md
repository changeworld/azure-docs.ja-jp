---
title: ナレッジ ベース - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: cb3426a960a6644b3ae149f02055cdb083febca7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040763"
---
# <a name="knowledge-base"></a>ナレッジ ベース

QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。

## <a name="key-knowledge-base-concepts"></a>ナレッジ ベースの重要な概念

* **質問** - 質問には、ユーザーのクエリを最も適切に表すテキストが含まれます。 
* **回答** - 回答は、ユーザーのクエリが関連付けられている質問と一致するときに返される応答です。  
* **メタデータ** - メタデータは、QnA ペアに関連付けられ、キーと値のペアとして表されるタグです。 メタデータは、QnA ペアをフィルター処理してクエリとのマッチングを行う対象を制限するために使用されます。

数値の QnA ID で表される 1 つの QnA には、さまざまなバリエーションの質問 (代替の質問) が含まれ、そのすべてが 1 つの回答にマッピングされています。 さらに、各ペアには複数のメタデータ フィールドを関連付けることができます。

![QnA Maker ナレッジ ベース](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>ナレッジ ベースのコンテンツ形式

リッチ コンテンツをナレッジ ベースに取り込むと、QnA Maker はコンテンツを markdown に変換しようとします。 ほとんどのチャット クライアントが解釈できる markdown 形式について理解するには、[こちらの](https://aka.ms/qnamaker-docs-markdown-support)ブログをお読みください。

メタデータ フィールドは、コロンで区切られたキーと値のペアで構成されます **(製品:シュレッダー)**。 キーと値は、どちらもテキストである必要があります。 メタデータのキーにスペースを含めることはできません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの開発ライフサイクル](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>関連項目

[QnA Maker の概要](../Overview/overview.md)