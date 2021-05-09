---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: 6d9b6fe18d7eb4ce532c6c2960944420eee952f0
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903968"
---
> [!IMPORTANT]
> この Cosmos DB インスタンスは、__サブスクリプション__ 内の Microsoft が管理対象リソース グループに作成されます。 このリソース グループには次のサービスも作成され、ユーザーが管理するキー構成によって使用されます。
> * Azure Storage アカウント
> * Azure Search
>
> これらのサービスは Azure サブスクリプションで作成されるため、これらのサービス インスタンスに対して課金されることを意味します。 
>
> マネージド リソース グループの名前は、`<AML Workspace Resource Group Name><GUID>` という形式で指定されます。 Azure Machine Learning ワークスペースでプライベート エンドポイントが使用される場合は、このリソース グループ内に仮想ネットワークも作成されます。 この VNet は、このリソース グループ内のサービスと Azure Machine Learning ワークスペース間の通信をセキュリティで保護するために使用されます。
> 
> * このグループに自動的に作成されたリソース、またはこの Cosmos DB インスタンスが含まれる __リソース グループを削除しないでください__。 Cosmos DB インスタンスなどのリソース グループを削除する必要がある場合は、それが使用されている Azure Machine Learning ワークスペースを削除してください。 関連付けられているワークスペースが削除されると、リソース グループ、Cosmos DB インスタンス、および自動的に作成されたその他のリソースが削除されます。
> * この Cosmos DB アカウントの既定の [__要求ユニット__](../articles/cosmos-db/request-units.md)は __8000__ に設定されています。
> * 作成された __Cosmos DB インスタンスでの使用のために、独自の VNet を指定することはできません__。 また、__仮想ネットワークを変更することはできません__。 たとえば、使用される IP アドレスの範囲を変更することはできません。