---
title: 通話中にビデオを管理する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用してビデオ通話を管理します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 536dc2233ca0ae0773c9e953d35bee6ee4a4da40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699646"
---
# <a name="manage-video-during-calls"></a>通話中にビデオを管理する

Azure Communication Services SDK を使用してビデオ通話を管理する方法について説明します。 通話内のビデオの受信と送信を管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Video Calls JavaScript](./includes/manage-video/manage-video-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Video Calls Android](./includes/manage-video/manage-video-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Video Calls iOS](./includes/manage-video/manage-video-ios.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [通話を管理する方法を確認する](./manage-calls.md)
- [通話を録画する方法を確認する](./record-calls.md)
- [通話を文字起こしする方法を確認する](./call-transcription.md)
