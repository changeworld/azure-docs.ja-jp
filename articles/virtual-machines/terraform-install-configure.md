---
title: "VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成 | Microsoft Docs"
description: "Azure リソースを作成するための Terraform のインストールと構成"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成 
この記事では、仮想マシンなどのリソースを Azure にプロビジョニングする Terraform のインストールと構成に必要な手順について詳しく説明します。 Azure の資格情報を作成し、その資格情報を使ってクラウド リソースを Terraform で安全にプロビジョニングする方法について説明します。

HashiCorp の Terraform は、HCL と呼ばれるカスタム テンプレート言語を使ってクラウド インフラストラクチャを簡単に定義してデプロイできるツールです。 このカスタム言語は、[記述しやすく理解しやすい](terraform-create-complete-vm.md)のが特徴です。 また、Terraform では、インフラストラクチャに対する変更を "terraform plan" というコマンドで事前に視覚化することができます。 以下の手順に従って、実際に Terraform を使った Azure のプロビジョニングを始めましょう。

## <a name="installing-terraform"></a>Terraform のインストール
Terraform をインストールするには、ご利用の OS に適したパッケージを別個のインストール ディレクトリに[ダウンロード](https://www.terraform.io/downloads.html)します。 ダウンロードには実行可能ファイルが 1 つ含まれており、そのファイルのグローバル PATH を定義する必要があります。 Linux と Mac で PATH を設定する手順については[こちらのページ](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)を、Windows で PATH を設定する手順については[こちらのページ](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)を参照してください。 インストール結果を確認するために、"terraform" コマンドを実行します。正しくインストールされていれば、利用できる Terraform オプションが出力として一覧表示されます。

次に、Azure サブスクリプションにアクセスしてインフラストラクチャのプロビジョニングを行うための許可を Terraform に付与する必要があります。

## <a name="setting-up-terraform-access-to-azure"></a>Terraform から Azure にアクセスするための設定
Terraform で Azure にリソースをプロビジョニングするには、Azure Active Directory (AAD) に AAD アプリケーションと AAD サービス プリンシパルの 2 つのエンティティを作成する必要があります。 Terraform スクリプトでは、それらのエンティティの ID を使用することになります。 サービス プリンシパルは、グローバルな AAD アプリケーションのローカル インスタンスです。 サービス プリンシパルを利用することで、グローバル リソースに対する粒度の細かいローカル アクセス制御が可能になります。

AAD アプリケーションと AAD サービス プリンシパルは、いくつかの方法で作成できます。 現在最も簡単で時間のかからない方法は、Azure CLI 2.0 の使用です。[こちらからダウンロードして Windows、Linux、Mac にインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)することができます。 PowerShell または Azure CLI 1.0 を使用して必要なセキュリティ インフラストラクチャを作成することもできます。 以降の手順では、これらすべてのアプローチを使用して Terraform の Azure 対応化を構成する方法について紹介しています。

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Azure CLI 2.0 を使用する Windows/Linux/Mac ユーザー
[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) をダウンロードしてインストールした後、次のコマンドを実行してログインし、Azure サブスクリプションの管理を行います。

```
az login
```

>[!NOTE]
>Azure China、Azure Germany、Azure Government のいずれかのクラウドを使用する場合は、まずそのクラウドと連携するための構成を Azure CLI に対して行う必要があります。 次のコマンドを実行してください。

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

複数の Azure サブスクリプションをご利用の場合、その詳細が `az login` コマンドから返されます。 使用するサブスクリプションから返された `id` フィールドの値を `SUBSCRIPTION_ID` 環境変数に設定します。 

このセッションで使用するサブスクリプションを設定します。

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

アカウントを照会してサブスクリプション ID とテナント ID の値を取得します。

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

次に、Terraform 用に別個の資格情報を作成します。

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

このコマンドからは、client_id、client_secret (パスワード)、sp_name、tenant が出力されます。 **client_id** と **client_secret** を書き留めておきます。

新しいシェルを開き、次のコマンドを実行すると資格情報 (サービス プリンシパル) を確認できます。**sp_name**、**client_secret**、**tenant** は、実際に返された値に置き換えてください。

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>PowerShell を使用する Windows ユーザー
Terraform スクリプトの作成と実行に Windows マシンを使用していて、PowerShell を使用した構成タスクを希望される方は、適切な PowerShell ツールでマシンを構成する必要があります。 そのためには、(1) [Azure PowerShell ツール](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)をインストールし、(2) [azure-setup.ps1 スクリプト](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)をダウンロードして PowerShell コンソールから実行します。 azure-setup.ps1 スクリプトを実行するには、スクリプトをダウンロードして PowerShell コンソールから「./azure-setup.ps1 setup」コマンドを実行し、Azure サブスクリプションに管理特権でログインします。 次に、アプリケーションの名前を求められたら任意の文字列 (必須) を指定し、強力なパスワード (省略可) を指定します。 パスワードを指定しなかった場合は、.NET セキュリティ ライブラリを使用して強力なパスワードが自動的に生成されます。

### <a name="linuxmac-users-using-azure-cli-10"></a>Azure CLI 1.0 を使用する Linux/Mac ユーザー
Linux マシンまたは Mac で Terraform と Azure CLI 1.0 を使用する場合、ご利用のマシンに適切なライブラリがインストールされている必要があります。 そのためには、(1) [Azure xPlat CLI ツールをインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)し、(2) JSON プロセッサ [jq をダウンロード、インストール](https://stedolan.github.io/jq/download/)して、(3) [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) bash スクリプトをコンソールからダウンロード、実行します。 azure-setup.sh スクリプトを実行するには、スクリプトをダウンロードしてコンソールから「./azure-setup setup」コマンドを実行し、Azure サブスクリプションに管理特権でログインします。 次に、アプリケーションの名前を求められたら任意の文字列 (必須) を指定し、強力なパスワード (省略可) を指定します。 パスワードを指定しなかった場合は、.NET セキュリティ ライブラリを使用して強力なパスワードが自動的に生成されます。

以上の全スクリプトによって AAD アプリケーションとサービス プリンシパルが作成され、サブスクリプションに対する共同作成者レベルまたは所有者レベルのアクセス権がサービス プリンシパルに付与されます。 高レベルのアクセス権が付与されるため、これらのスクリプトによって生成されるセキュリティ情報は必ず保護する必要があります。 これらのスクリプトから得られる 4 つのセキュリティ情報 (client_id、client_secret、subscription_id、tenant_id) はすべて書き留めておいてください。 

## <a name="setting-environment-variables"></a>環境変数の設定
AAD サービス プリンシパルの作成と構成が済んだら、使用するテナント ID、サブスクリプション ID、クライアント ID、クライアント シークレットを Terraform に伝える必要があります。 [次のセクション](terraform-create-complete-vm.md)で説明する方法に従って、これらの値を Terraform スクリプトに埋め込んでください。 または、次の環境変数を設定することもできます。環境変数を使用すれば、不注意で資格情報をチェックイン/共有することを防止できます。

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

これらの変数は、次のサンプル シェル スクリプトを使用して設定できます。

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

加えて、Azure Government、Azure Germany、Azure China のいずれかと組み合わせて Terraform を使用する場合は、ENVIRONMENT 変数を適切に設定する必要があります。

## <a name="next-steps"></a>次のステップ
以上で Terraform のインストールと Azure 資格情報の構成は完了です。Azure サブスクリプションへのインフラストラクチャのデプロイを開始できます。 次は、[Terraform によるインフラストラクチャの作成](terraform-create-complete-vm.md)方法について学習します。
