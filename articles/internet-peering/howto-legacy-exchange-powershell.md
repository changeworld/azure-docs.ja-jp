---
title: PowerShell を使用して従来の Exchange ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: PowerShell を使用して従来の Exchange ピアリングを Azure リソースに変換する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774002"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>PowerShell を使用して従来の Exchange ピアリングを Azure リソースに変換する

この記事では、PowerShell コマンドレットを使用して既存の従来の Exchange ピアリングを Azure リソースに変換する方法について説明します。

好みに応じて、[ポータル](howto-legacy-exchange-portal.md)を使用してこのガイドを実行することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)に関するページと「[Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)」を確認してください。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>従来の Exchange ピアリングを Azure リソースに変換する

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>変換のための従来の Exchange ピアリングを取得する
シアトルのピアリングの場所で従来の Exchange ピアリングを取得する例を次に示します。

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

応答は次の例のようになります。
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>従来のピアリングを変換する
従来の Exchange ピアリングを Azure リソースに変換するには、次のコマンドを使用できます。

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> 従来のピアリングを Azure リソースに変換する場合は、変更がサポートされないことに注意してください。&nbsp;

エンド ツー エンドのプロビジョニングが正常に完了した場合の応答の例を次に示します。

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```
詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」にアクセスしてください

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Exchange ピアリングを作成または変更する](howto-exchange-powershell.md)
