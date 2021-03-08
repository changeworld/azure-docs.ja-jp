---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445198"
---
* 新しいワークスペースを作成する場合は、ワークスペースに必要なサービスを自動的に作成するか、既存のサービスを使用することができます。 ワークスペースとは __異なる Azure サブスクリプション の既存のサービス__ を使用する場合は、それらのサービスを含むサブスクリプションに Azure Machine Learning 名前空間を登録する必要があります。 たとえば、サブスクリプション B のストレージ アカウントを使用するサブスクリプション A でワークスペースを作成する場合、ワークスペースでストレージ アカウントを使用するには、Azure Machine Learning 名前空間がサブスクリプション B に登録されている必要があります。

    Azure Machine Learning のリソース プロバイダーは、__Microsoft.MachineLearningService__ です。 登録されているかどうかの確認方法、および登録方法の詳細については、「[Azure リソース プロバイダーと種類](../articles/azure-resource-manager/management/resource-providers-and-types.md)」の記事を参照してください。

    > [!IMPORTANT]
    > これは、ワークスペースの作成時に提供されるリソースである、Azure Storage アカウント、Azure Container Register、Azure Key Vault、Application Insights にのみ適用されます。