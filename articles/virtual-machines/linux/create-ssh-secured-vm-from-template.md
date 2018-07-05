---
title: Azure でテンプレートから Linux VM を作成する | Microsoft Docs
description: Azure CLI を使用して Resource Manager テンプレートから Linux VM を作成する方法
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc2b4dde9796336112d6c8a68d16d0b3006b3fee
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936416"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Linux 仮想マシンを作成する方法
この記事では、Azure Resource Manager テンプレートと Azure CLI を使用してすばやく Linux 仮想マシン (VM) をデプロイする方法を説明します。 


## <a name="templates-overview"></a>テンプレートの概要
Azure Resource Manager テンプレートとは、Azure ソリューションのインフラストラクチャと構成を定義する JSON ファイルです。 テンプレートを使えば、ソリューションをそのライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。 テンプレートの形式とその構築方法については、「[初めての Azure Resource Manager テンプレートを作成する](../../azure-resource-manager/resource-manager-create-first-template.md)」を参照してください。 リソースの種類に関して JSON 構文を確認するには、「[Define resources in Azure Resource Manager templates (Azure Resource Manager テンプレートのリソースの定義)](/azure/templates/)」を参照してください。


## <a name="create-a-resource-group"></a>リソース グループの作成
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 次の例では、*myResourceGroupVM* という名前のリソース グループを *eastus* リージョンに作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
次の例では、[この Azure Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) から [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) を使用して VM を作成します。 SSH 認証のみが許可されています。 値を求められたら、自らの SSH 公開キーの値 (*~/.ssh/id_rsa.pub* の内容など) を指定します。 SSH キー ペアを短時間で作成する必要がある場合は、[Azure に Linux VM 用の SSH キー ペアを作成して使用する方法](mac-create-ssh-keys.md)に関する記事をご覧ください。

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

前の例では、GitHub に格納されているテンプレートが指定されています。 テンプレートをダウンロードまたは作成し、`--template-file` パラメーターを使用してローカル パスを指定することもできます。


## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続
VM に SSH 接続するには、[az vm show](/cli/azure/vm#az-vm-show) でパブリック IP アドレスを取得します。

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

通常どおりに、ご使用の VM に SSH 接続できます。 上記のコマンドで取得した独自のパブリック IP アドレスを指定します。

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>次の手順
この例では、基本的な Linux VM を作成しました。 アプリケーション フレームワークを含むその他の Resource Manager テンプレートについて、またはさらに複雑な環境の作成方法については、[Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)をご覧ください。