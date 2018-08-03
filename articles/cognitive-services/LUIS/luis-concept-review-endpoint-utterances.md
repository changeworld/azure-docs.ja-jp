---
title: Language Understanding (LUIS) でアクティブ ラーニングを使用するためのエンドポイント発話のレビュー | Microsoft Docs
description: "\"エンドポイント発話のレビュー\" という名前のアクティブ ラーニング機能を使用して、パフォーマンス予測を向上させる速度を上げます。"
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: 05b3404d318359c6966df44bfab9baff3ded980f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222615"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>エンドポイント発話のレビューによるアクティブ ラーニングの有効化
アクティブ ラーニングは、3 つの予測精度の改善戦略の 1 つで、最も簡単に実装できます。 

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは
アクティブ ラーニングは 2 段階のプロセスです。 まず、LUIS がアプリのエンドポイントで受信した発話が、LUIS によって選択されます。 次のステップで、アプリの所有者またはコラボレーターが、選択された発話を、正しい意図とその意図内の任意のエンティティを含め、[レビュー](luis-how-to-review-endoint-utt.md)のために検証します。 発話をレビューしたら、アプリをもう一度トレーニングして公開します。 

## <a name="which-utterances-are-on-the-review-list"></a>どの発話がレビュー リストに追加されるか
最上位にある実行中の意図のスコアが低いとき、または上位 2 つの意図のスコアが非常に近いときに、その発話は LUIS によってレビュー リストに追加されます。 

## <a name="single-pool-for-utterances-per-app"></a>アプリごとの発話のための 1 つのプール
**[エンドポイントの発話の確認]** 一覧は、バージョンによって変化しません。 どのバージョンの発話をアクティブに編集しているか、またはどのバージョンのアプリがエンドポイントで発行されたかには関係なく、確認すべき発話のプールが 1 つあります。 

## <a name="where-are-the-utterances-from"></a>発話の取得元
エンドポイント発話は、アプリケーションの HTTP エンドポイントのエンド ユーザーのクエリから取得されます。 お使いのアプリが公開されていない場合や、まだヒットがない場合、レビュー対象の発話はありません。 特定の意図またはエンティティについて、エンドポイントでヒットがない場合は、それを含む、レビュー対象の発話がありません。 

## <a name="schedule-review-periodically"></a>レビューを定期的にスケジュール設定
提案された発話は必ずしも毎日レビューする必要はありませんが、LUIS の定期的なメンテナンスに組み込むことをお勧めします。 

## <a name="delete-review-items-programmatically"></a>プログラムによるレビュー項目の削除
アプリのサイズが大きい場合は、レビューする発話をいくつか選択したうえで、残りをリストからプログラムによって削除できます。 これを行うには、最初にリストを[取得](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a)してから、ID によって発話を[削除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)します。

## <a name="next-steps"></a>次の手順

* エンドポイント発話を[レビュー](luis-how-to-review-endoint-utt.md)する方法を確認します
