---
title: "Azure CLI を使用して、Azure リソースにユーザー割り当ての MSI アクセスを割り当てる方法"
description: "Azure CLI を使用して、1 つのリソースにユーザー割り当ての MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6e3bab5356812c256cfd147e42f065f381e0f63d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Azure CLI を使用してリソースにユーザー割り当ての管理対象サービス ID (MSI) アクセスを割り当てる

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

ユーザー割り当ての MSI で Azure リソースを作成したら、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この例では、Azure CLI を使用して、ユーザー割り当ての MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある [試してみる] ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 次に、[az login](/cli/azure/#az_login) を使用して Azure にサインインします。 ユーザー割り当て MSI と VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

ホスト リソース (VM など) を備えた MSI を使用するには、サインインまたはリソース アクセスのために、最初にロール割り当て経由で MSI がリソース アクセスに付与される必要があります。 これは、MSI をホストに関連付ける前または後に行うことができます。 VM を作成して関連付ける手順については、[Azure CLI を使用して、VM にユーザー割り当ての管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)手順に関する記事をご覧ください。

次の例では、ユーザー割り当ての MSI にストレージ アカウントへのアクセス許可を付与します。  

1. MSI アクセスを付与するには、MSI のサービス プリンシパルのクライアント ID (アプリ ID とも呼ばれる) が必要です。 クライアント ID は、MSI とそのサービス プリンシパルのプロビジョニング上で、[az identity create](/cli/azure/identity#az_identity_create) によって提供されます (参考までに、以下に示します)。

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   成功した応答では、`clientId` プロパティに、ユーザー割り当てによる MSI のサービス プリンシパルのクライアント ID が含まれています。

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. クライアント ID がわかったら、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用して別のリソースに MSI アクセスを割り当てます。 `--assignee` パラメーターには必ず、`az identity create` の実行時に戻されたサブスクリプション ID およびリソース グループ名と一致する お使いのクライアント ID を使用してください。 ここで、MSI は "myStorageAcct" と呼ばれるストレージ アカウントへの「閲覧者」アクセスに割り当てられています。

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   成功した応答は次の出力のようになります。

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>次の手順

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- Azure VM 上でユーザー割り当ての MSI を有効にするには、[Azure CLI を使用して、VM にユーザー割り当ての管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)手順に関する記事をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

