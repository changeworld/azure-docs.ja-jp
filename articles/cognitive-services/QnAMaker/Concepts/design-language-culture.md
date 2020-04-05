---
title: 言語の設計 - QnA Maker
description: QnA Maker リソースとそのリソース内のすべてのナレッジ ベースは、1 つの言語をサポートします。 クエリに対して最適な回答結果を提供するために、1 つの言語が必要です。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843423"
---
# <a name="design-knowledge-base-for-content-language"></a>コンテンツ言語のナレッジ ベースを設計する

QnA Maker リソース、およびそのリソース内のすべてのナレッジ ベースは、1 つの言語をサポートします。 クエリに対して最適な回答結果を提供するために、1 つの言語が必要です。

## <a name="single-language-per-resource"></a>リソースごとに 1 つの言語

言語サポートに関する QnA Maker の考慮事項:

* QnA Maker サービスとそのすべてのナレッジ ベースは、1 つの言語のみをサポートします。
* 言語は、サービスの最初のナレッジ ベースが作成されるときに明示的に設定されます
* 言語は、ナレッジ ベースの作成時に追加されたファイルと URL から決定されます
* サービス内の他のナレッジ ベースの言語は変更できません
* この言語は Cognitive Search Service (ランカー #1) と QnA Maker サービス (ランカー #2) に使用され、クエリに対して最適な回答が生成されます

## <a name="supporting-multiple-languages"></a>多言語のサポート

複数の言語を含むナレッジ ベース システムをサポートする必要がある場合は、次のいずれかの方法を選択できます。

* ナレッジ ベースに質問を送信する前に、[Translation Text サービス](../../translator/translator-info-overview.md)を使用して質問を 1 つの言語に翻訳します。 これで、1 つの言語の品質と、代替の質問と回答の品質に集中できるようになります。
* すべての言語について、QnA Maker リソースとそのリソース内のナレッジ ベースを作成します。 これで、言語ごとに、より微妙な個別の代替の質問と回答のテキストを管理できるようになります。 これにより、柔軟性が大幅に向上しますが、すべての言語で質問または回答が変わると、メンテナンス コストが大幅に増加します。

QnA Maker の[サポートされている言語](../overview/language-support.md)を確認します。

### <a name="support-each-language-with-a-qna-maker-resource"></a>QnA Maker リソースで各言語をサポートします

* すべての言語の QnA Maker リソースを作成します
* その言語のファイルと URL のみを追加します
* 言語を識別するために、リソースの名前付け規則を使用します。 たとえば、フランス語ドキュメントのすべてのナレッジ ベースには `qna-maker-fr` などです。

## <a name="next-steps"></a>次のステップ

ナレッジ ベースのクエリを実行して回答を得る方法については、[概念](query-knowledge-base.md)に関する記事を参照してください。