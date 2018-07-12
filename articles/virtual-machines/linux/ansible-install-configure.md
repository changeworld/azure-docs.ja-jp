---
title: Azure 仮想マシンで使用するための Ansible のインストールと構成 |Microsoft ドキュメント
description: Ubuntu、CentOS、SLES で Azure リソースを管理するため、Ansible をインストールし構成する方法を説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930071"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Azure の仮想マシンを管理するための Ansible のインストールと構成

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Ansible を使用すると、他のリソースと同じように、Azure で仮想マシン (VM) を管理できます。 この記事では、最も一般的な Linux ディストリビューションに Ansible と必須の Azure Python SDK モジュールをインストールする方法について説明します。 インストールしたパッケージを特定のプラットフォーム用に合わせることで、他のディストリビューションに Ansible をインストールできます。 Azure リソースを安全に作成するため、Ansible で使用する資格情報を作成し定義する方法についても説明します。

詳細なインストール オプションとプラットフォームを追加する手順については、[Ansible インストール ガイド](https://docs.ansible.com/ansible/intro_installation.html)に関するページを参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="install-ansible"></a>Ansible のインストール

Azure で Ansible を使用する最も簡単な方法の 1 つは、Azure リソースを開発および管理するためのブラウザー ベースのシェル エクスペリエンスである Azure Cloud Shell を使用することです。 Ansible は Cloud Shell に事前インストールされているため、Ansible をインストールする方法の手順をスキップし、[Azure 資格情報の作成](#create-azure-credentials)に進むことができます。 Cloud Shell で使用可能なその他のツールの一覧については、[Azure Cloud Shell の Bash の機能とツール](../../cloud-shell/features.md#tools)に関するページをご覧ください。

次の手順に、さまざまなディストリビューション用に Linux VM を作成してから、Ansible をインストールする方法を示します。 Linux VM を作成する必要がない場合は、この Azure リソース グループを作成する最初の手順をスキップしてください。 VM を作成する必要がない場合は、まず [az group create](/cli/azure/group#az_group_create) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

ここで、必要に応じて、VM を作成する方法の手順について、次のディストリビューションのいずれかを選択し、Ansible をインストールします。

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

必要に応じて、[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

必要に応じて、[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。

### <a name="sles-12-sp2"></a>SLES 12 SP2

必要に応じて、[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。

## <a name="create-azure-credentials"></a>Azure 資格情報の作成

Ansible は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、Ansible などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。 この例では、ユーザー名とパスワードを提供するだけでなく、セキュリティを強化するために、基本的なサービス プリンシパルを作成します。

ホスト コンピューターまたは Azure Cloud Shell で、[az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) を使用して、サービス プリンシパルを作成します。 Ansible に必要な資格情報が画面に出力されます。

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

上記のコマンドの出力例は、次のとおりです。

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure に対して認証するには、[az account show](/cli/azure/account#az-account-show) を使用して Azure サブスクリプション ID を取得する必要もあります。

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

次の手順で、これら 2 つのコマンドからの出力を使用します。

## <a name="create-ansible-credentials-file"></a>Ansible の資格情報ファイルの作成

Ansible に資格情報を提供するには、環境変数を定義するか、ローカル資格情報ファイルを作成します。 Ansible 資格情報を定義する方法の詳細については、「[Providing Credentials to Azure Modules (Azure モジュールに資格情報を提供する)](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)」を参照してください。

開発環境の場合は、ホスト VM 上に Ansible の*資格情報*ファイルを作成します。 前の手順で Ansible をインストールした VM に資格情報ファイルを作成します。

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*資格情報*ファイル自体では、サービス プリンシパル作成時の出力とサブスクリプション ID を組み合わせます。 前述の [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) コマンドからの出力は、*client_id*、*secret*、*tenant* に必要なものと同じです。 次の*資格情報*ファイル例は、これらの値が前述の出力と一致することを示しています。 実際の値を次のように入力します。

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

ファイルを保存して閉じます。

## <a name="use-ansible-environment-variables"></a>Ansible 環境変数の使用

Ansible Tower や Jenkins などのツールを使用する場合は、環境変数を定義する必要があります。 Ansible クライアントを使用するだけで、前の手順で Azure 資格情報ファイルが作成されている場合は、この手順をスキップできます。 環境変数は、Azure 資格情報ファイルと同じ情報を定義します。

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>次の手順

これで、Ansible と必須の Azure Python SDK モジュールがインストールされ、Ansible 用に定義された資格情報を取得できました。 [Ansible を使用して VM を作成する](ansible-create-vm.md)方法を説明します。 また、[完全な Azure VM を作成し、Ansible でリソースをサポートする](ansible-create-complete-vm.md)方法についても説明します。
