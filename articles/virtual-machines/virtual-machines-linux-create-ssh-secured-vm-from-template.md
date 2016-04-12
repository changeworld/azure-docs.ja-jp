<properties
	pageTitle="Azure テンプレートを使用して安全な Linux VM を作成する | Microsoft Azure"
	description="Azure Resource Manager テンプレートを使用して、安全な Linux VM を Azure に作成します。"
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
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Azure テンプレートを使用して安全な Linux VM を作成する

テンプレートを使用すると、ユーザー名やホスト名など、立ち上げ時に必要な設定をカスタマイズした VM を Azure に作成できます。この記事では、Azure テンプレートを使った Ubuntu VM の立ち上げについて説明しています。テンプレートで、SSH 用にポート (22) を 1 つだけ開放したネットワーク セキュリティ グループ (NSG) を作成し、SSH キーを使ったログインを指定します。

Azure Resource Manager テンプレートは JSON ファイルになっており、この記事で行う Ubuntu VM の立ち上げなど単純な 1 回限りのタスクだけでなく、ネットワークから OS、アプリケーション スタックのデプロイまでを含めた環境全体 (テスト環境、開発環境、運用環境など) の複雑な Azure 構成を構築する際にも使用できます。

## 目標

- Azure テンプレートを使用して Azure に SSH で保護された Linux VM を作成する

## 前提条件

- Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/))、[SSH 公開キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)、[Azure CLI](../xplat-cli-install.md) が必要となります。「`azure config mode arm`」と入力して CLI をリソース モードにしてください。そのうえで、CLI から「`azure login`」と入力して Azure にログインします。

## コマンドの概要

このデプロイで必要なコマンドは 1 つだけです。Azure Resource Manager テンプレートが存在する場所をオプションで指定します。このトピックでは、Azure クイック スタート テンプレート Github リポジトリから入手したテンプレートをそのまま使用します。以下に示した各種オプションは単に、テンプレート (とパラメーター ファイル) がどこにあるかを指定するものです。

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Linux VM の作成

次のコードは、`azure group create` を呼び出してリソース グループを作成すると共に、SSH で保護された Linux VM をデプロイする例です。[こちらの Azure Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)を使用しています。この例で使われている各種の名前は、実際の環境に合わせて調整する必要があります。この例では、リソース グループ名として `quicksecuretemplate` を、VM の名前として `securelinux` を、サブドメイン名として `quicksecurelinux` を使用しています。

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## 詳しいチュートリアル

`--template-uri` パラメーターを使用して新しいリソース グループを作成し、VM をデプロイする以外にも、テンプレートをローカルでダウンロードまたは作成し、`--template-file` パラメーターを使ってそのテンプレートを渡す (テンプレート ファイルへのパスを引数として渡す) こともできます。テンプレートで必要となるパラメーターは、Azure CLI のプロンプトで入力します。

## 次のステップ

テンプレートを使って Linux VM を作成したら、テンプレートで扱うことのできる他のアプリケーション フレームワークをぜひご覧ください。[テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)から、次にデプロイするアプリケーション フレームワークを探しましょう。

<!---HONumber=AcomDC_0406_2016-->