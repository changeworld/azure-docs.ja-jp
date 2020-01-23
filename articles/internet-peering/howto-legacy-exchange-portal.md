---
title: ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773942"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>ポータルを使用して従来の Exchange ピアリングを Azure リソースに変換する

この記事では、ポータルを使用して既存の従来の Exchange ピアリングを Azure リソースに変換する方法について説明します。

必要に応じて、[PowerShell](howto-legacy-exchange-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)に関するページと「[Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)」を確認してください。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>従来の Exchange ピアリングを Azure リソースに変換する

### <a name="sign-in-to-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>従来の Exchange ピアリングを変換する

**ピアリング** リソースを使用して、従来のピアリング接続を変換できます。

#### <a name="launch-resource-and-configure-basic-settings"></a>リソースを起動して基本設定を構成する
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Exchange ピアリングを確認する
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
