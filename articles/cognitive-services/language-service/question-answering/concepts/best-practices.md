---
title: ベスト プラクティス - 質問応答
description: 以下のベスト プラクティスを利用してプロジェクトを改善し、アプリケーション/チャット ボットのエンド ユーザーにより良い情報を提供します。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 552e3d014ffecf5f43977420c9aa8c6bb1b59080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092346"
---
# <a name="question-answering-best-practices"></a>質問応答のベスト プラクティス

以下のベスト プラクティスを利用してナレッジ ベースを改善し、対象のアプリケーションまたはチャット ボットのエンド ユーザーにより良い情報を提供します。

## <a name="extraction"></a>抽出

質問応答は、コンテンツから質問と回答のペアを抽出するアルゴリズムを継続的に改善し、サポートされるファイルと HTML 形式のリストを拡大しています。 一般的に、FAQ ページは独立させ、他の情報と組み合わせないでください。 製品マニュアルにはわかりやすい見出しを付けてください。索引ページを付けることもお勧めします。

## <a name="creating-good-questions-and-answers"></a>優れた質問と回答の作成

### <a name="good-questions"></a>優れた質問

最高の質問は単純です。 各質問のキーワードまたはキー フレーズを検討した後、そのキーワードまたはキー フレーズに関する単純な質問を作成します。

同じような意味の質問を必要な数だけ追加しますが、変更は単純にしておきます。 質問の主な目的の一部ではないワードまたはフレーズを追加しても、質問応答アルゴリズムで一致するものを検索するための助けにはなりません。

### <a name="add-relevant-alternative-questions"></a>関連する他の質問を追加する

ユーザーは、`How do I add a toner cartridge to my printer?` のような会話形式のテキストか、`toner cartridge` のようなキーワード検索のどちらかで質問を入力する可能性があります。 最善の回答を正しく返すためには、プロジェクトにこの両方のスタイルの質問が用意されている必要があります。 顧客がどのキーワードを入力しているのかわからない場合は、[Azure Monitor](../how-to/analytics.md) のデータを使用してクエリを分析します。

### <a name="good-answers"></a>優れた回答

最善の回答はシンプルな回答ですが、シンプルすぎてはいけません。 `yes` や `no` などの回答は使用してはいけません。 回答を他のソースにリンクしたり、メディアとリンクを使用してリッチ エクスペリエンスを提供したりする必要がある場合は、メタデータ タグ付けを使用して回答を識別した後、`strictFilters` プロパティにそのメタデータ タグを含むクエリを送信して、正しい回答のバージョンを取得します。

|回答|フォローアップ プロンプト|
|--|--|
|キーボードの電源ボタンを押して Surface ノート PC の電源を切ってください。|* スリープ、シャットダウン、再起動を行うキーの組み合わせ。<br>* Surface ノート PC 本体を起動する方法<br>* Surface ノート PC の BIOS を変更する方法<br>* スリープ、シャットダウン、再起動の違い|
|カスタマー サービスは、電話、Skype、テキスト メッセージで 24 時間ご利用いただけます。|* 販売に関する連絡先情報。<br> * 直接訪問できるオフィスと店舗の場所と営業時間。<br> * Surface ノート PC のアクセサリ。|

## <a name="chit-chat"></a>おしゃべり

少ない労力でボットをより話し上手で魅力的にするために、おしゃべりをボットに追加します。 プロジェクトの作成時に、事前定義済みの性格に対応したおしゃべりデータ ソースを簡単に追加し、いつでも性格を変更することができます。 [おしゃべりをナレッジ ベースに追加](../How-To/chit-chat.md)する方法を学びます。

おしゃべりは[多くの言語](../how-to/chit-chat.md#language-support)でサポートされています。

### <a name="choosing-a-personality"></a>性格の選択

おしゃべりでは、次のいくつかの事前定義済みの性格がサポートされています。

|パーソナリティ |質問応答 - データセット ファイル |
|---------|-----|
|2 次元形式 |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Friendly |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring (温かい) |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthusiastic (熱心) |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

改まった応答からくだけた応答、非礼な応答まで可能です。 ボットに演じさせたい雰囲気に最も近い性格を選択する必要があります。 [データセット](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)を表示し、ボットの基本として機能するデータセットを選択した後、応答をカスタマイズすることができます。

### <a name="edit-bot-specific-questions"></a>ボット固有の質問の編集

おしゃべりデータ セットの一部であり、一般的な回答が入力されているボット固有の質問がいくつかあります。 ボットの詳細を最も的確に反映するように、これらの回答を変更します。

次のようなおしゃべりの質問と回答のペアをより具体的に設定することをお勧めします。

* ユーザーはだれか
* 何を実行できるか
* 何歳ですか?
* あなたを創造したのは誰ですか?
* こんにちは

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>メタデータ タグを使用したカスタムのおしゃべりの追加

独自のおしゃべりの質問と回答のペアを追加する場合は、必ずメタデータを追加して、これらの回答が返されるようにします。 メタデータの名前と値のペアは `editorial:chitchat` です。

## <a name="searching-for-answers"></a>回答の検索

質問応答 REST API では、質問と回答の両方を使用して、ユーザーのクエリに対する最善の回答を検索します。

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>回答が適切でない場合にのみ質問を検索する

回答を検索しない場合は、[`RankerType=QuestionOnly`](#choosing-ranker-type) を使用します。

この例としては、ナレッジ ベースが、質問としての頭字語と完全形としての回答を含むカタログである場合があります。 回答の値は、適切な回答を検索するのに役立ちません。

## <a name="rankingscoring"></a>ランキング/スコア

サポートされている優先度付け機能を効果的に活用してください。 それにより、ユーザーからの問い合わせに適切な回答が与えられる可能性が上がります。

### <a name="choosing-a-threshold"></a>しきい値の選択

しきい値として使用される既定の[信頼度スコア](confidence-score.md)は 0 ですが、ニーズに基づいてプロジェクトの[しきい値を変更](confidence-score.md#set-threshold)できます。 すべてのプロジェクトは異なっているため、プロジェクトに最も適したしきい値をテストして選ぶ必要があります。

### <a name="choosing-ranker-type"></a>ランカーの種類の選択

既定では、質問応答によって質問と回答が検索されます。 質問のみを検索して、回答を生成する場合、REST API 要求の POST 本文で `RankerType=QuestionOnly` を使用します。

### <a name="add-alternate-questions"></a>代わりの質問を追加する

代わりの質問を用意すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。 代わりの質問は、同じ質問にさまざまな表現が使われるときに役立ちます。 これには、文の構造や言葉遣いの変化が含まれることがあります。

|元の質問|代わりの質問|Change|
|--|--|--|
|駐車場は空いていますか?|駐車場はありますか?|文の構造|
 |やあ|よう<br>おい!|言葉遣いまたはスラング|

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>メタデータ タグを使用して質問と回答をフィルター処理する

メタデータは、クライアント アプリケーションがすべての回答を取得する代わりにメタデータ タグに基づいてユーザー クエリの結果を絞り込む必要があることを認識する機能を追加します。 プロジェクトおよびナレッジ ベースの回答は、問い合わせが同じであっても、メタデータ タグに基づいて異なる場合があります。 たとえば、*"where is parking located"* (駐車場はどこですか) という問いに対し、レストランの支店の場所が違えば、つまり、*Location: Seattle* (所在地: シアトル) のときと、*Location: Redmond* (所在地: レドモンド) のときで答えが変わります。

### <a name="use-synonyms"></a>同義語を使用する

英語では同義語をある程度サポートしていますが、さまざま言い方があるキーワードに同義語を追加するには、大文字と小文字が区別される[言葉の変更](../tutorials/adding-synonyms.md)機能を利用します。

|元の語|シノニム|
|--|--|
|買う|購入する<br>net 銀行<br>ネット バンキング|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>質問を区別する目的で明確な言葉を使う

優先度付けアルゴリズムはユーザーからの問い合わせとプロジェクトおよびナレッジ ベースに登録されている質問を一致させるアルゴリズムです。これは、それぞれの質問が異なるニーズに対処する場合に最も効果を発揮します。 複数の質問で同じ言葉が繰り返し使われていると、その言葉が含まれる問い合わせが届いたとき、正しい回答が選ばれる可能性が下がります。

たとえば、2 つの個別の質問と回答のペアがあり、次のような質問内容であるとします。

|疑問がある場合|
|--|
|駐車 *場所* はどこですか|
|ATM の "*場所*" はどこですか|

これら 2 つの質問は非常によく似た語で表現されていますが、この類似性のために、「 *`<x>` の場所はどこですか*」のように表現されるユーザーの問い合わせの多くが非常に近いスコアになる可能性があります。 代わりに、プロジェクト内の多くの質問に存在するかもしれない「場所」のような語を避け、"*駐車場はどこですか*" や "*ATM はどこですか*" のような質問で違いを明確にしてください。

## <a name="collaborate"></a>共同作業

質問応答では、ユーザーはプロジェクトおよびナレッジ ベースで共同作業を行うことができます。 プロジェクトおよびナレッジ ベースにアクセスするためには、関連付けられた Azure リソース グループへのアクセス権がユーザーに必要です。 ナレッジ ベースの編集と保守管理を外注する組織もありますが、その場合でも Azure リソースへのアクセスを保護できます。 この編集者/承認者モデルは、異なるサブスクリプションで同じ質問応答プロジェクトを使用した 2 つの同一の言語リソースを設定し、その 1 つを編集/テスト サイクルに選択することで実現されます。 テストが終了したら、[インポート/エクスポート](../how-to/migrate-knowledge-base.md) プロセスを利用してプロジェクト コンテンツをエクスポートし、承認者の言語リソースに転送します。この承認者が最終的にプロジェクトをデプロイし、エンドポイントを更新します。

## <a name="active-learning"></a>アクティブ ラーニング

[アクティブ ラーニング](../tutorials/active-learning.md)は、幅広い質と量のユーザー ベースのクエリがある場合に、代替の質問を提案するという最高の仕事をします。 クライアント アプリケーションのユーザー クエリが、検閲なしのアクティブ ラーニングのフィードバック ループに参加できるようにすることが重要です。 Language Studio ポータルで質問が提案されたら、それらの提案をレビューして、承認または拒否できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-to/manage-knowledge-base.md)
