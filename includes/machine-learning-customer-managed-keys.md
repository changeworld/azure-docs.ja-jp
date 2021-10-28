---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 09/14/2021
ms.author: larryfr
ms.openlocfilehash: 71810ecc6526364e37fe9a578d1e88d334db0d20
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238336"
---
> [!IMPORTANT]
> この Cosmos DB インスタンスは、__サブスクリプション__ 内の Microsoft が管理対象リソース グループに作成されます。 このリソース グループには次のサービスも作成され、ユーザーが管理するキー構成によって使用されます。
> * Azure Storage アカウント
> * Azure Search
>
> これらのサービスは Azure サブスクリプションで作成されるため、これらのサービス インスタンスに対して課金されることを意味します。 サブスクリプションに Azure Cosmos DB サービス用に十分なクォータがない場合は、エラーが発生します。 クォータの詳細については、「[Azure Cosmos DB サービスのクォータ](../articles/cosmos-db/concepts-limits.md)」を参照してください
>
> マネージド リソース グループの名前は、`<AML Workspace Resource Group Name><GUID>` という形式で指定されます。 Azure Machine Learning ワークスペースでプライベート エンドポイントが使用される場合は、このリソース グループ内に仮想ネットワークも作成されます。 この VNet は、このリソース グループ内のサービスと Azure Machine Learning ワークスペース間の通信をセキュリティで保護するために使用されます。
> 
> * このグループに自動的に作成されたリソース、またはこの Cosmos DB インスタンスが含まれる __リソース グループを削除しないでください__。 Cosmos DB インスタンスなどのリソース グループを削除する必要がある場合は、それが使用されている Azure Machine Learning ワークスペースを削除してください。 関連付けられているワークスペースが削除されると、リソース グループ、Cosmos DB インスタンス、および自動的に作成されたその他のリソースが削除されます。
> * この Cosmos DB アカウントで使用される [__要求ユニット__](../articles/cosmos-db/request-units.md)は、必要に応じて自動的にスケーリングされます。 __最小__ RU は __1,200__ です。 __最大__ RU は __12,000__ です。
> * 作成された __Cosmos DB インスタンスでの使用のために、独自の VNet を指定することはできません__。 また、__仮想ネットワークを変更することはできません__。 たとえば、使用される IP アドレスの範囲を変更することはできません。
> 
> Azure Cosmos DB インスタンスの追加コストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。