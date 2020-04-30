---
title: Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678514"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する

この記事では、Azure portal を使用して既存のレガシの Exchange ピアリングを Azure リソースに変換する方法について説明します。

必要に応じて、[PowerShell](howto-legacy-exchange-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>レガシの Exchange ピアリングを Azure リソースに変換する

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>従来の Exchange ピアリングを変換する

**Peering** リソースを使用して、レガシのピアリング接続を変換できます。

#### <a name="launch-the-resource-and-configure-basic-settings"></a>リソースを起動して基本設定を構成する
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange ピアリングを確認する
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
