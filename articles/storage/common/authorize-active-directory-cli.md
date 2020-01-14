---
title: Azure AD 資格情報で Azure CLI コマンドを実行して BLOB またはキューのデータにアクセスする
titleSuffix: Azure Storage
description: Azure CLI では、Azure AD 資格情報でサインインし、Azure Storage BLOB とキューのデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD セキュリティ プリンシパルに割り当てられた RBAC ロールによって異なります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553264"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 資格情報で Azure CLI コマンドを実行して BLOB またはキューのデータにアクセスする

Azure Storage には、Azure CLI のための拡張機能があります。この機能では、Azure Active Directory (Azure AD) 資格情報でサインインし、スクリプト コマンドを実行できます。 Azure AD 資格情報で Azure CLI にサインインすると、OAuth 2.0 アクセス トークンが返されます。 そのトークンが Azure CLI によって自動的に使用され、BLOB または Queue storage に対するその後のデータ操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

BLOB とキューのデータへのアクセス許可をロールベースのアクセス制御 (RBAC) を介して Azure AD セキュリティ プリンシパルに割り当てることができます。 Azure Storage の RBAC ロールの詳細については、[RBAC を使用した Azure ストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)」を参照してください。

## <a name="supported-operations"></a>サポート対象の操作

Azure Storage 拡張機能は、BLOB とキュー データの操作でサポートされています。 呼び出す操作は、Azure CLI にサインインする Azure AD セキュリティ プリンシパルに与えられているアクセス許可に依存します。 Azure Storage のコンテナーまたはキューのアクセス許可は、RBAC を介して割り当てられます。 たとえば、**BLOB データ閲覧者**ロールが割り当てられている場合、コンテナーまたはキューからデータを読み取るスクリプト コマンドを実行できます。 **BLOB データ共同作成者**ロールが割り当てられている場合、コンテナー、キュー、またはそれらに含まれているデータの読み取り、書き込み、削除を行うスクリプト コマンドを実行できます。

コンテナーまたはキューでの各 Azure Storage 操作に必要なアクセス許可の詳細については、「[Call storage operations with OAuth tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)」 (OAuth トークンを使用してストレージ操作を呼び出す) を参照してください。  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Azure AD サインイン情報を使用して Azure CLI コマンドを呼び出す

Azure CLI では、BLOB とキューのデータに対するデータ操作について `--auth-mode` パラメーターがサポートされています。

- Azure AD セキュリティ プリンシパルを利用してサインインするために、`--auth-mode` パラメーターを `login` に設定します。
- アカウントの認証パラメーターが与えられない場合、アカウント キーを問い合わせる目的で、`--auth-mode` パラメーターをレガシー `key` 値に設定します。

次の例では、Azure CLI から自分の Azure AD サインイン情報を使用して新しいストレージ アカウントにコンテナーを作成する方法を示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

1. Azure CLI バージョン 2.0.46 以降がインストールされていることを確認してください。 `az --version` を実行し、インストールされたバージョンを確認します。

1. ブラウザー ウィンドウで `az login` と認証を実行します。

    ```azurecli
    az login
    ```

1. 必要なサブスクリプションを指定します。 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) を使用してリソース グループを作成します。 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)を使用して、このリソース グループ内にストレージ アカウントを作成します。

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. コンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 RBAC ロールの割り当ての詳細については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac.md)」を参照してください。

    > [!IMPORTANT]
    > RBAC ロールの割り当ての反映には数分かかることがあります。

1. [az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) コマンドを、`--auth-mode` パラメーターに `login` を設定して呼び出し、自分の Azure AD サインイン情報を使用してコンテナーを作成します。

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode` パラメーターに関連付けられている環境変数は `AZURE_STORAGE_AUTH_MODE` です。 この環境変数に適切な値を指定すれば、Azure Storage データ操作を呼び出すたびにパラメーターに値を設定する必要はなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して、BLOB およびキュー データにアクセスするための RBAC ロールを割り当てる](storage-auth-aad-rbac-cli.md)
- [Azure リソースに対するマネージド ID を使用して BLOB およびキュー データへのアクセスを認証する](storage-auth-aad-msi.md)
