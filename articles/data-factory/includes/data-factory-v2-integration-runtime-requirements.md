---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 66b7159d19838cf08158d794f9b8e8819efc3f41
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112040429"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure 仮想ネットワーク、または Amazon Virtual Private Cloud 内にある場合は、それに接続するよう[セルフホステッド統合ランタイム](../create-self-hosted-integration-runtime.md)を構成する必要があります。

データ ストアがマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用できます。 ファイアウォール規則で承認されている IP にアクセスが制限されている場合は、[Azure Integration Runtime の IP](../azure-integration-runtime-ip-addresses.md) を許可リストに追加できます。 

また、Azure Data Factory の[マネージド仮想ネットワーク統合ランタイム](../tutorial-managed-virtual-network-on-premise-sql-server.md)機能を使用すれば、セルフホステッド統合ランタイムをインストールして構成しなくても、オンプレミス ネットワークにアクセスすることができます。

Data Factory によってサポートされるネットワーク セキュリティ メカニズムやオプションの詳細については、「[データ アクセス戦略](../data-access-strategies.md)」を参照してください。
