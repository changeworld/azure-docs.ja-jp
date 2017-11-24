---
title: "Azure に OpenShift Origin をデプロイする| Microsoft Docs"
description: "Azure に OpenShift Origin をデプロイします。"
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
ms.openlocfilehash: 1860ede19202566947b68b715e6bd354f64c1085
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Azure に OpenShift Origin をデプロイする

OpenShift Origin を Azure にデプロイするには、2 つの方法のいずれかを使用できます。

- 必要なすべての Azure インフラストラクチャ コンポーネントを手動でデプロイしてから、OpenShift Container Origin の[ドキュメント](https://docs.openshift.org/3.6/welcome/index.html)に従ってください。
- また、既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-origin)を使用して、OpenShift Origin クラスターのデプロイを簡略化することもできます。

## <a name="deploy-by-using-the-openshift-origin-template"></a>OpenShift Origin テンプレートを使用したデプロイ

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
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

次の例では、OpenShift クラスターとすべての関連リソースを myResourceGroup という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルを使用して GitHub リポジトリから直接参照されます。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数に応じて、このデプロイが完了するには少なくとも 25 分かかります。 デプロイが完了すると、OpenShift コンソールの URL と OpenShift マスターの DNS 名がターミナルに出力されます。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、ブラウザーで `OpenShift Console Uri` を使用して、OpenShift コンソールに接続します。 または、次のコマンドを使用して OpenShift マスターに接続できます。

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group#delete) コマンドを使用して削除できます。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- [デプロイ後タスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
- [OpenShift Origin の概要](https://docs.openshift.org/latest/getting_started/index.html)
