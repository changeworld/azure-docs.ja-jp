---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ecc8396204138df05aa779e8bfa09bbfc6cd184d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720285"
---
このセクションでは、Direct ピアリングで次の変更操作を実行する方法について説明します。

* Direct ピアリング接続を追加する
* Direct ピアリング接続を削除する
* アクティブな接続で帯域幅をアップグレードまたはダウングレードする。
* アクティブな接続で IPv4 または IPv6 セッションを追加する
* アクティブな接続で IPv4 または IPv6 セッションを削除する

### <a name="add-direct-peering-connections"></a>Direct ピアリング接続を追加する

この例では、既存の Direct ピアリングに接続を追加する方法について説明します。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Direct ピアリング接続を削除する

接続の削除は、現在 PowerShell ではサポートされていません。 詳細については、[Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>アクティブな接続で帯域幅をアップグレードまたはダウングレードする

この例では、既存の直接接続に 10Gbps を追加する方法について説明します。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>アクティブな接続で IPv4 または IPv6 セッションを追加する

この例では、IPv4 セッションのみがある既存の直接接続で IPv6 セッションを追加する方法について説明します。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>アクティブな接続で IPv4 または IPv6 セッションを削除する

PowerShell では、既存の接続から IPv4 または IPv6 セッションを削除することは現在サポートされていません。 詳細については、[Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。
