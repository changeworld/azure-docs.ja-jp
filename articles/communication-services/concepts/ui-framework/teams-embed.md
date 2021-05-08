---
title: UI フレームワークである Teams Embed
titleSuffix: An Azure Communication Services quickstart
description: このドキュメントでは、Azure Communication Services の UI フレームワークである Teams Embed 機能を使用してターンキーの呼び出しエクスペリエンスを構築する方法について説明します。
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307641"
---
# <a name="teams-embed"></a>Teams Embed

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams Embed は、一般的な企業-消費者間と企業間の呼び出しのやり取りに焦点を絞った、Azure Communication Services の機能です。 Teams Embed システムの中核となるのは[ビデオと音声呼び出し](../voice-video-calling/calling-sdk-features.md)ですが、Teams Embed システムは、Azure の呼び出しプリミティブを基に構築され、Microsoft Teams の会議に基づく包括的なユーザー エクスペリエンスを提供します。

Teams Embed SDK はクローズドソースであり、これらの機能をターンキーの複合形式で使用できるようにします。 Teams Embed をアプリのキャンバスにドロップすると、SDK によって包括的なユーザー エクスペリエンスが生成されます。 このユーザー エクスペリエンスは Microsoft Teams の会議とよく似ているため、以下を活用できます。

- 開発時間とエンジニアリングの複雑さの軽減
- Teams に関するエンド ユーザーの知識
- [Teams のエンドユーザーのトレーニング コンテンツ](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)を再利用できる

以下を含め、Teams の会議で提供されるほとんどの機能は Teams Embed でサポートされています。

- ユーザーが音声とビデオのデバイスを構成する、会議前エクスペリエンス
- 音声とビデオのデバイスを構成するための、会議中エクスペリエンス
- [ビデオの背景](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): 参加者が背景をぼかしたり置き換えたりすることができる
- [ビデオ ギャラリーの複数のオプション](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae)。大型ギャラリー、集合モード、フォーカス、ピン留め、スポットライト
- [コンテンツ共有](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): 参加者が画面を共有できる

他の Azure Communication SDK と比較した、この UI の詳細については、[UI SDK の概念の紹介](ui-sdk-overview.md)に関する記事を参照してください。 
