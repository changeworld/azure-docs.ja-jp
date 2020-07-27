---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544110"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure Virtual Network、または Amazon Virtual Private Cloud 内にある場合は、[セルフホステッド統合ランタイム](../articles/data-factory/create-self-hosted-integration-runtime.md)を設定して接続する必要があります。

データ ストアが、ファイアウォール規則でホワイトリストに登録されている IP にアクセスが制限されるマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用して、[その IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) を許可リストに追加できます。 

データ ストアにアクセスするために Data Factory によって一般的にサポートされるネットワーク セキュリティ メカニズムについては、「[データ アクセス戦略](../articles/data-factory/data-access-strategies.md)」を参照してください。
