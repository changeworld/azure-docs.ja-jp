---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640647"
---
* 作成できる最小のサブネット AzureBastionSubnet サイズは /27 です。 ただし、ホストのスケーリングに対応するために、/26 以上のサイズを作成することをお勧めします。 
   * スケーリングの詳細については、[ホスト スケーリングの構成設定](../articles/bastion/configuration-settings.md#instance)に関する記事を参照してください。
   * 設定の詳細については、[AzureBastionSubnet の構成設定](../articles/bastion/configuration-settings.md#instance)に関する記事を参照してください。
* ルート テーブルまたは委任なしで **AzureBastionSubnet** を作成します。 
* **AzureBastionSubnet** でネットワーク セキュリティ グループを使用する場合、[NSG の使用](../articles/bastion/bastion-nsg.md)に関する記事を参照してください。
