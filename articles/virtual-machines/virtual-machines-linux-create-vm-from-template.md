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
	ms.date="04/29/2016"
	ms.author="v-livech"/>

# Azure テンプレートを使用して Linux VM を作成する

この記事では、Azure テンプレートを使用してすばやく Linux 仮想マシンを Azure にデプロイする方法を紹介します。この記事では、Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) と、[Azure CLI](../xplat-cli-install.md) へのログイン (`azure login`) および Resource Manager モードの有効化 (`azure config mode arm`) が必要です。[Azure ポータル](virtual-machines-linux-quick-create-portal.md)または [Azure CLI](virtual-machines-linux-quick-create-cli.md) を使用してすばやく Linux VM をデプロイすることもできます。


## クイック コマンド

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## 詳細なチュートリアル

テンプレートを使用すると、ユーザー名やホスト名など、立ち上げ時に必要な設定をカスタマイズした VM を Azure に作成できます。この記事では、Azure テンプレートを使った Ubuntu VM の立ち上げについて説明しています。テンプレートで、SSH 用にポート (22) を 1 つだけ開放したネットワーク セキュリティ グループ (NSG) を作成し、SSH キーを使ったログインを指定します。

Azure Resource Manager テンプレートは JSON ファイルになっており、この記事で行う Ubuntu VM の立ち上げなど単純な 1 回限りのタスクだけでなく、ネットワークから OS、アプリケーション スタックのデプロイまでを含めた環境全体 (テスト環境、開発環境、運用環境など) の複雑な Azure 構成を構築する際にも使用できます。

## Linux VM の作成

次のコードは、`azure group create` を呼び出してリソース グループを作成すると共に、SSH で保護された Linux VM をデプロイする例です。[こちらの Azure Resource Manager テンプレート](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)を使用しています。この例で使われている各種の名前は、実際の環境に合わせて調整する必要があります。この例では、リソース グループ名として `quicksecuretemplate` を、VM 名として `securelinux` を、サブドメイン名として `quicksecurelinux` を使用しています。

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

`--template-uri` パラメーターを使って新しいリソース グループを作成し、VM をデプロイする以外にも、テンプレートをローカルでダウンロードまたは作成し、`--template-file` パラメーターを使ってテンプレート ファイルへのパスを引数として指定して、そのテンプレートを渡すこともできます。テンプレートで必要となるパラメーターは、Azure CLI のプロンプトで入力します。

## 次のステップ

テンプレートを使って Linux VM を作成したら、テンプレートでデプロイすることのできる他のアプリケーション フレームワークをぜひご覧ください。次にデプロイするアプリケーション フレームワークを見つけるには、[テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)を検索します。

<!---HONumber=AcomDC_0504_2016-->