---
title: "OpenShift Container Platform の Azure へのデプロイ | Microsoft Docs"
description: "OpenShift Container Platform を Azure にデプロイします。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>OpenShift Container Platform の Azure へのデプロイ

OpenShift Container Platform を Azure にデプロイするには複数の方法があります。 必要なすべての Azure インフラストラクチャ コンポーネントを手動でデプロイしてから、OpenShift Container Platform の[ドキュメント](https://docs.openshift.com/container-platform/3.6/welcome/index.html)に従うことができます。
また、OpenShift Container Platform クラスターのデプロイを簡略化する既存の Resource Manager テンプレートを使用することもできます。 このようなテンプレートの 1 つが[ここ](https://github.com/Microsoft/openshift-container-platform/)にあります。

別のオプションとして、[Azure Marketplace Offer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview) の使用があります。

どちらのオプションでも、Red Hat サブスクリプションが必要です。 デプロイ中に、RHEL インスタンスは Red Hat サブスクリプションに登録され、OpenShift Container Platform の資格を含むプール ID に接続されます。
有効な Red Hat サブスクリプション マネージャーのユーザー名、パスワード、およびプール ID (RHSM ユーザー名、RHSM パスワード、およびプール ID) があることを確認してください。 これらの情報は、https://access.redhat.com にログインすることによって確認できます。

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager テンプレートを使用したデプロイ

Resource Manager テンプレートを使用してデプロイするには、すべての入力パラメーターを指定するためのパラメーター ファイルが使用されます。 入力パラメーターを使用した場合の対象にならないいずれかのデプロイ項目をカスタマイズする場合は、Github リポジトリをフォークし、該当する項目を変更します。

いくつかの一般的なカスタマイズ オプションには、次のものが含まれます (ただし、これらには限定されません)。

- VNet CIDR [azuredeploy.json 内の変数]
- Bastion VM サイズ [azuredeploy.json 内の変数]
- 名前付け規則 [azuredeploy.json 内の変数]
- OpenShift クラスターの詳細 - ホスト ファイル [deployOpenShift.sh] 経由で変更済み

### <a name="configure-parameters-file"></a>パラメーター ファイルを構成する

以前に作成したサービス プリンシパルの値 `appId` を `aadClientId` パラメーターに使用します。 

次の例では、必要なすべての入力を含む **azuredeploy.parameters.json** という名前のパラメーター ファイルを作成します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

{} で囲まれている項目を適切な情報で置き換えます。

### <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ

> [!NOTE] 
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 az CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

次の例では、OpenShift クラスターとすべての関連リソースを myResourceGroup という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは Github リポジトリから直接参照され、**azuredeploy.parameters.json** ファイルという名前のローカル パラメーター ファイルが使用されます。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数に応じて、このデプロイが完了するには少なくとも 30 分かかります。 デプロイが完了すると、OpenShift コンソールの URL と OpenShift マスターの DNS 名がターミナルに出力されます。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>OpenShift Container Platform マーケットプレース プランを使用したデプロイ

OpenShift Container Platform を Azure にデプロイするための最も簡単な方法は、[Azure Marketplace Offer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview) の使用です。

このオプションは最も簡単であるだけでなく、制限されたカスタマイズ機能も備えています。 このプランには、次の 3 つの構成オプションが含まれます。

- [Small] (小): 1 つのマスター ノード、1 つのインフラストラクチャ ノード、2 つのアプリケーション ノード、および 1 つの Bastion ノードを含む非 HA クラスターをデプロイします。 すべてのノードが標準の DS2v2 VM サイズです。 このクラスターは合計 10 個のコアを必要とし、小規模なテストに最適です。
- [中]: 3 つのマスター ノード、2 つのインフラストラクチャ ノード、4 つのアプリケーション ノード、および 1 つの Bastion ノードを含む HA クラスターをデプロイします。 Bastion を除くすべてのノードが標準の DS3v2 VM サイズです。 Bastion ノードは標準の DS2v2 です。 このクラスターには 38 個のコアが必要です。
- [Large] (大): 3 つのマスター ノード、2 つのインフラストラクチャ ノード、6 つのアプリケーション ノード、および 1 つの Bastion ノードを含む HA クラスターをデプロイします。 マスターおよびインフラストラクチャ ノードは標準の DS3v2 VM サイズであり、アプリケーション ノードは標準の DS4v2 VM サイズであり、Bastion ノードは標準の DS2v2 です。 このクラスターには 70 個のコアが必要です。

Azure Cloud Provider の構成は、[中] と [Large] (大) のクラスター サイズでは省略可能です。 [Small] (小) のクラスター サイズでは、Azure Cloud Provider を構成するためのオプションは提供されません。

## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、ブラウザーで `OpenShift Console Uri` を使用して、OpenShift コンソールに接続します。 あるいは、次のコマンドを使用して OpenShift マスターに接続できます。

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、OpenShift クラスター、およびすべての関連リソースを削除できます。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- [デプロイ後のタスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
- [OpenShift Container Platform の概要](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
