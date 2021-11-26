---
title: 自動化フレームワークでのワークロード ゾーンのデプロイについて
description: Azure 上の SAP デプロイ自動化フレームワーク内の SAP ワークロード ゾーンのデプロイ プロセスの概要。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 1942eb957d601696c6dcb8044386d7c2304748c8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725178"
---
# <a name="workload-zone-deployment-in-sap-automation-framework"></a>SAP 自動化フレームワークでのワークロード ゾーンのデプロイ

通常、[SAP アプリケーション](automation-deployment-framework.md#sap-concepts)には複数の開発レベルがあります。 たとえば、開発、品質保証、実稼働のレベルがある場合があります。 [Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、これらのレベルを[ワークロード ゾーン](automation-deployment-framework.md#deployment-components)と呼びます。

ワークロード ゾーンは複数の Azure リージョンで使用できます。 そのため、各ワークロード ゾーンにそれぞれ独自の Azure Virtual Network (Azure VNet) があります

SAP ワークロード ゾーンでは、次のサービスが提供されます。

- Azure Virtual Network (サブネットやネットワーク セキュリティ グループを含みます)。
- Azure Key Vault (システム資格情報用)。
- ブート診断用のストレージ アカウント
- クラウド監視用のストレージ アカウント

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="SAP ワークロードゾーンの図。":::

ワークロード ゾーンは通常、ハブ アンド スポーク アーキテクチャのスポークにデプロイされます。 これらは独自のサブスクリプションに含まれている場合があります。

コントロール プレーンのプライベート DNS をサポートします。


## <a name="core-configuration"></a>コア構成

次のパラメーター ファイルの例では、必須のパラメーターのみを示します。

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# network_address_space is a mandatory parameter when an existing Virtual network is not used
network_address_space="10.110.0.0/16"

# admin_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
admin_subnet_address_prefix="10.110.0.0/19"

# db_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
db_subnet_address_prefix="10.110.96.0/19"

# app_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
app_subnet_address_prefix="10.110.32.0/19"

# The automation_username defines the user account used by the automation
automation_username="azureadm"

```

## <a name="preparing-the-workload-zone-deployment-credentials"></a>ワークロード ゾーンのデプロイの資格情報を準備する

SAP デプロイ フレームワークでは、デプロイを行う際にサービス プリンシパルを使用します。 ワークロード ゾーンのデプロイ用のサービス プリンシパルの作成は、サービス プリンシパルを作成するためのアクセス許可を持つアカウントを使用して次の手順で行います。


```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>" --name="<environment>-Deployment-Account"
  
```

> [!IMPORTANT]
> サービス プリンシパルの名前は一意であることが必要です。
>
> コマンドからの出力値を記録します。
   > - appId
   > - password
   > - tenant

サービス プリンシパルに適切なアクセス許可を割り当てます。 

```azurecli-interactive
az role assignment create --assignee <appId> --role "User Access Administrator"
```

## <a name="deploying-the-sap-workload-zone"></a>SAP ワークロード ゾーンをデプロイする
   
サンプルのワークロード ゾーン構成ファイル `DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE` フォルダーにあります。

次のコマンドを実行すると、SAP ワークロード ゾーンがデプロイされます。

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> デプロイ機能からこのタスクを実行します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>
keyvault=<keyvaultName>
storageaccount=<storageaccountName>
statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars   \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret "$spn_secret"                             \
        --tenant_id $tenant_id
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"
$keyvault=<keyvaultName>
$storageaccount=<storageaccountName>
$statefile_subscription=<statefile_subscription>

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

New-SAPWorkloadZone -Parameterfile .DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
-Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
-State_subscription $statefile_subscription -Vault $keyvault -$StorageAccountName $storageaccount
```

---

> [!NOTE]
> サンプル値 `<subscriptionID>` は実際のサブスクリプション ID に置き換えます。
> `<appID>`、`<password>`、`<tenant>` の値は、SPN 作成の出力値に置き換えます。`<keyvaultName>` は、デプロイ機能のキー コンテナー名に置き換えます。`<storageaccountName>` は、Terraform 状態ファイルを含むストレージ アカウントの名前に置き換えます。`<statefile_subscription>` は、Terraform 状態ファイルを含むストレージ アカウントのサブスクリプション ID に置き換えます

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [自動化フレームワークでの SAP システムのデプロイについて](automation-configure-system.md)
