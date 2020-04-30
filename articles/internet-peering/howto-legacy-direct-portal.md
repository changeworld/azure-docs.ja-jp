---
title: Azure portal を使用してレガシの Direct ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: Azure portal を使用してレガシの Direct ピアリングを Azure リソースに変換する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678858"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Azure portal を使用してレガシの Direct ピアリングを Azure リソースに変換する

この記事では、Azure portal を使用して既存のレガシの Direct ピアリングを Azure リソースに変換する方法について説明します。

必要に応じて、[PowerShell](howto-legacy-direct-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Direct ピアリングのチュートリアル](walkthrough-direct-all.md)を確認します。


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>レガシの Direct ピアリングを Azure リソースに変換する

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>レガシの Direct ピアリングを変換する

**Peering** リソースを使用して、レガシのピアリング接続を変換できます。

#### <a name="launch-the-resource-and-configure-basic-settings"></a>リソースを起動して基本設定を構成する
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct ピアリングを確認する
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)
