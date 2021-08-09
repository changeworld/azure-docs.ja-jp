---
title: Kubernetes から Azure Container Registry の認証を受ける
description: Kubernetes クラスターからコンテナー イメージをプルするために Azure Container Registry を認証するためのオプションとシナリオの概要
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 738bdf617d17c0bd621614ee0fd32f2d0e18b729
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442218"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>Kubernetes から Azure Container Registry の認証を受ける


Azure Container Registry は、管理するクラスター、[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md) などのクラウドでホストされている管理対象クラスター、[minikube](https://minikube.sigs.k8s.io/) や [kind](https://kind.sigs.k8s.io/) などの "ローカル" Kubernetes 構成といった、Kubernetes のコンテナー イメージのソースとして使用できます。 

Azure Container Registry から Kuberentes クラスターにイメージをプルするには、認証と認可のメカニズムを確立する必要があります。 クラスター環境に応じて、次のいずれかの方法を選択します。

## <a name="scenarios"></a>シナリオ

| Kubernetes クラスター |認証方法  | 説明  | 例 | 
|---------|---------|---------|----------|
| AKS クラスター |AKS マネージド ID    |  AKS kubelet [マネージ ID](../aks/use-managed-identity.md) を有効にして、アタッチされている Azure Container Registry からイメージをプルします。<br/><br/> レジストリは、同じまたは別の Azure サブスクリプションに配置できます。      | [Azure Kubernetes Service から Azure Container Registry の認証を受ける](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| AKS クラスター | AKS サービス プリンシパル     | ターゲットの Azure Container Registry に対するアクセス許可を持つ [AKS サービス プリンシパル](../aks/kubernetes-service-principal.md)を有効にします。<br/><br/>レジストリは、同じ Azure Active Directory テナント内でも、別のテナント内でも構いません。        | [Azure Container Registry から別の AD テナントの AKS クラスターにイメージをプルする](authenticate-aks-cross-tenant.md)
| AKS 以外の Kubernetes クラスター |ポッド [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  一般的な Kubernetes メカニズムを使用して、ポッド デプロイのレジストリ資格情報を管理します。<br/><br/>AD サービス プリンシパル、リポジトリ スコープのトークン、またはその他の[レジストリ資格情報](container-registry-authentication.md)を構成します。  | [プル シークレットを使用して Azure Container Registry から Kubernetes クラスターにイメージをプルする](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>次のステップ

* [Azure Container Registry で認証](container-registry-authentication.md)する方法について詳しくは、こちらをご覧ください。
