---
title: Azure での OpenShift の前提条件 | Microsoft Docs
description: Azure で OpenShift をデプロイするための前提条件を説明します。
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
ms.openlocfilehash: 36271116d697e5ee6c6ed08d5fdc6063a511e820
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984339"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Azure で OpenShift をデプロイする場合の共通の前提条件

この記事では、Azure で OpenShift Origin または OpenShift Container Platform をデプロイする場合の共通の前提条件について説明します。

OpenShift のインストールには Ansible プレイブックを使用します。 Ansible は、Secure Shell (SSH) を使用してすべてのクラスター ホストに接続し、インストール手順を完了します。

リモート ホストへの SSH 接続を開始すると、パスワードを入力できません。 このため、秘密キーにパスワードを関連付けることができないか、デプロイが失敗します。

仮想マシン (VM) は Azure Resource Manager テンプレートを通じてデプロイされるため、すべての VM へのアクセスに同じ公開キーが使用されます。 また、対応する秘密キーを、すべてのプレイブックを実行する VM に挿入する必要もあります。 この操作を安全に行うには、Azure キー コンテナーを使用して秘密キーを VM に渡します。

コンテナーに永続的ストレージが必要であれば、永続ボリュームが必要になります。 OpenShift では、この機能のための Azure 仮想ハード ディスク (VHD) がサポートされていますが、まず Azure をクラウド プロバイダーとして構成する必要があります。 

このモデルでは、OpenShift で以下の処理が実行されます。

- Azure ストレージ アカウントで VHD オブジェクトを作成します。
- VHD を VM にマウントし、ボリュームをフォーマットします。
- ボリュームをポッドにマウントします。

この構成が機能するには、Azure で前のタスクを実行できるアクセス許可が OpenShift に必要です。 そのためには、サービス プリンシパルが必要です。 サービス プリンシパルは、リソースへのアクセス許可が付与された Azure Active Directory でのセキュリティ アカウントです。

サービス プリンシパルには、ストレージ アカウントと、クラスターを構成する VM へのアクセスが必要です。 OpenShift クラスターのすべてのリソースを 1 つのリソース グループにデプロイした場合、サービス プリンシパルにそのリソース グループに対するアクセス許可を付与できます。

このガイドでは、成果物を作成する方法を前提条件と関連付けて説明します。

> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理するキー コンテナーを作成する。
> * Azure Cloud Solution Provider で使用するためのサービス プリンシパルを作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 
[az login](/cli/azure/reference-index#az_login) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従うか、**[使ってみる]** をクリックして Cloud Shell を使用します。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 キー コンテナーのホストには、専用のリソース グループを使用します。 このグループは、OpenShift クラスター リソースがデプロイするリソース グループとは別です。 

次の例では、*keyvaultrg* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault を作成します
[az keyvault create](/cli/azure/keyvault#az_keyvault_create) コマンドを使用して、クラスターの SSH キーを格納するキー コンテナーを作成します。 キー コンテナーの名前はグローバルに一意である必要があります。

次の例では、*keyvault* という名前のキー コンテナーを *keyvaultrg* リソース グループに作成します。

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH キーの作成 
SSH キーは、OpenShift Origin クラスターへのアクセスをセキュリティで保護するために必要です。 `ssh-keygen` コマンドを使用して、SSH キーペアを作成します (Linux または macOS 上)。
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH キー ペアにパスワードを指定することはできません。

Windows の SSH キーの詳細については、「[Azure 上の Windows で SSH キーを使用する方法](/azure/virtual-machines/linux/ssh-from-windows)」を参照してください。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Azure Key Vault に SSH 秘密キーを格納する
OpenShift のデプロイでは、OpenShift マスターに安全にアクセスするために作成した SSH キーが使用されます。 SSH キーを安全に取得するようにデプロイを有効にするには、次のコマンドを使用してキーを Key Vault に格納します。

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 
OpenShift は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、OpenShift などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。 この例では、ユーザー名とパスワードを提供するだけでなく、セキュリティを強化するために、基本的なサービス プリンシパルを作成します。

[az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) を使用してサービス プリンシパルを作成し、OpenShift が必要とする資格情報を出力します。

次の例では、サービス プリンシパルを作成して、共同作成者のアクセス許可を myResourceGroup という名前のリソース グループに割り当てます。 Windows を使用する場合、```az group show --name myResourceGroup --query id``` を別途実行し、出力を使用して --scopes オプションをフィードします。

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

コマンドから返された appId プロパティを書き留めます。
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 必ず安全なパスワードを作成してください。 [Azure AD のパスワードの規則と制限](/azure/active-directory/active-directory-passwords-policy)に関するガイダンスに従ってください。

サービス プリンシパルについて詳しくは、「[Azure CLI で Azure サービス プリンシパルを作成する](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)」をご覧ください。

## <a name="next-steps"></a>次の手順

この記事では、次のトピックについて説明しました。
> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理するキー コンテナーを作成する。
> * Azure Cloud Solution Provider で使用するためのサービス プリンシパルを作成します。

次に、OpenShift クラスターをデプロイしてみましょう。

- [OpenShift Origin のデプロイ](./openshift-origin.md)
- [OpenShift Container Platform のデプロイ](./openshift-container-platform.md)

