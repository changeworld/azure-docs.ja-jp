---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 413328496d35346ef6b7219bbed40755160ce9eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288248"
---
* **Standard SKU [ の](../articles/virtual-network/ip-services/public-ip-addresses.md#standard)パブリック IPv4 アドレス**。 パブリック IP アドレス リソースは、外部または内部アクセスのいずれかに対して仮想ネットワークを設定するときに必要です。 内部仮想ネットワークでは、パブリック IP アドレスは管理操作にのみ使用されます。 詳細は「[Azure API Management の IP アドレス](../articles/api-management/api-management-howto-ip-addresses.md)」を参照してください。

  * この IP アドレスは、API Management インスタンスおよび仮想ネットワークと同じリージョンおよびサブスクリプションにある必要があります。

  * IP アドレスの値は、そのリージョンの API Management インスタンスの仮想パブリック IPv4 アドレスとして割り当てられます。 

  * 外部から内部の仮想ネットワーク (またはその逆) に変更する場合、ネットワーク内のサブネットを変更する場合、または API Management インスタンスの可用性ゾーンを更新する場合は、別のパブリック IP アドレスを構成する必要があります。