---
title: Azure Container Registry へのイメージの保存
description: Azure Container Registry に Docker コンテナー イメージが保存される方法について、セキュリティ、冗長性、および容量も含めて説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165097"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry へのコンテナー イメージの保存

すべての Azure Container Registry ([Basic、Standard、および Premium](container-registry-skus.md)) は、イメージ データのセキュリティ保護のための保存時の暗号化やイメージ データの保護のための geo 冗長などの Azure の高度なストレージ機能の恩恵を受けています。 次のセクションで、Azure Container Registry (ACR) におけるイメージの保存に関する機能と制限の両方について説明します。

## <a name="encryption-at-rest"></a>保存時の暗号化

レジストリ内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、アプリケーションやサービスがイメージをプルするときにその場で暗号解除されます。

## <a name="geo-redundant-storage"></a>geo 冗長ストレージ

Azure では、geo 冗長ストレージ スキームを使用して、コンテナー イメージの損失を防ぎます。 Azure Container Registry は、コンテナー イメージを複数の地理的に離れたデータ センターに自動的にレプリケートして、地域的なストレージ障害が発生した場合の損失を防ぎます。

## <a name="geo-replication"></a>geo レプリケーション

より以上の高可用性の保証を必要とするシナリオでは、Premium レジストリの機能である [geo レプリケーション](container-registry-geo-replication.md)の使用を検討します。 geo レプリケーションは、ストレージ障害だけでなく、*地域全体*で障害が発生した場合に、レジストリにアクセスできなくなるのを防ぐために役立ちます。 geo レプリケーションには、他にも、分散型の開発またはデプロイ シナリオで、プッシュとプルを高速で実行するためのネットワークに近いイメージの保存などのメリットがあります。

## <a name="image-limits"></a>イメージの制限

次の表は、Azure Container Registry に適用されるコンテナー イメージとストレージの制限について説明しています。

| リソース | 制限 |
| -------- | :---- |
| リポジトリ | 制限なし |
| イメージ | 制限なし |
| レイヤー | 制限なし |
| タグ | 制限なし|
| Storage | 5 TB |

膨大な数のリポジトリとタグは、レジストリのパフォーマンスに影響する可能性があります。 レジストリのメンテナンス ルーチンの一部として、[Azure CLI](/cli/azure/acr)、ACR [REST API](/rest/api/containerregistry/)、または [Azure Portal][portal] を使用した未使用のリポジトリ、タグ、およびイメージの定期的な削除を実行してください。 リポジトリ、イメージ、タグなどの削除されたレジストリ リソースは、削除後に元に戻すことは*できません*。

## <a name="storage-cost"></a>Storage コスト

価格の詳細については、「[Azure Container Registry の価格][pricing]」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Container Registry の SKU (Basic、Standard、Premium) の詳細については、「[Azure Container Registry SKU](container-registry-skus.md)」を参照してください。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
