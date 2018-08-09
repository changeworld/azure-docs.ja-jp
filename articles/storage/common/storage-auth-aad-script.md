---
title: Azure AD ID で Azure CLI または PowerShell コマンドを実行し、Azure Storage にアクセスする (プレビュー) | Microsoft Docs
description: Azure CLI と PowerShell では、Azure AD ID でログインし、Azure Storage コンテナー、キュー、そのデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD ID に割り当てられた役割によって異なります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 253edccef064e729e96bceac619458cf4c585ae4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522487"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)

Azure Storage には、Azure CLI と PowerShell のためのプレビュー拡張機能があります。この機能では、Azure Active Directory (Azure AD) ID でログインし、スクリプト コマンドを実行できます。 ユーザー、グループ、アプリケーション サービス プリンシパルが Azure AD ID になることができます。[マネージド サービス ID](../../active-directory/managed-service-identity/overview.md) が Azure AD ID になることもあります。 ストレージ リソースにアクセスするためのアクセス許可をロールベースのアクセス制御 (RBAC) を介して Azure AD ID に割り当てることができます。 Azure Storage の RBAC ロールについては、「[Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md)」 (RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)) を参照してください。

Azure AD ID で Azure CLI または PowerShell にログインするとき、その ID で Azure Storage にアクセスするためのアクセス トークンが返されます。 そのトークンが CLI または PowerShell によって自動的に使用され、Azure Storage に対する操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

> [!IMPORTANT]
> このプレビューは、非運用環境でのみの使用を意図しています。 運用サービス レベル アグリーメント (SLA) は、Azure Storage 用の Azure AD 統合の一般公開が宣言されるまで利用できません。 ご利用のシナリオで Azure AD 統合がまだサポートされていない場合、お使いのアプリケーションでは共有キー承認か SAS トークンを引き続き使用してください。 プレビューの詳細については、「[Authenticate access to Azure Storage using Azure Active Directory (Preview) (Azure Active Directory を使用した Azure Storage へのアクセスの認証 (プレビュー))](storage-auth-aad.md)」を参照してください。
>
> プレビュー中、RBAC ロールの割り当ては反映に最大 5 分かかることがあります。
>
> Azure AD と Azure Storage の統合では、Azure Storage 操作に HTTPS を使用する必要があります。

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

Azure PowerShell を使用し、Azure AD ID でサインインするには:

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行し、最新バージョンをインストールします。
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Azure PowerShell を以前にインストールしていた場合、それはアンインストールしてください。
3. AzureRM をインストールします。

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. プレビュー モジュールをインストールします。

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) コマンドレットを呼び出してコンテキストを作成し、`-UseConnectedAccount` パラメーターを含めます。 
6. Azure AD ID でコマンドレットを呼び出すには、コンテキストをコマンドレットに渡します。

次の例では、Azure AD ID を使用し、Azure PowerShell からコンテナーの BLOB を一覧表示する方法を示しています。 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。
- Azure Storage でマネージド サービス ID を使用する方法については、「[Authenticate with Azure AD from an Azure Managed Service Identity (Preview)](storage-auth-aad-msi.md)」 (Azure マネージド サービス ID から Azure AD で認証する (プレビュー)) を参照してください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
- Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
