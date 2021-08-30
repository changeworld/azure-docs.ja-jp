---
title: Azure Red Hat OpenShift (ARO) クラスターのサービス プリンシパルの資格情報をローテーションする
description: Azure Red Hat OpenShift (ARO) のサービス プリンシパルの資格情報をローテーションする方法について説明します。
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090873"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) クラスターのサービス プリンシパルの資格情報をローテーションする
この記事では、Azure Red Hat OpenShift クラスター ([ARO](https://github.com/Azure/ARO-RP)) でサービス プリンシパルの資格情報をローテーションするために必要な詳細情報を提供します。

## <a name="before-you-begin"></a>開始する前に
この記事では、最新の更新プログラムが適用されている既存の ARO クラスターがあることを前提としています。

ARO クラスター内でサービス プリンシパルの資格情報をローテーションするための最小 Azure CLI 要件は2.24.0 です。

Azure CLI のバージョンを確認するには以下を実行します。
```azurecli-interactive
# Azure CLI version
az --version
```
Azure CLI をインストールまたはアップグレードするには、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」に従ってください。

次の手順では、bash 構文を使用します。

## <a name="service-principal-credential-rotation"></a>サービス プリンシパルの資格情報のローテーション
>[!IMPORTANT]
>  サービス プリンシパルの資格情報のローテーションは、クラスターの状態によっては 2 時間以上かかることがあります。

サービス プリンシパルの資格情報のローテーションには、次の 2 つの方法があります。
 - [自動化されたサービス プリンシパルの資格情報のローテーション](#Automated-Service-Principal-Credential-Rotation)
 - [ユーザー指定のクライアント ID とクライアントシークレットのサービス プリンシパルの資格情報のローテーション](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)

### <a name="automated-service-principal-credential-rotation"></a>自動化されたサービス プリンシパルの資格情報のローテーション<a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  自動化されたサービス プリンシパルの資格情報のローテーションには、Azure CLI バージョン2.24.0 以上で ARO クラスターを作成する必要があります。

自動化されたサービス プリンシパルの資格情報のローテーションでは、サービス プリンシパルが存在するかどうかが確認され、新しいサービス プリンシパルがローテーションまたは作成されます。

次のコマンドを使用して、サービス プリンシパルの資格情報を自動的にローテーションします。

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>ユーザー指定のクライアント ID とクライアントシークレットのサービス プリンシパルの資格情報のローテーション<a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


次の手順に従って、ユーザー指定のクライアント ID とクライアントシークレットを使用してサービス プリンシパルの資格情報を手動でローテーションします。

サービス プリンシパル クライアント ID (`--client-id`) を取得し、これを `SP_ID` 環境変数として設定します。
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
上記の `SP_ID` 変数を使用してサービス プリンシパルの新しいセキュア シークレット (`--client-secret`) を生成します。 `SP_SECRET` 環境変数として新しいセキュア シークレットを格納します。
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
上記の環境変数を使用して、サービス プリンシパルの資格情報をローテーションします。
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>トラブルシューティング
### <a name="service-principal-expiration-date"></a>サービス プリンシパルの有効期限日
サービス プリンシパルの資格情報には、1 年の有効期限が設定されており、指定された期間内にローテーションされる必要があります。

有効期限が過ぎると、次のエラーが発生する可能性があります。
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
サービス プリンシパルの資格情報の有効期限を確認するには、以下を実行します。
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
サービス プリンシパルの資格情報の有効期限が切れている場合、2 つの資格情報ローテーション方法のいずれかを使用して更新してください。

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>クラスター AAD アプリケーションには、説明が空のクライアント シークレットが含まれています
[自動化されたサービス プリンシパルの資格情報のローテーション](#Automated-Service-Principal-Credential-Rotation)を使用すると、次のエラーが発生します。
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
クラスターが Azure CLI 2.24.0 以上を使用して作成されていません。 代わりに[ユーザー指定のクライアント ID とクライアントシークレットのサービス プリンシパルの資格情報のローテーション](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)を使用してください。

### <a name="azure-cli-aro-update-help"></a>Azure CLI ARO 更新プログラムのヘルプ
詳細については、Azure CLI ARO 更新プログラムのヘルプ コマンドを参照してください。
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
