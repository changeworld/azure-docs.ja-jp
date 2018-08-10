---
title: Azure VM マネージド サービス ID (プレビュー) から Azure AD の認証を受ける | Microsoft Docs
description: Azure VM マネージド サービス ID (プレビュー) から Azure AD の認証を受けます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 6ddae66ee6408a3cab905826cd0d7c0831607d33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526387"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Azure マネージド サービス ID (プレビュー) から Azure AD の認証を受ける

Azure Storage は、[マネージド サービス ID](../../active-directory/managed-service-identity/overview.md) による Azure Active Directory (Azure AD) の認証をサポートしています。 マネージド サービス ID (MSI) は、Azure Active Directory (Azure AD) で、自動マネージド ID を提供します。 Azure 仮想マシン、関数アプリ、仮想マシン スケール セットなどで動作するアプリケーションから、MSI を使用して Azure Storage の認証を受けることができます。 MSI を使用し、Azure AD の認証機能を利用して、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。  

ストレージ コンテナーまたはキューのマネージド サービス ID にアクセス許可を付与するには、ストレージのアクセス許可を含む RBAC ロールを MSI に割り当てます。 ストレージの RBAC ロールについては、[RBAC でストレージ データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。 

> [!IMPORTANT]
> このプレビューは、非運用環境でのみの使用を意図しています。 運用サービス レベル アグリーメント (SLA) は、Azure Storage 用の Azure AD 統合の一般公開が宣言されるまで利用できません。 ご利用のシナリオで Azure AD 統合がまだサポートされていない場合、お使いのアプリケーションでは共有キー承認か SAS トークンを引き続き使用してください。 プレビューの詳細については、「[Authenticate access to Azure Storage using Azure Active Directory (Preview) (Azure Active Directory を使用した Azure Storage へのアクセスの認証 (プレビュー))](storage-auth-aad.md)」を参照してください。
>
> プレビュー中、RBAC ロールの割り当ては反映に最大 5 分かかることがあります。

この記事では Azure VM から MSI を使用して Azure Storage の認証を受ける方法について説明しています。  

## <a name="enable-msi-on-the-vm"></a>VM 上で MSI を有効にする

VM から MSI を使用して Azure Storage の認証を受けるには、まず VM 上で MSI を有効にする必要があります。 MSI を有効にする方法については、以下のいずれかの記事を参照してください。

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>MSI アクセス トークンを取得する

MSI での認証には、アプリケーションまたはスクリプトが MSI アクセス トークンを取得する必要があります。 アクセス トークンの取得方法については、「[トークン取得に Azure VM の管理対象サービス ID (MSI) を使用する方法](../../active-directory/managed-service-identity/how-to-use-vm-token.md)」を参照してください。

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB の作成

このコード例では、MSI アクセス トークンがあることを前提としています。 アクセス トークンは、マネージド サービス ID によるブロック BLOB の作成を承認するために使用されます。

### <a name="add-references-and-using-statements"></a>参照と using ステートメントを追加する  

Visual Studio で、Azure Storage クライアント ライブラリのプレビュー バージョンをインストールします。 **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。 次のコマンドをコンソールに入力します。

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

以下の using ステートメントをコードに追加します。

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>MSI アクセス トークンから資格情報を作成する

ブロック BLOB を作成するには、プレビュー パッケージで提供されている **TokenCredentials** クラスを使用します。 以前取得した MSI アクセス トークンを渡して、**TokenCredentials** の新しいインスタンスを作成します。

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
