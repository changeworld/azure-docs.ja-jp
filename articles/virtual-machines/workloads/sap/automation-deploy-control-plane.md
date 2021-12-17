---
title: SAP デプロイ自動化フレームワークでのコントロール プレーンのデプロイについて
description: Azure 上の SAP デプロイ自動化フレームワーク内のコントロール プレーン デプロイ プロセスの概要。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 17f7b02d6a1ed57341a32573cadec9f27a25b9c8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725179"
---
# <a name="deploy-the-control-plane"></a>コントロール プレーンをデプロイする

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)のコントロール プレーンのデプロイは、次のコンポーネントで構成されます。
 - デプロイ機能
 - SAP ライブラリ

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="コントロール プレーンの図。":::

## <a name="prepare-the-deployment-credentials"></a>デプロイ資格情報を準備する

SAP デプロイ フレームワークでは、デプロイを行う際にサービス プリンシパルを使用します。 コントロール プレーンのデプロイ用のサービス プリンシパルの作成は、サービス プリンシパルを作成するためのアクセス許可を持つアカウントを使用して次の手順で行います。


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

## <a name="deploy-the-control-plane"></a>コントロール プレーンをデプロイする
   
サンプルのデプロイ機能構成ファイル `MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE` フォルダーにあります。

サンプルの SAP ライブラリ構成ファイル `MGMT-WEEU-SAP_LIBRARY.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY` フォルダーにあります。

次のコマンドを実行すると、デプロイ機能と SAP ライブラリが作成され、サービス プリンシパルの詳細がデプロイ キー コンテナーに追加されます。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

az logout
az login

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      \
        --subscription $subscriptionID                                                                           \
        --spn_id $appID                                                                                          \
        --spn_secret  $spn_secret                                                                                \ 
        --tenant_id $tenant
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP00-INFRASTRUCTURE\MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars -Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
```
---


> [!NOTE]
> サンプル値 `<subscriptionID>` は実際のサブスクリプション ID に置き換えます。
> `<appID>`、`<password>`、`<tenant>` の値は、SPN 作成の出力値に置き換えます

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [SAP ワークロード ゾーンを構成する](automation-deploy-workload-zone.md)


