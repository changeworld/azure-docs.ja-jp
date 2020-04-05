---
title: Personalizer とは
titleSuffix: Azure Cognitive Services
description: Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにするクラウドベースの API サービスです。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 850ab0ee89ee167886d8747a0c721bb643529e14
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052061"
---
# <a name="what-is-personalizer"></a>Personalizer とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer とは、クラウドベースの API サービスで、クライアント アプリケーションが各ユーザーに示す最適な "_コンテンツ_" 項目を 1 つ選択する際に役立ちます。 このサービスでは、コンテンツとコンテキストに関して指定したリアルタイム情報の集合に基づき、最適な項目がコンテンツ項目から選択されます。

そのコンテンツ項目をユーザーに提示した後、システムは、ユーザーの行動を監視して報酬スコアを Personalizer に再度レポートし、受け取ったコンテキスト情報に基づいて最適なコンテンツを選択する能力を高めます。

**コンテンツ**には、テキスト、画像、URL、メールなど、抽出してユーザーに表示するあらゆる情報単位が当てはまります。

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>最適なコンテンツ項目を Personalizer が選択するしくみ

Personalizer は**強化学習**を使用し、すべてのユーザーにわたって集めた行動と報酬スコアに基づいて最適な項目 ("_アクション_") を選択します。 アクションとは、ニュース記事、特定の映画、製品など、選択肢となるコンテンツ項目です。

**Rank** 呼び出しは、アクション項目、アクションの特徴、コンテキストの特徴を受け取って、最上位のアクション項目を選択します。

* **アクションと特徴** - コンテンツ項目と各項目に固有の特徴
* **コンテキストの特徴** - アプリ使用時のユーザーとそのコンテキスト、環境の特徴

Rank 呼び出しからは、ユーザーに表示するコンテンツ項目 (__アクション__) の ID が、**報酬アクション ID** フィールドで返されます。
ユーザーに表示される __アクション__ は、一定期間にわたる報酬の総量が最大となるように機械学習モデルを使用して選択されます。

いくつかのシナリオの例は次のとおりです。

|Content type|**アクション (と特徴)**|**コンテキストの特徴**|返される報酬アクション ID<br>(このコンテンツが表示されます)|
|--|--|--|--|
|ニュース リスト|a. `The president...` (国内, 政治, [テキスト])<br>b. `Premier League ...` (グローバル, スポーツ, [テキスト, 画像, ビデオ])<br> c. `Hurricane in the ...` (地域, 気象, [テキスト,画像]|ニュースの閲覧に使用するデバイス<br>月または季節<br>|a `The president...`|
|映画リスト|1.`Star Wars` (1977, [アクション, アドベンチャー, ファンタジー], ジョージ ルーカス)<br>2.`Hoop Dreams` (1994, [ドキュメンタリー, スポーツ], スティーブ ジェームズ<br>3.`Casablanca` (1942, [ロマンス, ドラマ, 戦争], マイケル カーティス)|映画の視聴に使用するデバイス<br>画面サイズ<br>ユーザーの種類<br>|3. `Casablanca`|
|Product 一覧|i. `Product A` (3 kg, $$$$, 24 時間以内に配送)<br>ii. `Product B` (20 kg, $$, 出荷と通関に 2 週間)<br>iii. `Product C` (3 kg, $$$, 48 時間以内に配送)|ショッピングの閲覧に使用するデバイス<br>ユーザーの支出階層<br>月または季節|ii. `Product B`|

Personalizer は、次の組み合わせに基づいて "_報酬アクション ID_" と呼ばれる最適なアクションを 1 つ選択するために強化学習を使用しました。
* トレーニング済みのモデル - Personalizer サービスが受け取った過去の情報
* 最新のデータ - 特徴を伴う具体的なアクション、コンテキストの特徴

## <a name="when-to-call-personalizer"></a>Personalizer を呼び出すタイミング

Personalizer の **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) は、コンテンツを提示する "_たびに_" リアルタイムで呼び出されます。 これは**イベント**と呼ばれ、"_イベント ID_" で表されます。

Personalizer の **Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) は、リアルタイムで呼び出すことも、実際のインフラストラクチャに合わせて時間差で呼び出すこともできます。 報酬スコアは、ビジネス ニーズに基づいて決めます。 これは、適切な場合は 1、不適切な場合は 0 というように 1 つの値になる場合があるほか、ビジネス目標や指標を考慮して作成したアルゴリズムによって生成される場合もあります。

## <a name="personalizer-content-requirements"></a>Personalizer のコンテンツ要件

Personalizer は、対象のコンテンツが次の条件を満たす場合に使用します。

* 選択肢となる一連の項目数が限られている (最大約 50 個)。 リストがそれよりも大きい場合は、[レコメンデーション エンジンを使用](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)して、項目を 50 個まで減らしてください。
* ランク付けしたいコンテンツを説明する情報がある ("_アクションと特徴_" および "_コンテキストの特徴_")。
* Personalizer で効果を発揮するために、コンテンツに関連したイベントが 1 日あたり少なくとも約 1,000 件発生する。 最低限必要なトラフィックが Personalizer に送信されなかった場合、このサービスでは、最適なコンテンツ項目を 1 つ割り出すために、さらに時間がかかります。

Personalizer は、集まった情報をほぼリアルタイムで使用して最適なコンテンツ項目を 1 つ返すため、このサービスでは次のことが行われません。
* ユーザー プロファイル情報を保持および管理する
* 個々のユーザーの好みや履歴を記録する
* クリーニング済みおよびラベル付きのコンテンツを要求する

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>クライアント アプリケーションに合わせて Personalizer を設計して実装する方法

1. コンテンツ、" **_アクション_** "、" **_コンテキスト_** " を[設計](concepts-features.md)および計画します。 " **_報酬_** " スコア用の報酬アルゴリズムを決定します。
1. 作成した各 [Personalizer リソース](how-to-settings.md)は、1 学習ループと見なされます。 このループでは、そのコンテンツまたはユーザー エクスペリエンスに対する Rank と Reward 両方の呼び出しを受け取ります。
1. 対象の Web サイトまたはコンテンツ システムに Personalizer を追加します。
    1. ユーザーにコンテンツを表示する前に最適な "_コンテンツ_" 項目を 1 つ決定する **Rank** 呼び出しをアプリケーション、Web サイト、またはシステムの Personalizer に追加します。
    1. 最適な "_コンテンツ_" 項目を 1 つユーザーに表示します。これは、返された "_報酬アクション ID_" です。
    1. ユーザーの行動に関して収集された情報に "_アルゴリズム_" を適用して、次のように**報酬**スコアを決定します。

        |動作|算出された報酬スコア|
        |--|--|
        |ユーザーが最適な "_コンテンツ_" 項目 (報酬アクション ID) を 1 つ選択しました|**1**|
        |ユーザーがその他のコンテンツを選択しました|**0**|
        |ユーザーが一時停止し、漠然とした態度で前後にスクロールした後、最適な "_コンテンツ_" 項目 (報酬アクション ID) を 1 つ選択しました|**0.5**|

    1. 0 から 1 までの報酬スコアを送信する **Reward** 呼び出しを追加します。
        * コンテンツを表示した直後
        * またはオフライン システムで少し経ってから
    1. 一定期間使用した後、オフライン評価を使用して[ループを評価](concepts-offline-evaluation.md)します。 オフライン評価を使用すると、コードを変更したりユーザー エクスペリエンスに影響を与えたりすることなく、Personalizer サービスの有効性をテストして評価することができます。

## <a name="next-steps"></a>次のステップ


* [Personalizer のしくみ](how-personalizer-works.md)
* [強化学習とは](concepts-reinforcement-learning.md)
* [Rank 要求の機能とアクションについて学習します](concepts-features.md)
* [Reward 要求のスコアの特定について学習します](concept-rewards.md)
* [クイック スタート](sdk-learning-loop.md)
* [チュートリアル](tutorial-use-azure-notebook-generate-loop-data.md)
* [対話型デモを使用する](https://personalizationdemo.azurewebsites.net/)
