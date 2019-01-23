---
title: Azure Content Moderator でのテキストのレビュー - Content Moderator
description: Content Moderator でテキストをレビューして、そのスコアおよび検出されたタグを確認する方法について説明します。 情報を使用して、コンテンツが適切かどうかを判断します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 005e6ed853cbc6013d74e7ff479097dcbb0a1043
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259565"
---
# <a name="let-human-reviewers-review-text"></a>人間のレビュー担当者にテキストをレビューさせます

Azure Content Moderator を使用し、スコアおよび検出されたタグを使用してテキストをレビューできます。 情報を使用して、コンテンツが適切かどうかを判断します。 

## <a name="select-or-enter-the-text-to-review"></a>レビューするテキストを入力または選択する

Content Moderator で、**[Try]\(試す\)** タブを選択します。次に、**[テキスト]** オプションを選択し、テキスト モデレーション開始画面に移動します。 任意のテキストを入力するか、既定のサンプル テキストを送信して自動テキスト モデレーションを行います。 最大で 1,024 文字を入力できます。

## <a name="get-ready-to-review-results"></a>結果のレビューを準備する

レビュー ツールは、まず Text Moderation API を呼び出します。 次に、検出されたタグを使用して、テキストのレビューを生成します。 レビュー ツールは、スコアの結果とチームの注目を照合します。

## <a name="review-text-results"></a>テキスト結果をレビューする

ウィンドウに詳細な結果が表示されます。 結果には、検出されたタグと、Text Moderation API によって返された用語が含まれます。 タグの選択状態を切り替えるには、タグを選択します。 また、作成したカスタム タグを操作することもできます。

![Chrome ブラウザー ウィンドウにフラグが付けられたテキストが表示されたレビュー ツールのスクリーンショット](../images/reviewresults_text.png)
