---
title: パターンがどのように予測精度を改善するかを学習する | Microsoft Docs
titleSuffix: Azure
description: 意図の予測スコアを改善し、エンティティを探すようにパターンを設計する方法について学習します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: c08419e3fb5b25284121a0eac30c38c8ba7570f1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225219"
---
# <a name="patterns-improve-prediction-accuracy"></a>パターンは予測精度を改善する
パターンは、複数の発話が非常に似ているときに、精度を改善するように設計されています。 発話のパターンを指定することで、LUIS は信頼度の高い予測を実現します。 

## <a name="patterns-solve-low-intent-confidence"></a>パターンは信頼度の低い意図を解決する
従業員に関連する組織図を報告する人事管理アプリがあるとします。 ある従業員の名前と関係を指定すると、LUIS はその従業員と関わりのある従業員を返します。 Tom という従業員の上司は Alice で、部下は Michael、Rebbeca、および Carl です。

![組織図のイメージ](./media/luis-concept-patterns/org-chart.png)

|発話|予測される意図|意図のスコア|
|--|--|--|
|Who is Tom's subordinate? (Tom の部下は誰。)|GetOrgChart|.30|
|Who is the subordinate of Tom? (Tom の部下は誰ですか。)|GetOrgChart|.30|

アプリに文の長さが異なる、語順が異なる、および違う言葉 ("subordinate (部下)"、"manage (管理)"、"report (所属)" の同義語) が使用されている発話が 10 から 20 ある場合、LUIS が返す信頼度スコアは低くなることがあります。 LUIS が語順の重要度を理解できるように、パターンを作成します。 

パターンは次のような状況を解決します。 

* 意図スコアが低いとき。
* 正しい意図が最上位のスコアではないが、最上位のスコアに近すぎるとき。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>パターンは意図を保証するものではない
パターンは、さまざまな予測テクノロジを使用します。 パターンにテンプレートの発話の意図を設定することは、意図の予測を保証するものではありませんが、強力なシグナルになります。 

## <a name="patterns-do-not-improve-entity-detection"></a>パターンはエンティティの検出を改善しない
パターンにはエンティティが必要ですが、パターンはエンティティの検出を支援しません。 パターンは意図とロールの予測を支援するためだけに用意されています。  

## <a name="patterns-use-entity-roles"></a>パターンはエンティティのロールを使用する
パターン内の 2 つ以上のエンティティがコンテキスト上関連性がある場合、パターンはエンティティの[ロール](luis-concept-roles.md)を使用して、エンティティに関するコンテキスト情報を抽出します。 これは階層におけるエンティティの子要素と同等ですが、パターンで**のみ**有効です。 

## <a name="prediction-scores-with-and-without-patterns"></a>パターンありとなしの場合の予測スコア
発話例が十分に指定されている場合、LUIS はパターンなしで予測の信頼度を改善できます。 パターンがあれば、それほど多くの発話を指定しなくても信頼度スコアを改善できます。  

## <a name="pattern-matching"></a>パターン マッチング
パターンは、まずパターン内のエンティティの検出に基づいて、次に残りのワードとパターンの語順を検証することでマッチングを行います。 パターンが一致するには、パターン内にエンティティが必要です。 

## <a name="pattern-syntax"></a>パターンの構文
パターンの構文は、発話のテンプレートです。 テンプレートには、一致させるワードとエンティティのほか、無視するワードや句読点が含まれている必要があります。 正規表現では**ありません**。 

パターン内のエンティティは中かっこ (`{}`) で囲まれます。 パターンにはエンティティ、およびロール付きのエンティティを含めることができます。 Pattern.any はパターンにのみ使用されるエンティティです。 構文については次のセクションで説明します。

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>パターン テンプレートにエンティティを追加する構文
パターン テンプレートにエンティティを追加するには、エンティティ名を `Who does {Employee} manage?` のように中かっこで囲みます。 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>パターン テンプレートにエンティティとロールを追加する構文
エンティティのロールは、`{entity:role}` のようにエンティティ名、コロン、ロール名の順に記述します。 パターン テンプレートにロール付きのエンティティを追加するには、エンティティ名とロール名を `Book a ticket from {Location:Origin} to {Location:Destination}` のように中かっこで囲みます。 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>パターン テンプレートに pattern.any を追加する構文
Pattern.any エンティティでは、パターンに可変長のエンティティを追加することができます。 パターン テンプレートが後に続く限り、pattern.any は任意の長さで指定できます。 

**Pattern.any** エンティティをパターン テンプレートに追加するには、Pattern.any エンティティを `How much does {Booktitle} cost and what format is it available in?` のように中かっこで囲みます。  

```
How much does {Booktitle} cost and what format is it available in?
```

|パターン内の書籍のタイトル|
|--|
|How much does **steal this book** cost and what format is it available in? (steal this book (この本を盗む) の価格はいくらで、どの形式で購入できますか。)|
|How much does **ask** cost and what format is it available in? (ask (質問) の価格はいくらで、どの形式で購入できますか。)|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in? (The Curious Incident of the Dog in the Night-Time (夜間の犬に関する面白い事件) の価格はいくらで、どの形式で購入できますか。)| 

これらの書籍のタイトル例では、書籍のタイトルのコンテキスト ワードによって LUIS が混乱することはありません。 書籍のタイトルはパターン内にあり、Pattern.any エンティティによってマークされているため、LUIS はそのタイトルがどこで終了するかを把握できます。

### <a name="explicit-lists"></a>明示的なリスト
パターンに Pattern.any が含まれており、そのパターン構文が発話に基づいて正しくないエンティティを抽出する可能性を考慮する場合は、オーサリング API を通じて[明示的なリスト](https://aka.ms/ExplicitList)を作成し、例外を許可します。 

たとえば、オプションの構文 `[]` とエンティティ構文 `{}` の両方が含まれるパターンがあり、データを正しくない方法で抽出するように結合されているとします。

'[find] email about {subject} [from {person}]' というパターンを考えてみます。 次の発話では **subject** エンティティと **person** が一方では正しく抽出されており、もう一方では正しく抽出されていません。

|発話|エンティティ|正しい抽出|
|--|--|:--:|
|email about dogs from Chris (クリスから犬に関する電子メール)|subject=dogs (犬)<br>person=Chris (クリス)|✔|
|email about the man from La Mancha (ラ・マンチャからその男に関する電子メール)|subject=the man (その男)<br>person=La Mancha (ラ・マンチャ)|○|

前の表の発話 `email about the man from La Mancha` では、subject は `the man from La Mancha` (書籍のタイトル) である必要がありますが、subject にオプションのワード `from` が含まれているため、タイトルが間違って予測されています。 

このパターンの例外を修正するには、[明示的なリストのオーサリング API](https://aka.ms/ExplicitList) を使用して、`the man from la mancha` を {subject} エンティティに対する明示的なリストの一致として追加します。

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>テンプレートの発話でオプションのテキストをマークする構文
正規表現の角かっこの構文 `[]` を使用して、発話内のオプションのテキストをマークします。 オプションのテキストは、最大で 2 つの角かっこのみを入れ子にできます。

```
[find] email about {subject} [from {person}]
```

`.`、`!`、`?` などの句読点は角かっこを使用して無視できます。 これらのマークを無視するには、各マークが別個のパターン内にある必要があります。 現在、オプションの構文は、複数の項目のリスト内のある項目を無視することはできません。

## <a name="patterns-only"></a>パターンのみ
LUIS は意図に発話例が 1 つもないアプリを許可します。 この使用法は、パターンが使用されている場合にのみ許可されます。 パターンでは、各パターンに少なくとも 1 つのエンティティが必要です。 パターンのみのアプリの場合、そのパターンには機械学習エンティティが含まれていてはなりません。これらのエンティティは発話例を必要とするためです。 

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [このチュートリアルでパターンを実装する方法を学習する](luis-tutorial-pattern.md)