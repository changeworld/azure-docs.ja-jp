---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023190"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure 仮想ネットワーク、または Amazon Virtual Private Cloud 内にある場合は、それに接続するよう[セルフホステッド統合ランタイム](../articles/data-factory/create-self-hosted-integration-runtime.md)を構成する必要があります。

また、データ ストアがマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用できます。 ファイアウォール規則で承認されている IP にアクセスが制限されている場合は、[Azure Integration Runtime の IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) を許可リストに追加できます。 

Data Factory によってサポートされるネットワーク セキュリティ メカニズムやオプションの詳細については、「[データ アクセス戦略](../articles/data-factory/data-access-strategies.md)」を参照してください。
