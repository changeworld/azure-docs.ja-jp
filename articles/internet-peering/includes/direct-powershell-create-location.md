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
ms.openlocfilehash: 65f282a63e95530549bd0db35c2f42fe5c25993d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720050"
---
PowerShell コマンドレット **Get-AzPeeringLocation** の必須パラメーター `Kind` によって、ピアリングの場所の一覧が返されます。これは後述の手順で使用します。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接ピアリングの場所には、次のフィールドが含まれます。
* PeeringLocation 
* Country
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

[PeeringDB](https://wwww.peeringdb.com) を参照して、そこが目的のピアリング ファシリティであることを検証します。

この例では、ピアリングの場所として Seattle を使用し、Direct ピアリングを作成する方法を示します。

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```
