---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 8a56b564d9126197b76d6e96cc56bb51eca980eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520433"
---
* 作成できる最小のサブネット AzureBastionSubnet サイズは /26 です。 ホストのスケーリングに対応するために、/26 以上のサイズを作成することをお勧めします。 
   * スケーリングの詳細については、[ホスト スケーリングの構成設定](../articles/bastion/configuration-settings.md#instance)に関する記事を参照してください。
   * 設定の詳細については、[AzureBastionSubnet の構成設定](../articles/bastion/configuration-settings.md#instance)に関する記事を参照してください。
* ルート テーブルまたは委任なしで **AzureBastionSubnet** を作成します。 
* **AzureBastionSubnet** でネットワーク セキュリティ グループを使用する場合、[NSG の使用](../articles/bastion/bastion-nsg.md)に関する記事を参照してください。
