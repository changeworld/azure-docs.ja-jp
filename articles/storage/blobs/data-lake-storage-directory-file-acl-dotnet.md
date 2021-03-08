---
title: ファイルと ACL 用の Azure Data Lake Storage Gen2 .NET SDK
description: Azure Storage クライアント ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、ディレクトリとファイル、およびディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 5af1c656699a7c60ad4f93beb43b603bdc6e3be7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935106"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>.NET を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、.NET を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

[パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API リファレンス](/dotnet/api/azure.storage.files.datalake) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](../common/storage-account-create.md)の手順に従います。

## <a name="set-up-your-project"></a>プロジェクトの設定

まず、[Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet パッケージをインストールします。

NuGet パッケージのインストール方法の詳細については、「[NuGet パッケージ マネージャーを使用して Visual Studio にパッケージをインストールして管理する](/nuget/consume-packages/install-use-packages-visual-studio)」を参照してください。

次に、これらの using ステートメントをコード ファイルの先頭に追加します。

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成する必要があります。 

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> その他の例については、[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)のドキュメントを参照してください。

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 これは、[DataLakeServiceClient.CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) メソッドを呼び出すことで作成できます。

この例では、`my-file-system` という名前のコンテナーを作成します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>ディレクトリを作成する

[DataLakeFileSystemClient.CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) メソッドを呼び出して、ディレクトリ参照を作成します。

この例では、`my-directory` という名前のディレクトリをコンテナーに追加してから、`my-subdirectory` という名前のサブディレクトリを追加します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

[DataLakeDirectoryClient.RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) メソッドを呼び出して、ディレクトリを名前変更または移動します。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-subdirectory-renamed` に変更します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

この例では、`my-subdirectory-renamed` という名前のディレクトリを `my-directory-2` という名前のディレクトリのサブディレクトリに移動します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>ディレクトリを削除する

[DataLakeDirectoryClient.Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

まず、[DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) クラスのインスタンスを作成して、ターゲット ディレクトリにファイル参照を作成します。 [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) メソッドを呼び出して、ファイルをアップロードします。 [DataLakeFileClient.FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) メソッドを呼び出して、アップロードを確実に完了します。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。 
   
:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> ファイル サイズが大きい場合は、コードで [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) を複数回呼び出す必要があります。 代わりに [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) メソッドを使用することを検討してください。 この方法により、1 回の呼び出しでファイル全体をアップロードできます。 
>
> 次のセクションの例を参照してください。

## <a name="upload-a-large-file-to-a-directory"></a>大きなファイルをディレクトリにアップロードする

[DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) メソッドを使用して大きなファイルをアップロードします。[DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) メソッドを複数回呼び出す必要はありません。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする 

まず、ダウンロードするファイルを表す [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) インスタンスを作成します。 [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) メソッドを使用し、戻り値を解析して [Stream](/dotnet/api/system.io.stream) オブジェクトを取得します。 任意の .NET ファイル処理 API を使用して、ストリームからファイルにバイトを保存します。 

この例では、[BinaryReader](/dotnet/api/system.io.binaryreader) と [FileStream](/dotnet/api/system.io.filestream) を使用してバイトをファイルに保存します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

[FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) メソッドを呼び出してディレクトリの内容を一覧表示し、結果を列挙します。

この例では、`my-directory` という名前のディレクトリにある各ファイルの名前を出力します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>アクセス制御リスト (ACL) を管理する

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

### <a name="manage-a-directory-acl"></a>ディレクトリ ACL を管理する

[DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) メソッドを呼び出してディレクトリのアクセス制御リスト (ACL) を取得し、[DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認する場合は、アプリケーションがアクセスを承認するために使用するセキュリティ プリンシパルが、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)に割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。 

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 `user::rwx,group::r-x,other::rw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

また、コンテナーのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、[DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) メソッドに空の文字列 (`""`) を渡します。

### <a name="manage-a-file-acl"></a>ファイル ACL を管理する

[DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) メソッドを呼び出してファイルのアクセス制御リスト (ACL) を取得し、[DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認する場合は、アプリケーションがアクセスを承認するために使用するセキュリティ プリンシパルが、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)に割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。 

この例では、`my-file.txt` という名前のファイルの ACL を取得して設定します。 `user::rwx,group::r-x,other::rw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>ACL を再帰的に設定する

また、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することができます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL) を再帰的に設定する](recursive-access-control-lists.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [API リファレンス ドキュメント](/dotnet/api/azure.storage.files.datalake)
* [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-net/issues)