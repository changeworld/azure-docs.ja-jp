---
title: Azure リソース (プレビュー) の Azure Active Directory 管理 ID を持つ blob およびキューへのアクセスの認証 - Azure Storage |Microsoft Docs
description: Azure の Blob およびキュー ストレージは、Azure リソースの管理 ID を使用する Azure Active Directory 認証をサポートします。 Azure リソースの管理 ID を使用して、Azure の仮想マシン、関数アプリ、仮想マシン スケール セット、およびその他においてで実行されているアプリケーションから blob およびキューへのアクセスを認証することができます。 Azure リソースの管理 ID を使用し、Azure AD の認証機能を利用して、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 67e0731c1f10bb635baa4e0d1a26dce0a336b555
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090357"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-managed-identities-for-azure-resources-preview"></a>Azure リソース (プレビュー) の Azure 管理 ID を使用した blob およびキューへのアクセス認証

Azure の Blob およびキュー ストレージは、Azure Active Directory (Azure AD) 認証を[ Azure リソースの管理 ID ](../../active-directory/managed-identities-azure-resources/overview.md)を使用してサポートします。 Azure リソースの管理 ID を使用して、Azure の仮想マシン (VMs)、関数アプリ、仮想マシン スケール セット、およびその他においてで実行されているアプリケーションから blob およびキューへのアクセスを認証することができます。 Azure リソースの管理 ID を使用し、Azure AD の認証機能を利用して、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。  

管理 ID から blob コンテナーまたはキューへのアクセス許可を付与するには、ロールベースのアクセス制御 (RBAC) ロールを適切なスコープでそのリソースに対するアクセス許可を含む管理 ID に割り当てます。 ストレージの RBAC ロールについては、[RBAC でストレージ データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。 

この記事では Azure VM から 管理 ID を使用して Azure Blob または キューストレージ の認証を受ける方法について説明しています。  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>VM 上の管理 ID を有効にする

VM から blob およびキューへのアクセス認証に対し Azure リソースの管理 ID を使用する前に、まずは VM 上の管理 ID を有効にする必要があります。 Azure リソースの管理 ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>管理 ID アクセス トークンの取得

管理 ID を使用して認証するには、アプリケーションまたはスクリプトは、管理 ID へのアクセス トークンを取得する必要があります。 アクセス トークンを取得する方法については、「[アクセストークンを取得するために Azure VM 上の Azure リソースの管理 ID を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)」を参照してください。

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB の作成

このコード例では、管理 ID アクセス トークンがあることを前提としています。 アクセス トークンは、管理 ID によるブロック BLOB の作成を承認するために使用されます。

### <a name="add-references-and-using-statements"></a>参照と using ステートメントを追加する  

Visual Studio で、Azure Storage クライアント ライブラリのプレビュー バージョンをインストールします。 **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。 次のコマンドをコンソールに入力します。

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

以下の using ステートメントをコードに追加します。

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>管理 ID アクセス トークンから資格情報を作成する

ブロック BLOB を作成するには、プレビュー パッケージで提供されている **TokenCredentials** クラスを使用します。 以前取得した管理 ID アクセス トークンを渡して、**TokenCredentials** の新しいインスタンスを作成します。

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD と Azure Storage の統合では、Azure Storage 操作に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
- Azure AD ID で Azure CLI と PowerShell にログインする方法については、「[Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)](storage-auth-aad-script.md)」を参照してください。
- Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
