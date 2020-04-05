---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75437015"
---
|  | **プライベート ピアリング** | **Microsoft ピアリング** |  **パブリック ピアリング** (新しい回線では非推奨) |
| --- | --- | --- | --- |
| **各ピアリングでサポートされるプレフィックスの最大数** |既定 4,000、ExpressRoute Premium 10,000 |200 |200 |
| **サポートされる IP アドレス範囲** |お客様の WAN 内の任意の有効な IP アドレス。 |お客様または接続プロバイダーが所有するパブリック IP アドレス。 |お客様または接続プロバイダーが所有するパブリック IP アドレス。 |
| **AS 番号の要件** |プライベートおよびパブリックの AS 番号。 いずれかを使用する場合はパブリックの AS 番号を所有している必要があります。 |プライベートおよびパブリックの AS 番号。 ただし、パブリック IP アドレスの所有権を証明する必要があります。 |プライベートおよびパブリックの AS 番号。 ただし、パブリック IP アドレスの所有権を証明する必要があります。 |
| **サポート対象 IP プロトコル**| IPv4 |  IPv4、IPv6 | IPv4 |
| **ルーティング インターフェイスの IP アドレス** |RFC1918 およびパブリック IP アドレス |ルーティング レジストリに登録されているパブリック IP アドレス。 |ルーティング レジストリに登録されているパブリック IP アドレス。 |
| **MD5 ハッシュのサポート** |はい |はい |はい |