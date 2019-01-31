---
title: Azure Content Moderator でのテキストのレビュー - Content Moderator
description: Content Moderator でテキストをレビューして、そのスコアおよび検出されたタグを確認する方法について説明します。 情報を使用して、コンテンツが適切かどうかを判断します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219789"
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
