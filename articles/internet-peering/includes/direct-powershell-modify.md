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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773654"
---
このセクションでは、Direct ピアリングに対して次の変更操作を実行する方法について説明します。

* Direct ピアリング接続を追加する
* Direct ピアリング接続を削除する
* アクティブな接続で帯域幅をアップグレードまたはダウングレードする。
* アクティブな接続で IPv4/IPv6 セッションを追加する。
* アクティブな接続で IPv4/IPv6 セッションを削除する。

### <a name="add-direct-peering-connections"></a>Direct ピアリング接続を追加する

次の例では、既存の Direct ピアリングに接続を追加する方法について説明します

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

接続の削除は、現在 PowerShell ではサポートされていません。 [Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>アクティブな接続で帯域幅をアップグレードまたはダウングレードする

次の例では、既存の直接接続に 10Gbps を追加する方法について説明します。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>アクティブな接続で IPv4/IPv6 セッションを追加する

次の例では、IPv4 セッションのみがある既存の直接接続で IPv6 セッションを追加する方法について説明します。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>アクティブな接続で IPv4/IPv6 セッションを削除する

PowerShell では、既存の接続から IPv4/IPv6 セッションを削除することは現在サポートされていません。 [Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。