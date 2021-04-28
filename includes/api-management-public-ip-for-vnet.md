---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531660"
---
* クライアントで API バージョン 2021-01-01-preview 以降を使用している場合は、**Standard SKU [パブリック IPv4 アドレス](../articles/virtual-network/public-ip-addresses.md#standard)** 。 パブリック IP アドレス リソースは、外部または内部アクセスのいずれかに対して仮想ネットワークを設定するときに必要です。 内部仮想ネットワークでは、パブリック IP アドレスは管理操作にのみ使用されます。 詳細は「[Azure API Management の IP アドレス](../articles/api-management/api-management-howto-ip-addresses.md)」を参照してください。

  * この IP アドレスは、API Management インスタンスおよび仮想ネットワークと同じリージョンおよびサブスクリプションにある必要があります。

  * IP アドレスの値は、そのリージョンの API Management インスタンスの仮想パブリック IPv4 アドレスとして割り当てられます。 

  * 外部から内部の仮想ネットワーク (またはその逆) に変更する場合、ネットワーク内のサブネットを変更する場合、または API Management インスタンスの可用性ゾーンを更新する場合は、別のパブリック IP アドレスを構成する必要があります。 

  > [!IMPORTANT]
  > 現在は、API Management インスタンスを作成または更新するときに、Azure portal では API バージョン 2021-01-01 preview が使用されます。 この API バージョンは、Azure Resource Manager テンプレートまたは API Management REST API を使用して指定できます。 Azure CLI と Azure PowerShell では、現在、API バージョン 2020-12-01 がサポートされています。
