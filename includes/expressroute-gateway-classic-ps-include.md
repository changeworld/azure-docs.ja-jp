---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219819"
---
以下の作業を行う前に、VNet とゲートウェイ サブネットを作成しておく必要があります。

> [!NOTE]
> これらの例は、S2S/ExpressRoute の共存構成には該当しません。
> 共存構成におけるゲートウェイについて詳しくは、[接続の共存の構成](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)に関するページをご覧ください。

## <a name="add-a-gateway"></a>ゲートウェイを追加する

ゲートウェイを作成するには、次のコマンドを使用します。 必ず、独自の値に置き換えてください。

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認

次のコマンドを使用して、ゲートウェイが作成されていることを確認します。 このコマンドは、他の操作に必要なゲートウェイ ID も取得します。

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する

[ゲートウェイ SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>ゲートウェイを削除する

ゲートウェイを削除するには、次のコマンドを使用します。

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
