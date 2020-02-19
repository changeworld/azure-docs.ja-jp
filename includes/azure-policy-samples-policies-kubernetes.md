---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 49ba27ef559748a8805160558d48b7b7c2cbe80f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170160"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスターで特権コンテナーを許可しない](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターでの特権コンテナーの作成を許可しません。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスターで HTTPS イングレスを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターで HTTPS イングレスを強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスターで内部ロード バランサーを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターでロード バランサーがパブリック IP を持たないように強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内のポッドでラベルを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |このポリシーでは、指定されたラベルが Kubernetes クラスター内のポッドに指定されるように強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内の複数の名前空間にわたって一意のイングレス ホスト名を強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHostnamesConflict_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターの複数の名前空間にわたって一意のイングレス ホスト名を強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内でコンテナーの CPU およびメモリのリソース制限が指定された制限を超えないようにする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターでコンテナーの CPU およびメモリのリソース制限を定義して、指定された制限を超えないようにします。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内の許可されたポートでのみリッスンするようにコンテナーを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターで許可されたポートでのみコンテナーがリッスンするように強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内で許可されたコンテナー イメージのみを実行する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |このポリシーでは、許可されたコンテナー イメージのみが Kubernetes クラスターで実行されるようにします。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[プレビュー\]: \[AKS エンジン\] Kubernetes クラスター内の許可されたポートでのみリッスンするようにサービスを強制する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |このポリシーでは、Kubernetes クラスターで許可されたポートでのみサービスがリッスンするように強制します。 このポリシーの使用方法については、 https://aka.ms/kubepolicydoc にアクセスしてください。 |enforceOPAConstraint、disabled |1.0.0-preview |
