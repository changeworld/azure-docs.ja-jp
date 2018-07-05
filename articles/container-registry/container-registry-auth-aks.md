---
title: Azure Kubernetes Service から Azure Container Registry の認証を受ける
description: Azure Active Directory サービス プリンシパルを使用して、Azure Kubernetes Service からプライベート コンテナー レジストリ内のイメージへのアクセスを許可する方法を説明します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: iainfou
ms.openlocfilehash: 8cfd70275caa13c708f7d2f46cdc71e0f190ca0e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100625"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service から Azure Container Registry の認証を受ける

Azure Kubernetes Service (AKS) で Azure Container Registry (ACR) を使用する場合は、認証メカニズムを確立する必要があります。 このドキュメントでは、この 2 つの Azure サービス間で認証を行う場合に推奨される構成について詳しく説明します。

## <a name="grant-aks-access-to-acr"></a>ACR へのアクセス許可を AKS に付与する

AKS クラスターを作成すると、Azure リソースでのクラスターの運用性を管理するためにサービス プリンシパルも作成されます。 このサービス プリンシパルは、ACR レジストリでの認証も使用できます。 そのためには、ACR リソースへの読み取りアクセス許可をサービス プリンシパルに付与するロールの割り当てを作成する必要があります。

この作業は以下のサンプルを使用して実行できます。

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Kubernetes シークレットを使用したアクセス

場合によっては、AKS によって使用されているサービス プリンシパルの範囲を ACR レジストリに設定することができません。 このような場合は、一意のサービス プリンシパルを作成し、その範囲を ACR レジストリのみに設定することができます。

次のスクリプトを使用して、サービス プリンシパルを作成することができます。

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

サービス プリンシパルの資格情報を Kubernetes の[イメージ プル シークレット][image-pull-secret]に格納し、AKS クラスター内でコンテナーを実行するときに参照できるようになりました。

次のコマンドでは、Kubernetes シークレットが作成されます。 サーバー名を ACR ログイン サーバーに、ユーザー名をサービス プリンシパル ID に、パスワードをサービス プリンシパル パスワードに置き換えます。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

`ImagePullSecrets` パラメーターを使用すれば、Kubernetes シークレットをポッド デプロイで使用することができます。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
