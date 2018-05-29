---
title: Azure CLI を使用して、ユーザーによって割り当てられた管理対象サービス ID (MSI) を管理する方法
description: Azure CLI を使用して、ユーザーによって割り当てられた管理対象サービス ID (MSI) の作成、一覧表示、削除する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930668"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Azure CLI を使用して、ユーザー割り当て ID を作成、一覧表示、削除する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID は、Azure Active Directory の管理対象 ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure CLI を使用して、ユーザー割り当て ID を作成、一覧表示、削除する方法を学びます。

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を見直すようにしてください。**
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。

    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョンをインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 以降) 。 ユーザー割り当て ID をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用して、Azure に `az login` でサインインします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当て管理対象 ID を作成する 

ユーザー割り当て ID を作成するには、[az identity create](/cli/azure/identity#az-identity-create) コマンドを使用します。 `-g` パラメーターはユーザー割り当て ID が作成されるリソース グループを指定し、`-n` パラメーターはその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値を、実際の値に置き換えます。

> [!IMPORTANT]
> ユーザー割り当て ID の作成には、英数字およびハイフン (0-9、a-z、A-Z、-) 文字のみがサポートされます。 さらに、VM/VMSS への割り当てが適切に動作するためには、名前は 24 文字の長さに制限する必要があります。 アップデートは後ほどご確認ください。 詳細については、[FAQ と既知の問題](known-issues.md)をご覧ください。

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>ユーザー割り当て ID を一覧表示する

ユーザー割り当て ID を一覧表示するには、[az identity list](/cli/azure/identity#az-identity-list) コマンドを使用します。  `-g` パラメーターは、ユーザー割り当て ID が作成されたリソース グループを指定します。  `<RESOURCE GROUP>` を実際の値に置き換えます。

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
json 応答で、ユーザー ID はキー `type` に対して `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値を返します。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>ユーザー割り当て ID を削除する

ユーザー割り当て ID を削除するには、[az identity delete](/cli/azure/identity#az-identity-delete) コマンドを使用します。  -n パラメーターはその名前を指定し、-g パラメーターはユーザー割り当て ID が作成されたリソース グループを指定します。  `<USER ASSIGNED IDENTITY NAME>` と `<RESOURCE GROUP>` のパラメーターの値を、実際の値に置き換えます。

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> ユーザー割り当て ID の削除は、それが割り当てられていたすべてのリソースからの参照を削除しません。 それらを VM/VMSS から削除するには、`az vm/vmss identity remove` コマンドを使用します。

## <a name="related-content"></a>関連コンテンツ

Azure CLI の ID コマンドの一覧は、[az identity](/cli/azure/identity) をご覧ください。

Azure VM にユーザー割り当て ID を割り当てる方法については、[Azure CLI を使用して管理対象サービス ID (MSI) を構成する](qs-configure-cli-windows-vm.md#user-assigned-identity)をご覧ください。


 
