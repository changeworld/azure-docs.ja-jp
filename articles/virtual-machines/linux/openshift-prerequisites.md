---
title: "Azure での OpenShift の前提条件 | Microsoft Docs"
description: "Azure で OpenShift をデプロイするための前提条件を説明します。"
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
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Azure での OpenShift の共通の前提条件

Azure で OpenShift をデプロイする際、OpenShift Origin または OpenShift Container Platform のいずれをデプロイする場合にも共通の前提条件がいくつかあります。

OpenShift のインストールは Ansible プレイブック経由で実行されます。 Ansible は、インストール手順を完了するために SSH を使用して、クラスターの一部となるすべてのホストに接続します。
リモート ホストへの SSH 接続が開始されると、パスフレーズを入力する方法がなくなります。 このため、秘密キーにパスフレーズを関連付けることができないか、デプロイが失敗します。
すべての VM は Resource Manager テンプレートを通じてデプロイされるため、すべての VM へのアクセスに同じ公開キーが使用されます。 また、対応する秘密キーを、すべてのプレイブックを実行する VM に挿入する必要もあります。
これを安全に行うには、Azure Key Vault を使用して秘密キーを VM に渡します。

コンテナーに永続的ストレージが必要であれば、永続ボリューム (PV) が必要になります。 これらの PV は、何らかの形式の永続的ストレージによってバックアップする必要があります。 OpenShift では、この機能のための Azure ディスク (VHD) がサポートされていますが、まず Azure をクラウド プロバイダーとして構成する必要があります。 このモデルでは、OpenShift により以下が行われます。

- Azure ストレージ アカウントでの VHD オブジェクトの作成
- VHD の VM へのマウントおよびボリュームのフォーマット
- ボリュームのポッドへのマウント

これを機能させるために、OpenShift には Azure で前のタスクを実行するためのアクセス許可が必要です。 そのためには、サービス プリンシパルが必要です。 サービス プリンシパル (SP) は、リソースへのアクセス許可が付与された Azure Active Directory でのセキュリティ アカウントです。
サービス プリンシパルには、ストレージ アカウントと、クラスターを構成する VM へのアクセスが必要です。 OpenShift クラスターのすべてのリソースを 1 つのリソース グループにデプロイした場合、SP にそのリソース グループに対するアクセス許可を付与できます。

このガイドでは、成果物を作成する方法を前提条件と関連付けて説明します。

> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理する KeyVault を作成する。
> * Azure クラウド プロバイダーで使用するためのサービス プリンシパルを作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン 
[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従うか、**[使ってみる]** をクリックして Cloud Shell を使用します。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 Key Vault のホスト用に専用のリソース グループ (OpenShift クラスター リソースのデプロイ先のリソース グループとは別の) を使用することをお勧めします。 

次の例では、*keyvaultrg* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault を作成します
[az keyvault create](/cli/azure/keyvault#create) コマンドを使用して、クラスターの SSH キーを格納する KeyVault を作成します。 Key Vault の名前はグローバルに一意である必要があります。

次の例では、*keyvault* という名前の keyvault を *keyvaultrg* リソース グループに作成します。

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH キーの作成 
SSH キーは、OpenShift Origin クラスターへのアクセスをセキュリティで保護するために必要です。 `ssh-keygen` コマンドを使用して、SSH キーペアを作成します (Linux または Mac 上)。
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 作成する SSH キー ペアには、パスフレーズは使用しないでください。

Windows の SSH キーの詳細については、「[Azure 上の Windows で SSH キーを使用する方法](/azure/virtual-machines/linux/ssh-from-windows)」を参照してください。

## <a name="store-ssh-private-key-in-key-vault"></a>キー コンテナーに SSH 秘密キーを格納する
OpenShift のデプロイでは、OpenShift マスターに安全にアクセスするために作成した SSH キーが使用されます。 SSH キーを安全に取得するようにデプロイを有効にするには、次のコマンドを使用してキーを Key Vault に格納します。

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 
OpenShift は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、OpenShift などのオートメーション ツールで使用できるセキュリティ ID です。 Azure で実行できる操作やサービス プリンシパルを設定するアクセス許可をコントロールおよび定義します。 この例では、ユーザー名とパスワードを提供するだけでなく、セキュリティを強化するために、基本的なサービス プリンシパルを作成します。

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) を使用してサービス プリンシパルを作成し、OpenShift が必要とする資格情報を出力します。

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
 > 安全でないパスワードを作成しないでください。  [Azure AD のパスワードの規則と制限](/azure/active-directory/active-directory-passwords-policy)に関するガイダンスに従ってください。

サービス プリンシパルの詳細については、「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、次のトピックについて説明しました。
> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理する KeyVault を作成する。
> * Azure クラウド プロバイダーで使用するためのサービス プリンシパルを作成します。

次に、OpenShift クラスターをデプロイしてみましょう。

- [OpenShift Origin のデプロイ](./openshift-origin.md)
- [OpenShift Container Platform のデプロイ](./openshift-container-platform.md)

