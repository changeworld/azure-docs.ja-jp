---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5accc38a8693fba5934b1182ebdafe8921540d98
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170210"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[\[限定プレビュー\]:\[AKS\] AKS で特権コンテナーを許可しない](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerNoPrivilege_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターでの特権コンテナーの作成を許可しません。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS で HTTPS イングレスを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHttpsOnly_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターで HTTPS イングレスを強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS で内部ロード バランサーを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/LoadbalancerNoPublicIPs_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターでロード バランサーがパブリック IP を持たないように強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS でポッドにラベルを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/PodEnforceLabels_EnforceRegoPolicy.json) |このポリシーでは、指定されたラベルが Azure Kubernetes Service クラスター内のポッドに指定されるように強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS で複数の名前空間にわたって一意のイングレス ホスト名を強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHostnamesConflict_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターの複数の名前空間にわたって一意のイングレス ホスト名を強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS で許可されたポートでのみコンテナーがリッスンするようにする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedPorts_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターで許可されたポートでのみコンテナーがリッスンするように強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS でコンテナーに CPU とメモリのリソース制限が定義されるようにする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerResourceLimits_EnforceRegoPolicy.json) |このポリシーは、Azure Kubernetes Service クラスターのコンテナーで CPU とメモリのリソース制限が確実に定義されるようにします。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] 許可されたコンテナー イメージのみ AKS で実行されるようにする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedImages_EnforceRegoPolicy.json) |このポリシーでは、許可されたコンテナー イメージのみが Azure Kubernetes Service クラスターで実行されるようにします。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[限定プレビュー\]:\[AKS\] AKS で許可されたポートでのみサービスがリッスンするようにする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ServiceAllowedPorts_EnforceRegoPolicy.json) |このポリシーでは、Azure Kubernetes Service クラスターで許可されたポートでのみサービスがリッスンするように強制します。 限定プレビューのポリシーは、登録済みサブスクリプションに対してのみ有効です。 登録するには、 https://aka.ms/akspolicyonboarding にアクセスしてください。 このポリシーの使用方法については、 https://aka.ms/akspolicydoc にアクセスしてください。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
