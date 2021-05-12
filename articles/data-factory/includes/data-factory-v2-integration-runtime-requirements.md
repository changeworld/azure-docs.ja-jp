---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: f2d93449744ea7993ec22f200c51fea8280d1576
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747803"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure 仮想ネットワーク、または Amazon Virtual Private Cloud 内にある場合は、それに接続するよう[セルフホステッド統合ランタイム](../create-self-hosted-integration-runtime.md)を構成する必要があります。

また、データ ストアがマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用できます。 ファイアウォール規則で承認されている IP にアクセスが制限されている場合は、[Azure Integration Runtime の IP](../azure-integration-runtime-ip-addresses.md) を許可リストに追加できます。 

Data Factory によってサポートされるネットワーク セキュリティ メカニズムやオプションの詳細については、「[データ アクセス戦略](../data-access-strategies.md)」を参照してください。
