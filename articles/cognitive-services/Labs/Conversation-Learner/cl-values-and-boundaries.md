---
title: Conversation Learner の既定の構成 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner の既定の構成について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376160"
---
# <a name="default-values-and-boundaries"></a>既定値と境界

このドキュメントでは、Conversation Learner の既定の構成と主なサービスの境界を説明します。

## <a name="default-configuration"></a>既定の構成

パラメーター | 既定値
--- | --- 
セッションのタイムアウトの既定値 | 30 分

## <a name="boundaries"></a>境界

パラメーター | 制限
--- | --- 
オーサリング API、1 か月あたりの HTTP 呼び出しの最大数 | 5 M
オーサリング API、1 秒あたりの HTTP 呼び出しの最大数 | 25
セッション API、1 か月あたりの HTTP 呼び出しの最大数 | 500K
セッション API、1 秒あたりの HTTP 呼び出しの最大数 | 10
アプリケーションごとのカスタム エンティティ (非プログラマティック エンティティ) の最大数 | [LUIS の境界に関するドキュメント](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)を参照してください。実際の数は、ドキュメントに記載の数よりも少なくなることがあります。
アプリケーションごとのビルド済みエンティティの最大数 | [LUIS の境界に関するドキュメント](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)を参照してください。
アプリケーションごとのエンティティの最大数 (合計数) | 100
アプリケーションごとのアクションの最大数 | 32
アプリケーションごとのトレーニング ダイアログの最大数 | 1,000
トレーニング ダイアログあたりのユーザーのターンの最大数 | 100
アプリケーションごとのログ ダイアログの最大数 | 制限は設定されていません。ログ ダイアログは一定期間のみ保持され、その期間が経過すると破棄されます。  また、Conversation Learner UI では一度に 100 件のログ ダイアログが表示されます。 
ユーザーごとのアプリケーションの最大数 | 制限は設定されていません。
連続した非待機アクションの最大数 | 5 (*)

(*) 非待機アクションが 5 回連続して発生すると、非待機アクションがすべてマスクされ、Conversation Learner により利用可能な待機アクションが選択されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Conversation Learner を使ってみる](./quickstart.md)
