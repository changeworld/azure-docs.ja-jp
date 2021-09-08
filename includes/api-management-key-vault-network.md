---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: f651dc7990212347d3179b9d4eddf7bf733ef6fd
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646475"
---
#### <a name="requirements-for-key-vault-firewall"></a>Key Vault ファイアウォールの要件

キー コンテナーで [Key Vault ファイアウォール](../articles/key-vault/general/network-security.md)が有効になっている場合、追加要件は次のとおりです。

* キー コンテナーにアクセスするには、API Management インスタンスの **システムによって割り当てられた** マネージド ID を使用する必要があります。
* Key Vault ファイアウォールで、 **[Allow Trusted Microsoft Services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** オプションを有効にします。

#### <a name="virtual-network-requirements"></a>仮想ネットワークの要件

API Management インスタンスが仮想ネットワークにデプロイされている場合は、次のネットワーク設定も構成してください。

* API Management サブネットで Azure Key Vault への[サービス エンドポイント](../articles/key-vault/general/overview-vnet-service-endpoints.md)を有効にします。
* AzureKeyVault と AzureActiveDirectory の[サービス タグ](../articles/virtual-network/service-tags-overview.md)への送信トラフィックを許可するネットワーク セキュリティ グループ (NSG) 規則を構成します。 

詳細については、[仮想ネットワークへの接続](../articles/api-management/api-management-using-with-vnet.md#network-configuration)に関するページに含まれているネットワーク構成の詳細を参照してください。