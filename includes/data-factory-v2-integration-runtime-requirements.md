---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629533"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアが次のいずれかの方法で構成されている場合、データ ストアに接続するには、[セルフホステッド統合ランタイム](../articles/data-factory/create-self-hosted-integration-runtime.md)を設定する必要があります。

- データ ストアは、オンプレミスのネットワーク内、Azure Virtual Network 内、または Amazon Virtual Private Cloud 内に配置されています。
- データ ストアは、ファイアウォール規則でホワイトリストに登録されている IP にアクセスが制限されるマネージド クラウド データ サービスです。
