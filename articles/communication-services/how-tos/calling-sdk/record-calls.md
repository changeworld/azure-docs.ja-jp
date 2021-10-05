---
title: クライアントで通話レコーディングを管理する
titleSuffix: An Azure Communication Services how-to guide
description: クライアントで通話レコーディングを管理するには、Azure Communication Services SDK を使用します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699643"
---
# <a name="manage-call-recording-on-the-client"></a>クライアントで通話レコーディングを管理する

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

[通話レコーディング](../../concepts/voice-video-calling/call-recording.md)は、Azure Communication Services で行った通話をユーザーが記録できるようにする機能です。 ここでは、クライアント側でレコーディングを管理する方法について説明します。 この機能を使用するためには、あらかじめ[サーバー側](../../quickstarts/voice-video-calling/call-recording-sample.md)のレコーディングをセットアップする必要があります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [通話の管理方法を参照する](./manage-calls.md)
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話を文字起こしする方法を参照する](./call-transcription.md)