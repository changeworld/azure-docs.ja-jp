---
title: 信頼度スコア - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 信頼度スコアは、回答が特定のユーザー クエリに最適である信頼度を示します。
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8fe4ecfe241422c03a2de8d0634ef1e1395aa050
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081321"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの信頼度スコア
ユーザー クエリがナレッジ ベースに対して一致すると、QnA Maker は、信頼度スコアと共に該当する回答を返します。 このスコアは、回答が特定のユーザー クエリに最適である信頼度を示します。 

信頼スコアは 0 から 100 の範囲の数値です。 100 点は完全一致であるの可能性が高いのに対して、0 点は一致する回答が見つからなかったことを意味します｡ スコアが高得点であるほど、回答の信頼度は高くなります。 与えられた 1 つのクエリに対して、複数の回答が返されることがあります｡ その場合、回答は信頼度スコアが高い順に返されます。

次の例では、1 つの QnA エンティティと 2 つの質問があります｡ 


![QnA のペア例](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

上記の例では、ユーザー クエリのさまざまな種類に応じて以下のスコア範囲サンプルのようなスコアを予想できます｡


![ランカーのスコア範囲](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


次の表は、特定のスコアに関連付けられる標準的な信頼度を示します。

|スコアの値|スコアの意味|サンプル クエリ|
|--|--|--|
|90 - 100|ユーザー クエリと KB の質問がほぼ完全に一致|"My changes aren't updated in Knowledge base after publish"|
|70 以上|高信頼度 - 通常､ユーザーのクエリに対する完全な答えとなる良い回答|"I published my KB but it's not updated"|
|50 - 70|中程度の信頼度 - 通常､ユーザー クエリの主な意図に答えるかなり良い回答|"Should I save my updates before I publish my KB?"|
|30 - 50|低信頼度 - 通常、ユーザーの意図の一部に答える回答。|" What does the save and train do?"|
|30以下|非常に低い信頼度 - 通常ユーザーのクエリに答えていないが､一部､一致する単語や語句がある回答 |" Where can I add synonyms to my KB"|
|0|一致なし｡回答は返されません。|"How much does the service cost"|

## <a name="choose-a-score-threshold"></a>スコアのしきい値を選択します。
上記の表は、たいていの KB で予想されるスコアをしています｡ ただし、各 KB はそれぞれに異なり、さまざまな種類の単語や意図､目的があるため､自分に最も合うしきい値をテストし､選ぶことをお勧めします｡ たいていの KB に合う､既定の推奨しきい値は **50** です。

しきい値を選択するさいは､Accuracy (精度) と Coverage (カバレッジ) のバランスを考慮し､自分の要件に基づいて、しきい値を調整してください。

- **Accuracy**(精度または正確さ) が重要な場合は､しきい値を大きくします。 そうすることで､回答が返されるたびに､信頼度の高いケースがずっと多くなり､ユーザーが求めている回答になる可能性がずっと高くなります｡探している可能性が高くなります。 このケースでは、最終的に回答を得られない質問が多くなる可能性があります｡ *例:* しきい値を **70** にした場合、｢what is save and train?｣などの曖昧な例が見逃される可能性があります｡

- **Coverage**(カバレッジまたはリコール)が重要で､ユーザーの質問に対して部分的な関係しかなくても､できるだけ多くの質問に答えるようにしたい場合は､しきい値を小さくします｡ このことは､ユーザーの実際のクエリに対する答えにはなっていないものの､他の多少関係のある回答が得られるケースが増える可能性があることを意味します｡ *例:* しきい値を **30** にした場合は、｢Where can I edit my KB?｣のようなクエリに対して､上記の例のようにあまり関係のない回答が返される可能性があります｡

> [!NOTE]
> QnA Maker の新しいバージョンにはスコアリング ロジックの機能強化が含まれ、しきい値に影響を与える可能性があります。 サービスを更新するときは常に、テストを行い、必要に応じてしきい値を調整してください。 お使いの QnA サービスのバージョンは、[こちら](https://www.qnamaker.ai/UserSettings)で確認できます。最新の更新プログラムの入手方法については、[こちら](../How-To/troubleshooting-runtime.md)をご覧ください。

## <a name="improve-confidence-scores"></a>信頼度スコアの向上
ユーザー クエリに対する特定の応答の信頼度スコアを上げるには、その応答に対する代替質問として、ユーザー クエリをナレッジ ベースに追加できます。


## <a name="similar-confidence-scores"></a>類似する信頼度スコア
複数の応答の信頼度スコアが似ている場合は、クエリが一般的すぎるために、複数の回答で信頼度が等しくなっている可能性があります。 QnA の構造を改善して、すべての QnA エンティティが明確な意図を持つようにすることを試してください。


## <a name="confidence-score-differences"></a>信頼度スコアの違い
コンテンツが同じ場合でも、ナレッジ ベースのテスト バージョンと公開バージョンの間で、回答の信頼度スコアが、無視できるほどですが、変化する場合があります。 これは、ナレッジ ベースのテスト バージョンと公開バージョンのコンテンツが、異なる Azure Search インデックスに配置されるためです。
公開のしくみについては、[こちら](../How-To/publish-knowledge-base.md)をご覧ください。


## <a name="no-match-found"></a>一致が見つからない
ランク付けによって適切な一致が見つからない場合は、0.0 または "None" の信頼度スコアが返され、既定の応答は [No good match found in the KB]\(KB で適切な一致が見つかりませんでした\) になります。 ボットまたはエンドポイントを呼び出すアプリケーション コード内のこの既定の応答を上書きできます。 別の方法として、Azure に上書き応答を設定することもできます。この場合は、特定の QnA Maker サービスにデプロイされたすべてのナレッジ ベースの既定値が変更されます。

### <a name="change-default-answer"></a>既定の回答の変更

1. [Azure Portal](https://portal.azure.com) に移動し、作成した QnA Maker サービスを表すリソース グループに移動します。

2. **[App Service]** をクリックして開きます。

    ![Azure portal で QnA Maker の App Service にアクセスする](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **[アプリケーション設定]** をクリックし、**DefaultAnswer** フィールドを目的の既定の応答に編集します。 **[Save]** をクリックします。

    ![[アプリケーション設定] を選択し、QnA Maker の DefaultAnswer を編集します。](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service を再起動します。

    ![DefaultAnswer の変更後、QnA Maker の App Service を再起動する](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [サポートされるデータ ソース](./data-sources-supported.md)
## <a name="see-also"></a>関連項目 
[QnA Maker の概要](../Overview/overview.md)
