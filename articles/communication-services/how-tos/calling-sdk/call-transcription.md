---
title: クライアントで通話の文字起こしの状態を表示する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して、通話の文字起こしの状態を表示します
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635743"
---
# <a name="display-call-transcription-state-on-the-client"></a>クライアントで通話の文字起こしの状態を表示する

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

通話の文字起こしを使用するときに、通話が文字起こしされていることをユーザーに知らせることができます。 ここではその方法を説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話の管理方法を参照する](./manage-calls.md)
- [通話の記録方法を参照する](./record-calls.md)