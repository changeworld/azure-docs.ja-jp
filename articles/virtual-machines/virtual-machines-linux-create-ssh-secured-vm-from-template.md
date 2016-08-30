<properties
	pageTitle="Azure テンプレートを使用して Linux VM を作成する | Microsoft Azure"
	description="Azure Resource Manager テンプレートを使用して、Linux VM を Azure に作成します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/17/2016"
	ms.author="v-livech"/>

# Azure テンプレートを使用して Linux VM を作成する

この記事では、Azure テンプレートを使用してすばやく Linux 仮想マシンを Azure にデプロイする方法を紹介します。この記事では、Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) のほか、[Azure CLI](../xplat-cli-install.md) へのログイン (`azure login`) および Resource Manager モードの有効化 (`azure config mode arm`) が必要です。[Azure ポータル](virtual-machines-linux-quick-create-portal.md)または [Azure CLI](virtual-machines-linux-quick-create-cli.md) を使用して、すばやく Linux VM をデプロイすることもできます。

## クイック コマンドの概要

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## 詳細なチュートリアル

テンプレートを使用すると、ユーザー名やホスト名など、起動時にカスタマイズされる設定で VM を Azure に作成できます。この記事では、Ubuntu VM とネットワーク セキュリティ グループ (NSG) を使用する Azure テンプレートを紹介します。このテンプレートでは、ポート 22 が SSH 用に開かれています。

Azure Resource Manager テンプレートは、この記事で行う Ubuntu VM の起動のような、単純で 1 回限りのタスクに使用できる JSON ファイルです。Azure テンプレートは、テスト、開発、または運用デプロイ スタックのような、環境全体の複雑な Azure 構成の構築にも使用できます。

## Linux VM の作成

次のコードは、`azure group create` を呼び出してリソース グループを作成すると当時に、SSH で保護された Linux VM をデプロイする例です。[こちらの Azure Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)を使用しています。この例で使われている各種の名前は、実際の環境に合わせて調整する必要があります。この例では、リソース グループ名として `quicksecuretemplate` を、VM 名として `securelinux` を、サブドメイン名として `quicksecurelinux` を使用しています。

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

出力

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

この例では、`--template-uri` パラメーターを使用して VM をデプロイしています。また、テンプレートをローカルでダウンロードまたは作成し、`--template-file` パラメーターを使ってテンプレート ファイルへのパスを引数として指定して、そのテンプレートを渡すこともできます。テンプレートで必要となるパラメーターは、Azure CLI のプロンプトで入力します。

## 次のステップ

次にデプロイするアプリ フレームワークを見つけるには、[テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)を検索します。

<!---HONumber=AcomDC_0824_2016-->