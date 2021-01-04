---
title: Azure Communication Services における Teams の相互運用性の概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication チャット クライアント ライブラリを使用して、Teams の会議に参加する方法について説明します
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578052"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>クイックスタート: チャット アプリを Teams の会議に参加させる

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services の入門として、JavaScript クライアント ライブラリを使用してチャット ソリューションを Microsoft Teams に接続してみましょう。 

## <a name="prerequisites"></a>前提条件 

1.  [Teams のデプロイ](https://docs.microsoft.com/deployoffice/teams-install)。 
2. 実際に動作する[チャット アプリ](./get-started.md)。 

## <a name="enable-teams-interoperability"></a>Teams の相互運用性を有効にする 

ゲスト ユーザーとして Teams に参加している Communication Services ユーザーは、Teams の会議通話に参加したときにのみ会議のチャットにアクセスできます。 Communication Services ユーザーを Teams の会議通話に追加する方法については、[Teams の相互運用性](../voice-video-calling/get-started-teams-interop.md)に関するドキュメントを参照してください。

Teams の相互運用性機能は、現在プライベート プレビュー段階です。 ご利用の Communication Services リソースに関して、この機能を有効にする場合は、次の情報を添えて、メール (acsfeedback@microsoft.com) でご連絡ください。 
1. Communication Services リソースがある Azure サブスクリプションのサブスクリプション ID。 
2. Teams のテナント ID。 この情報を入手する方法としては、Teams へのリンクを取得して共有するのが最も簡単です。 

この機能を使用するには、両方のエンティティの所有組織のメンバーである必要があります。 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [チャットのヒーロー サンプル](../../samples/chat-hero-sample.md)を確認する
- [チャットのしくみ](../../concepts/chat/concepts.md)の詳細を確認する
