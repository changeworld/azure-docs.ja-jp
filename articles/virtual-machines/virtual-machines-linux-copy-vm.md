---
title: Azure Linux VM のコピーの作成 | Microsoft Docs
description: Resource Manager デプロイ モデルで Azure Linux 仮想マシンのコピーを作成する方法を説明します
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Azure で実行されている Linux 仮想マシンのコピーを作成する
この記事では、Resource Manager デプロイ モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを 作成する方法について説明します。はじめにオペレーティング システムとデータ ディスクを新しいコンテナーにコピーし、次にネットワーク リソースをセットアップして、新しい仮想マシンを作成します。

[カスタム ディスク イメージをアップロードして VM を作成する](virtual-machines-linux-upload-vhd.md)こともできます。

## 開始する前に
手順を開始する前に、次の前提条件が満たされていることを確認します。

* コンピューターに [Azure CLI](../xplat-cli-install.md) をダウンロードしてインストールしてある必要があります。
* 既存の Azure Linux VM について次の情報も必要です。

| ソース VM 情報 | 情報の入手元 |
| --- | --- |
| VM 名 |`azure vm list` |
| リソース グループ名 |`azure vm list` |
| Location (場所) |`azure vm list` |
| ストレージ アカウント名 |`azure storage account list -g <resourceGroup>` |
| コンテナー名 |`azure storage container list -a <sourcestorageaccountname>` |
| ソース VM の VHD ファイル名 |`azure storage blob list --container <containerName>` |

* 新しい VM について次の項目を選択する必要があります。<br> - コンテナー名<br> - VM 名<br> - VM サイズ<br> - vNet 名<br> - サブネット名<br> - IP 名 <br> - NIC 名

## ログインしてサブスクリプションを設定する
1. CLI にログインします。
   
        azure login
2. リソース マネージャー モードになっていることを確認します。
   
        azure config mode arm
3. 現在のサブスクリプションを設定します。"azure account list" を使用すると、すべてのサブスクリプションを表示することができます。
   
        azure account set <SubscriptionId>

## VM を停止する
ソース VM を停止して割り当てを解除します。"azure vm list" を使用すると、サブスクリプション内のすべての VM と、そのリソース グループ名の一覧を取得できます。

        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## VHD をコピーする
`azure storage blob copy start` を使用して、ソース ストレージからターゲットに VHD をコピーできます。この例では、同じストレージ アカウントの別のコンテナーに VHD をコピーします。

VHD を同じストレージ アカウントの別のコンテナーにコピーするには、次のように入力します。

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>


## 新しい VM の仮想ネットワークを設定する
新しい VM の仮想ネットワークと NIC を設定します。

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## 新しい VM を作成する
これでアップロード済みの仮想ディスクから VM を作成することができます。VM を作成するには、[Resource Manager テンプレートを使用する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)か、次のように入力して CLI からコピーしたディスクの URI を指定します。

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## 次のステップ
Azure CLI を使用して新しい仮想マシンを管理する方法については、[Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0803_2016-->