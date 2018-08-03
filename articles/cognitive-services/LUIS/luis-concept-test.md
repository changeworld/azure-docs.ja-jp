---
title: LUIS アプリのテスト - Azure | Microsoft Docs
description: Language Understanding (LUIS) を使用して、アプリケーションの改善とその言語解釈の向上に継続的に取り組みます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: d231eaf98358e3f8237a820e59433558d293872f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224349"
---
# <a name="testing-in-luis"></a>LUIS でのテスト

テストは、LUIS にサンプル発話を提供し、LUIS が認識した意図およびエンティティの応答を取得するプロセスです。 

LUIS で、対話的に一度に 1 つずつ発話を[テスト](luis-interactive-test.md)することも、発話の[バッチ](luis-concept-batch-test.md)を提供することもできます。 テストを使用して、現在の[アクティブ](luis-concept-version.md#active-version) モデルを、公開済みのモデルと比較します。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>テストでのスコアとは
予測スコアの詳細については、「[予測スコア](luis-concept-prediction-score.md)」の概念を参照してください。

## <a name="interactive-testing"></a>対話型テスト
対話型テストは、Web サイトの **[Test]\(テスト\)** パネルから実行します。 発話を入力すると、意図とエンティティがどのように識別され、スコア付けされるかを確認することができます。 テスト ウィンドウで発話の意図とエンティティが期待どおりに予測されない場合、LUIS によってその発話が新しい発話として **[Intent]\(意図\)** ページにコピーされます。 次に、その発話の部分にラベルを付け、LUIS をトレーニングします。 

## <a name="batch-testing"></a>バッチ テスト
一度に複数の発話をテストする場合は、[バッチ テスト](luis-concept-batch-test.md)に関するページを参照してください。

## <a name="endpoint-testing"></a>エンドポイント テスト
最大 2 つのバージョンのアプリで、[エンドポイント](luis-glossary.md#endpoint)を使用してテストを実行できます。 アプリのメインまたはライブ バージョンを**運用**エンドポイントとして使用し、2 つ目のバージョンを**ステージング** エンドポイントに追加します。 この方法では、[LUIS](luis-reference-regions.md) Web サイトの [テスト] ウィンドウにある現在のモデルと、2 つの異なるエンドポイントにある 2 つのバージョンの合計 3 つのバージョンの発話が提供されます。 

すべてのエンドポイントのテストが、ユーザーの使用量クォータに加算されます。 

## <a name="do-not-log-tests"></a>テストをログに記録しない
エンドポイントに対してテストを実行し、発話をログに記録したくない場合は、必ず `logging=false` クエリ文字列構成を使用してください。

## <a name="where-to-find-utterances"></a>発話の検索場所
LUIS では、ログに記録されたすべての発話がクエリ ログに格納され、[LUIS](luis-reference-regions.md) Web サイトの **[アプリ]** 一覧ページでダウンロードしたり、LUIS [オーサリング API](https://aka.ms/luis-authoring-apis) で使用したりできます。 

LUIS が確信を持てなかった発話はすべて、[LUIS](luis-reference-regions.md) Web サイトの **[[エンドポイントの発話の確認]](luis-how-to-review-endoint-utt.md)** ページに一覧表示されます。 

![エンドポイントの発言の確認](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>必ずトレーニングする
モデルに変更を加えた後は、必ず LUIS を[トレーニング](luis-how-to-train.md)します。 LUIS アプリへの変更は、アプリをトレーニングするまで、テストで表示されません。 

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認します。

## <a name="next-steps"></a>次の手順

* 発話の[テスト](luis-interactive-test.md)の詳細について確認します。