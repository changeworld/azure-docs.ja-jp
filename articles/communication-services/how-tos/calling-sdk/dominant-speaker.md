---
title: アクティブな話者を取得する
titleSuffix: An Azure Communication Services how-to guide
description: 通話のアクティブな話者をレンダリングするには、Azure Communication Services SDK を使用します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699247"
---
# <a name="get-active-speakers-within-a-call"></a>通話内のアクティブな話者を取得する

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

アクティブな通話中に、レンダリングしたり異なる方法で表示したりするために、アクティブな話者の一覧の取得が必要になる場合があります。 ここではその方法を説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>次のステップ
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話の管理方法を参照する](./manage-calls.md)
- [通話を記録する方法を参照する](./record-calls.md)