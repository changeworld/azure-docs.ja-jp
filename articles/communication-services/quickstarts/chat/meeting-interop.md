---
title: Azure Communication Services における Teams の相互運用性の概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Chat SDK を使用して Teams の会議に参加する方法について説明します
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ba3a589c5d0f09f24950bd3fee8edc7f4dcd4601
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169064"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>クイックスタート: チャット アプリを Teams の会議に参加させる

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [チームのテナント相互運用性](../../concepts/teams-interop.md)を有効または無効にするには、[このフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)を完成させてください。

Azure Communication Services の使用を開始するには、JavaScript SDK を使用してチャット ソリューションを Microsoft Teams に接続します。 

## <a name="prerequisites"></a>前提条件 

1.  [Teams のデプロイ](/deployoffice/teams-install)。 
2. 実際に動作する[チャット アプリ](./get-started.md)。 

## <a name="enable-teams-interoperability"></a>Teams の相互運用性を有効にする 

ゲスト ユーザーとして Teams に参加している Communication Services ユーザーは、Teams の会議通話に参加したときにのみ会議のチャットにアクセスできます。 Communication Services ユーザーを Teams の会議通話に追加する方法については、[Teams の相互運用性](../voice-video-calling/get-started-teams-interop.md)に関するドキュメントを参照してください。

この機能を使用するには、両方のエンティティの所有組織のメンバーである必要があります。

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [チャットのヒーロー サンプル](../../samples/chat-hero-sample.md)を確認する
- [チャットのしくみ](../../concepts/chat/concepts.md)の詳細を確認する