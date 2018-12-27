---
title: REST を使用して Azure ユーザー割り当てマネージド ID を管理する方法
description: REST API 呼び出すためのユーザー割り当てマネージド ID を作成、一覧表示、削除する詳細な手順。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 4bf77cd34ba985dfcfa568db0543150c0510c406
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300100"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>REST API 呼び出しを使用して、ユーザー割り当てマネージド ID を作成、一覧表示、削除する

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure リソースのマネージド ID を使用すれば、Azure サービスで Azure AD Authentication をサポートするサービスを認証することができます。この場合、コードに資格情報を挿入する必要はありません。 

この記事では、CURL を使用して REST API を呼び出し、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) をインストールするか、Azure Portal で [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。
- [Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) または [Linux ディストリビューション OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) を使用する場合は、[Azure CLI ローカル コンソール](/cli/azure/install-azure-cli)をインストールします。
- Azure CLI ローカル コンソールを使用している場合は、ユーザー割り当てマネージド ID の情報をデプロイまたは取得する Azure サブスクリプションに関連付けられているアカウントで `az login` を使用して Azure にサインインします。
- 以下のユーザー割り当てマネージド ID の操作を実行するには、`az account get-access-token` を使用して Bearer アクセス トークンを取得します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、Azure Resource Manager API への次の CURL 要求を使用します。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<USER ASSIGNED IDENTITY NAME>`、`<LOCATION>`、`<ACCESS TOKEN>` の各値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID の一覧表示/読み取りには、お使いのアカウントに[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator)ロールまたは[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、Azure Resource Manager API への次の CURL 要求を使用します。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<ACCESS TOKEN>` の各値は、実際の値に置き換えます。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を削除するには、Azure Resource Manager API への次の CURL 要求を使用します。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<ACCESS TOKEN>` の各パラメーター値は、実際の値に置き換えます。

> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 CURL を使用して VM からユーザー割り当てマネージド ID を削除する場合は、「[Azure VM からユーザー割り当て ID を削除する](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)」を参照してください。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>次の手順

CURL を使用して Azure VM/VMSS にユーザー割り当てマネージド ID を割り当てる方法については、[REST API 呼び出しを使用する Azure VM での Azure リソースのマネージド ID の構成](qs-configure-rest-vm.md#user-assigned-managed-identity)と [REST API 呼び出しを使用する仮想マシン スケール セットでの Azure リソースのマネージド ID の構成](qs-configure-rest-vmss.md#user-assigned-managed-identity)に関するページを参照してください。


