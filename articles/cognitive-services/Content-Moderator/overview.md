---
title: Azure Content Moderator の概要 | Microsoft Docs
description: Content Moderator を使用して、ユーザー生成コンテンツ内の不適切な内容を追跡、フラグ付け、評価、フィルター処理する方法について説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377752"
---
# <a name="what-is-content-moderator"></a>Content Moderator とは

コンテンツ モデレーションは、不快感を与え、危険で望ましくないコンテンツの可能性があるもの監視するプロセスです。 イメージ、テキスト、ビデオがモデレート対象のコンテンツとなります。

## <a name="where-it-is-used"></a>使用場所

Content Moderator を使用するシナリオの例を次にいくつか一覧します。

- 製品カタログとユーザー生成コンテンツをモデレートするオンライン マーケットプレース
- ユーザー生成ゲームの成果物とチャット ルームをモデレートするゲーム会社
- ユーザーによって追加されたイメージ、テキスト、ビデオをモデレートするソーシャル メッセージング プラットフォーム
- コンテンツに対する一元的なコンテンツ モデレーションを実装するエンタープライズ メディア企業
- 学生および教育者向けの不快感を与える不適切なコンテンツをフィルター処理する K-12 教育ソリューション プロバイダー

## <a name="what-it-includes"></a>備えている機能

Content Moderator は、いくつかの Web サービス API と、イメージ、テキスト、ビデオのモデレートを支援する、組み込みの Human-in-the-loop (人間参加型) レビュー ツールとから構成されます。

![Content Moderator のブロック図](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

Content Moderator には次の API が含まれています。
  - [**テキスト モデレーション API**](text-moderation-api.md): この API は、テキストをスキャンして不適切な表現、暗示的な表現、不快感を与える表現、個人を特定できる情報 (PII) の可能性があるものを探す場合に使用します。
  - [**カスタム用語リスト API**](try-terms-list-api.md): この API は、組み込みの用語だけでなくカスタムの用語リストとの照合も行う場合に使用します。 これらのリストは、コンテンツ ポリシーに従ってコンテンツをブロックまたは許可するために使用します。  
  - [**イメージ モデレーション API**](image-moderation-api.md): この API は、イメージをスキャンして成人向けのわいせつなコンテンツを探すため、光学式文字認識 (OCR) 機能によりイメージ内のテキストを検出するため、および顔を検出するために使用します。
  - [**カスタム イメージ リスト API**](try-image-list-api.md): この API は、イメージのカスタム リスト (事前に識別されたコンテンツのうち再び分類する必要のないもの) と照合する場合に使用します。
  - [**ビデオ モデレーション API**](video-moderation-api.md): この API は、ビデオをスキャンして成人向けのわいせつなコンテンツの可能性があるものを探す場合に使用します。
  - [**レビュー API**](try-review-api-job.md): [ジョブ](try-review-api-job.md)、[レビュー](try-review-api-review.md)、[ワークフロー](try-review-api-workflow.md) 操作を使用して、レビュー ツール内で Human-in-the-loop (人間参加型) ワークフローを作成して自動化します。

## <a name="human-review-tool"></a>目視レビュー ツール

Content Moderator サブスクリプションには、組み込みの[目視レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)が含まれています。 ヒューマン モデレーターが最終的な決定を行うようにテキスト、イメージ、ビデオのレビューを作成するには、前に説明したレビュー API を使用します。

![Content Moderator ビデオ レビュー ツール](images/video-review-default-view.png)

## <a name="next-steps"></a>次の手順

[クイック スタート](quick-start.md)を使用して Content Moderator を使い始める。
