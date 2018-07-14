---
title: 信頼度スコア - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 信頼スコアの説明
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376309"
---
# <a name="confidence-score"></a>信頼度スコア

信頼度スコアは、ユーザーの質問と返された応答の間の一致の程度を示します。

ユーザー クエリがナレッジ ベースのコンテンツと照合されたときに、複数の応答が返される可能性があります。 応答は、信頼度スコアの高いほうから降順で返されます。

信頼度スコアの値は 0 から 100 の範囲です。

|スコアの値|信頼度|
|--|--|
|100|ユーザー クエリと KB の質問が完全に一致|
|90|高い信頼度 - 単語のほとんどが一致|
|40-60|中程度の信頼度 - 重要な単語が一致|
|10|低い信頼度 - 重要な単語が一致しない|
|0|一致する単語がない|


## <a name="similar-confidence-scores"></a>類似する信頼度スコア
複数の応答の信頼度スコアが似ている場合は、クエリが一般的すぎるために、複数の回答で信頼度が等しくなっている可能性があります。 QnA の構造を改善して、すべての QnA エンティティが明確な意図を持つようにすることを試してください。


## <a name="improving-confidence-scores"></a>信頼度スコアの向上
ユーザー クエリに対する特定の応答の信頼度スコアを上げるには、その応答に対する代替質問として、ユーザー クエリをナレッジ ベースに追加できます。
   
## <a name="confidence-score-differences"></a>信頼度スコアの違い
コンテンツが同じ場合でも、ナレッジ ベースのテスト バージョンと公開バージョンの間で、回答の信頼度スコアが、無視できるほどですが、変化する場合があります。 これは、ナレッジ ベースのテスト バージョンと公開バージョンのコンテンツが、異なる Azure Search インデックスに配置されるためです。

公開のしくみについては、[こちら](../How-To/publish-knowledge-base.md)をご覧ください。


## <a name="no-match-found"></a>一致が見つからない
ランク付けによって適切な一致が見つからない場合は、0.0 または "None" の信頼度スコアが返され、既定の応答は [No good match found in the KB]\(KB で適切な一致が見つかりませんでした\) になります。 ボットまたはエンドポイントを呼び出すアプリケーション コード内のこの既定の応答を上書きできます。 別の方法として、Azure に上書き応答を設定することもできます。この場合は、特定の QnA Maker サービスにデプロイされたすべてのナレッジ ベースの既定値が変更されます。

1. [Azure Portal](http://portal.azure.com) に移動し、作成した QnA Maker サービスを表すリソース グループに移動します。

2. **[App Service]** をクリックして開きます。

    ![App Service にアクセスする](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **[アプリケーション設定]** をクリックし、**DefaultAnswer** フィールドを目的の既定の応答に編集します。 **[保存]** をクリックします。

    ![既定の応答を変更する](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service を再起動します。

    ![QnA Maker App Service の再起動](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サポートされるデータ ソース](./data-sources-supported.md)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)
