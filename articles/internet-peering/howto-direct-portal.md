---
title: ポータルを使用して Direct ピアリングを作成または変更する
titleSuffix: Azure
description: ポータルを使用して Direct ピアリングを作成または変更する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773982"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>ポータルを使用して Direct ピアリングを作成または変更する

この記事では、ポータルを使用して Microsoft Direct ピアリングを作成する方法について説明します。 また、この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

必要に応じて、[PowerShell](howto-direct-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Direct ピアリングのチュートリアル](walkthrough-direct-all.md)に関する記事を確認してください。
* Microsoft との Direct ピアリングが既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)」を参照してください

## <a name="create-and-provision-a-direct-peering"></a>Direct ピアリングを作成およびプロビジョニングする

### <a name="sign-in-to-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Direct ピアリングを作成する

**[ピアリング]** リソースを使用して、新しいピアリング要求を作成できます。

#### <a name="launch-resource-and-configure-basic-settings"></a>リソースを起動して基本設定を構成する
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Direct ピアリングを確認する
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Direct ピアリングを変更する
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Direct ピアリングをプロビジョニング解除する
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)。
* [ポータルを使用して従来の Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)。

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください
