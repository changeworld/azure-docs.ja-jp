---
title: Azure AD ID で Azure CLI または PowerShell コマンドを実行し、Azure Storage にアクセスする (プレビュー) | Microsoft Docs
description: Azure CLI と PowerShell では、Azure AD ID でログインし、Azure Storage コンテナー、キュー、そのデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD ID に割り当てられた役割によって異なります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: b5a129c2a92c18b979a3b0c2eeea7fa19791551c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633767"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)

Azure Storage には、Azure CLI と PowerShell のためのプレビュー拡張機能があります。この機能では、Azure Active Directory (Azure AD) ID でログインし、スクリプト コマンドを実行できます。 Azure AD ID には、ユーザー、グループ、アプリケーション サービス プリンシパル、または [Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用できます。 ストレージ リソースにアクセスするためのアクセス許可をロールベースのアクセス制御 (RBAC) を介して Azure AD ID に割り当てることができます。 Azure Storage の RBAC ロールについては、「[Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md)」 (RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)) を参照してください。

Azure AD ID で Azure CLI または PowerShell にログインするとき、その ID で Azure Storage にアクセスするためのアクセス トークンが返されます。 そのトークンが CLI または PowerShell によって自動的に使用され、Azure Storage に対する操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>サポート対象の操作

プレビュー拡張機能は、コンテナーとキューの操作でサポートされています。 呼び出す操作は、Azure CLI または PowerShell にログインする Azure AD ID に与えられているアクセス許可に依存します。 Azure Storage のコンテナーまたはキューのアクセス許可は、ロールベースのアクセス制御 (RBAC) を介して割り当てられます。 たとえば、Data Reader ロールが ID に割り当てられている場合、コンテナーまたはキューからデータを読み取るスクリプト コマンドを実行できます。 Data Contributer ロールが ID に割り当てられている場合、コンテナー、キュー、またはそれらに含まれているデータの読み取り、書き込み、削除を行うスクリプト コマンドを実行できます。 

コンテナーまたはキューでの各 Azure Storage 操作に必要なアクセス許可については、「[Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)」 (REST 操作を呼び出すためのアクセス許可) を参照してください。  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Azure AD ID で CLI コマンドを呼び出す

Azure CLI 用のプレビュー拡張機能をインストールするには:

1. Azure CLI バージョン 2.0.32 以降がインストールされていることを確認してください。 `az --version` を実行し、インストールされたバージョンを確認します。
2. 次のコマンドを実行し、プレビュー拡張機能をインストールします。 

    ```azurecli
    az extension add -n storage-preview
    ```

プレビュー拡張機能によって、サポートされているコマンドに新しい `--auth-mode` パラメーターが追加されます。

- Azure AD ID を利用してサインインするために、`--auth-mode` パラメーターを `login` に設定します。
- アカウントの認証パラメーターが与えられない場合、アカウント キーを問い合わせる目的で、`--auth-mode` パラメーターをレガシー `key` 値に設定します。 

たとえば、Azure AD ID を利用して Azure CLI で BLOB をダウンロードするには、最初に `az login` を実行し、`--auth-mode` を `login` に設定した状態でコマンドを呼び出します。

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

`--auth-mode` パラメーターに関連付けられている環境変数は `AZURE_STORAGE_AUTH_MODE` です。

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Azure AD ID で PowerShell コマンドを呼び出す

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell を使用し、Azure AD ID でサインインするには:

1. Azure PowerShell の以前のインストールがある場合はアンインストールします。

    - **[設定]** の **[アプリと機能]** 設定を使用して、Windows から Azure PowerShell の以前のインストールを削除します。
    - `%Program Files%\WindowsPowerShell\Modules` からすべての **Azure*** モジュールを削除します。

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 Windows PowerShell ウィンドウを開き、次のコマンドを実行して最新バージョンをインストールします。
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. PowerShellGet のインストール後、PowerShell ウィンドウを閉じて再び開きます。 

1. 最新バージョンの Azure PowerShell をインストールします。

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Azure AD をサポートする Azure Storage プレビュー モジュールをインストールします。
   
   ```powershell
   Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
   ```
1. PowerShell ウィンドウを閉じて再び開きます。
1. [New-AzStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStoragecontext) コマンドレットを呼び出してコンテキストを作成し、`-UseConnectedAccount` パラメーターを含めます。 
1. Azure AD ID でコマンドレットを呼び出すには、新しく作成したコンテキストをコマンドレットに渡します。

次の例では、Azure AD ID を使用し、Azure PowerShell からコンテナーの BLOB を一覧表示する方法を示しています。 プレースホルダーのアカウント名とコンテナー名は、必ず実際の値に置き換えてください。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。
- Azure Storage を使用して Azure リソースのマネージド ID を使用する方法については、「[Azure マネージド サービス ID (プレビュー) から Azure AD の認証を受ける](storage-auth-aad-msi.md)」を参照してください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
- Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
