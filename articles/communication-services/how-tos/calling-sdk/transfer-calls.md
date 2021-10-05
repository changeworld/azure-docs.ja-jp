---
title: 通話を転送する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して通話を転送します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e68c81a88c972cc05ffafea0d94e82c84de4268f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699642"
---
# <a name="transfer-calls"></a>通話を転送する

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

アクティブな通話の途中で、別の人や番号にその通話を転送したい場合があります。 その方法について見ていきましょう。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Transfer calls Client-side JavaScript](./includes/transfer-calls/transfer-calls-web.md)]

## <a name="next-steps"></a>次のステップ
- [通話の管理方法を参照する](./manage-calls.md)
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話を記録する方法を参照する](./record-calls.md)
- [通話を文字起こしする方法を参照する](./call-transcription.md)