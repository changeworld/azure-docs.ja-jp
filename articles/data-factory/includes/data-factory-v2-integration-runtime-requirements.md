---
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.openlocfilehash: a5fbf3071c134b52fb053afc88f6efb51a5ea2f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725627"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure 仮想ネットワーク、または Amazon Virtual Private Cloud 内にある場合は、それに接続するよう[セルフホステッド統合ランタイム](../create-self-hosted-integration-runtime.md)を構成する必要があります。

データ ストアがマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用できます。 ファイアウォール規則で承認されている IP にアクセスが制限されている場合は、[Azure Integration Runtime の IP](../azure-integration-runtime-ip-addresses.md) を許可リストに追加できます。 

また、Azure Data Factory の[マネージド仮想ネットワーク統合ランタイム](../tutorial-managed-virtual-network-on-premise-sql-server.md)機能を使用すれば、セルフホステッド統合ランタイムをインストールして構成しなくても、オンプレミス ネットワークにアクセスすることができます。

Data Factory によってサポートされるネットワーク セキュリティ メカニズムやオプションの詳細については、「[データ アクセス戦略](../data-access-strategies.md)」を参照してください。
