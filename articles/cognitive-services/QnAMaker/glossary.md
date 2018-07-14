---
title: 用語集 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 用語集
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376373"
---
# <a name="glossary"></a>用語集

## <a name="qna-maker-service"></a>QnA Maker サービス
QnA Maker サービスは、QnA Maker の使用を開始するための前提条件です。 QnA Maker レベルを購入すると、ナレッジ ベースを作成して管理するためのリソースが、お使いの Azure サブスクリプション内に設定されます。 各 QnA Maker ユーザー アカウントは、各自のサブスクリプション内に複数の QnA Maker サービスを作成できます。

## <a name="knowledge-base"></a>ナレッジ ベース
ナレッジ ベースは質問と回答のリポジトリであり、その作成、管理、および使用は QnA Maker を通して行われます。 各 QnA Maker レベルで、複数のナレッジ ベースを使用できます。

## <a name="endpoint"></a>エンドポイント
お使いのアプリケーション (通常はチャット ボット) に統合できるナレッジ ベースの内容にサービスを提供するREST ベースの HTTP エンドポイント。 

## <a name="test-knowledge-base"></a>テスト ナレッジ ベース
ナレッジ ベースには、テストと公開という 2 つの状態があります。 テスト ナレッジ ベースは、応答の精度と完全性の編集、保存、およびテストが実行されるバージョンです。 テスト ナレッジベースに加えられた変更は、アプリケーション/チャット ボットのエンド ユーザーには影響しません。

## <a name="published-knowledge-base"></a>公開ナレッジ ベース
ナレッジ ベースには、テストと公開という 2 つの状態があります。  公開ナレッジ ベースは、チャット ボット/アプリケーションで使用されるバージョンです。 ナレッジ ベースを公開するアクションは、テスト ナレッジ ベースの内容を、ナレッジ ベースの公開バージョン内に配置します。 公開ナレッジ ベースは、アプリケーションがエンドポイントを介して使用するバージョンであるため、その内容が正確であり、十分にテストされていることを保証する必要があります。

## <a name="query"></a>クエリ
ユーザー クエリは、エンド ユーザーまたはテスト担当者がナレッジ ベースに尋ねる質問です。 多くの場合、クエリは、自然言語形式か、質問を表すいくつかのキーワードで行われます。

## <a name="response"></a>応答
応答は、特定のユーザー クエリの最適な一致に基づいて、ナレッジ ベースから取得された回答です。

## <a name="confidence-score"></a>信頼度スコア
応答の信頼度スコアは、提供される応答が、特定のユーザー クエリに対する適切な応答であることを示す、0 から 100 の範囲の数値です (100 はユーザー クエリとナレッジ ベース内の質問が完全に一致していることを示します)。 回答は、通常は信頼度スコアによって順位付けされ、高い信頼度スコアを持つ回答が既定の応答として提供されます。
