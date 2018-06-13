---
title: OpenShift Container Platform の Azure へのデプロイ | Microsoft Docs
description: OpenShift Container Platform を Azure にデプロイします。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125743"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>OpenShift Container Platform の Azure へのデプロイ

Azure で OpenShift Container Platform をデプロイするには、次のいずれかの方法を使用できます。

- OpenShift Container Platform の[ドキュメント](https://docs.openshift.com/container-platform/3.6/welcome/index.html)の手順を実行するには、事前に必要な Azure インフラストラクチャ コンポーネントを手動でデプロイする必要があります。
- また、OpenShift Container Platform クラスターのデプロイを簡略化する既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-container-platform/)を使用することもできます。
- 別のオプションとして、[Azure Marketplace プラン](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)の使用があります。

どのオプションでも、Red Hat サブスクリプションが必要です。 デプロイ中に、Red Hat Enterprise Linux インスタンスは Red Hat サブスクリプションに登録され、OpenShift Container Platform の資格を含むプール ID に接続されます。
有効な Red Hat Subscription Manager (RHSM) のユーザー名、パスワード、およびプール ID があることを確認してください。 これらの情報は、https://access.redhat.com にサインインして確認できます。

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager テンプレートを使用したデプロイ

Resource Manager テンプレートを使用してデプロイするには、パラメーター ファイルを使用して入力パラメーターを指定します。 入力パラメーターで指定できないデプロイ項目をカスタマイズするには、GitHub リポジトリをフォークし、該当する項目を変更します。

一部の一般的なカスタマイズ オプションには、以下のような項目があります。

- 仮想ネットワーク CIDR (azuredeploy.json 内の変数)
- Bastion VM サイズ (azuredeploy.json 内の変数)
- 名前付け規則 (azuredeploy.json 内の変数)
- OpenShift クラスターの詳細 - ホスト ファイル (deployOpenShift.sh) 経由で変更済み

### <a name="configure-the-parameters-file"></a>パラメーター ファイルの構成

以前に作成したサービス プリンシパルの値 `appId` を `aadClientId` パラメーターに使用します。 

次の例では、必要なすべての入力を含む azuredeploy.parameters.json という名前のパラメーター ファイルを作成します。

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

かっこで囲まれた項目は、実際の情報で置き換えます。

### <a name="deploy-by-using-azure-cli"></a>Azure CLI を使用したデプロイ

> [!NOTE] 
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)」を参照してください。

次の例では、OpenShift クラスターとすべての関連リソースを myResourceGroup という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは GitHub リポジトリから直接参照され、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルが使用されます。

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

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>OpenShift Container Platform Azure Marketplace プランを使用したデプロイ

OpenShift Container Platform を Azure にデプロイするための最も簡単な方法は、[Azure Marketplace Offer](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview) の使用です。

これは最も簡単なだけでなく、制限されたカスタマイズ機能も備えている方法です。 このプランには、次の 3 つの構成オプションが含まれます。

- **[小]**: 1 つのマスター ノード、1 つのインフラストラクチャ ノード、2 つのアプリケーション ノード、および 1 つの要塞ノードを含む非 HA クラスターをデプロイします。 すべてのノードが標準の DS2v2 VM サイズです。 このクラスターは合計 10 個のコアを必要とし、小規模なテストに最適です。
- **[中]**: 3 つのマスター ノード、2 つのインフラストラクチャ ノード、4 つのアプリケーション ノード、および 1 つの要塞ノードを含む HA クラスターをデプロイします。 要塞ノードを除くすべてのノードが標準の DS3v2 VM サイズです。 要塞ノードは標準の DS2v2 です。 このクラスターには 38 個のコアが必要です。
- **[大]**: 3 つのマスター ノード、2 つのインフラストラクチャ ノード、6 つのアプリケーション ノード、および 1 つの要塞ノードを含む HA クラスターをデプロイします。 マスター ノードとインフラストラクチャ ノードは標準の DS3v2 VM サイズです。 アプリケーション ノードは標準の DS4v2 VM サイズ、要塞ノードは標準の DS2v2 です。 このクラスターには 70 個のコアが必要です。

Azure Cloud Solution Provider の構成は、[中] と [大] のクラスター サイズでは省略可能です。 [小] のクラスター サイズでは、Azure Cloud Solution Provider を構成するためのオプションは提供されません。

## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、ブラウザーで `OpenShift Console Uri` を使用して、OpenShift コンソールに接続します。 または、次のコマンドを使用して OpenShift マスターに接続できます。

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して削除できます。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

- [デプロイ後タスク](./openshift-post-deployment.md)
- [Azure での OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
- [OpenShift Container Platform の概要](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
