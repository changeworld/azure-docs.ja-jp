---
title: 通話のプッシュ通知を有効にします。
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して通話のプッシュ通知を有効にします。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635719"
---
# <a name="enable-push-notifications-for-calls"></a>通話のプッシュ通知を有効にする

こちらでは、Azure Communication Services の通話に対してプッシュ通知を有効にする方法を説明します。 この設定をすると、ユーザーは着信があったことを知り、応答できるようになります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [イベントをサブスクライブする方法を参照する](./events.md)
- [通話の管理方法を参照する](./manage-calls.md)
- [ビデオの管理方法を参照する](./manage-video.md)
