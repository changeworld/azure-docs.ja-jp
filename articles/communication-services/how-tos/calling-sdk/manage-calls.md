---
title: 通話を管理する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して通話を管理します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4abf92e2d3003d8944ea75c411bc9699716e4684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699647"
---
# <a name="manage-calls"></a>通話を管理する

Azure Communication Services SDK を使用して通話を管理する方法について説明します。 通話を行い、参加者とプロパティを管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../quickstarts/access-tokens.md)についての詳細
- 省略可能:[アプリケーションへの通話の追加の概要](../../quickstarts/voice-video-calling/getting-started-with-calling.md)に関するクイックスタートを完了します

::: zone pivot="platform-web"
[!INCLUDE [Manage Calls JavaScript](./includes/manage-calls/manage-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Calls Android](./includes/manage-calls/manage-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Calls iOS](./includes/manage-calls/manage-calls-ios.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Manage Calls Windows](./includes/manage-calls/manage-calls-windows.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話を記録する方法を参照する](./record-calls.md)
- [通話を文字起こしする方法を参照する](./call-transcription.md)