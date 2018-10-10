---
title: Azure で OKD をデプロイする | Microsoft Docs
description: Azure で OKD をデプロイします。
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
ms.openlocfilehash: 75a02e61adf3e5477b9945afc778e867d5d9c88c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958137"
---
# <a name="deploy-okd-in-azure"></a>Azure で OKD をデプロイする

Azure で OKD (旧称 OpenShift Origin) をデプロイするには、2 つの方法のいずれかを使用できます。

- 必要なすべての Azure インフラストラクチャ コンポーネントを手動でデプロイしてから、OKD の[ドキュメント](https://docs.okd.io/3.10/welcome/index.html)に従います。
- 既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-origin)を使用して、OKD クラスターのデプロイを簡略化することもできます。

## <a name="deploy-by-using-the-okd-template"></a>OKD テンプレートを使用してデプロイする

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

### <a name="deploy-by-using-azure-cli"></a>Azure CLI を使用したデプロイ


> [!NOTE] 
> 次のコマンドを実行するには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

次の例では、OKD クラスターとすべての関連リソースを myResourceGroup という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルを使用して GitHub リポジトリから直接参照されます。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数に応じて、このデプロイが完了するには少なくとも 25 分かかります。 デプロイが完了すると、OKD コンソールの URL と OpenShift マスターの DNS 名がターミナルに出力されます。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>OKD クラスターに接続する

デプロイが完了したら、ブラウザーで `OpenShift Console Uri` を使用して、OKD コンソールに接続します。 または、次のコマンドを使用して OKD マスターに接続できます。

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して削除できます。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

- [デプロイ後タスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
- [OKD の概要](https://docs.okd.io/latest/getting_started/index.html)
