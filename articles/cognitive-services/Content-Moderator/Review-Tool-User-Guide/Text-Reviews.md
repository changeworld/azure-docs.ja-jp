---
title: Azure Content Moderator でのテキストのレビュー | Microsoft Docs
description: Content Moderator でテキストをレビューして、そのスコアおよび検出されたタグを確認する方法について説明します。 情報を使用して、コンテンツが適切かどうかを判断します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372917"
---
# <a name="review-text"></a>テキストのレビュー

Azure Content Moderator を使用し、スコアおよび検出されたタグを使用してテキストをレビューできます。 情報を使用して、コンテンツが適切かどうかを判断します。 

## <a name="select-or-enter-the-text-to-review"></a>レビューするテキストを入力または選択する

Content Moderator で、**[Try]\(試す\)** タブを選択します。次に、**[テキスト]** オプションを選択し、テキスト モデレーション開始画面に移動します。 任意のテキストを入力するか、既定のサンプル テキストを送信して自動テキスト モデレーションを行います。 最大で 1,024 文字を入力できます。

## <a name="get-ready-to-review-results"></a>結果のレビューを準備する

レビュー ツールは、まず Text Moderation API を呼び出します。 次に、検出されたタグを使用して、テキストのレビューを生成します。 レビュー ツールは、スコアの結果とチームの注目を照合します。

## <a name="review-text-results"></a>テキスト結果をレビューする

ウィンドウに詳細な結果が表示されます。 結果には、検出されたタグと、Text Moderation API によって返された用語が含まれます。 タグの選択状態を切り替えるには、タグを選択します。 また、作成したカスタム タグを操作することもできます。

![テキスト結果をレビューする](images/3-review-text-2.png)
