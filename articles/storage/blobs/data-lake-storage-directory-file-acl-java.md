---
title: ファイル と ACL 用の Azure Data Lake Storage Gen2 Java SDK
description: Java 用 Azure Storage ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、ディレクトリとファイル、およびディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108129"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、Java を使用してディレクトリ、ファイル、アクセス許可を作成および管理する方法を示します。 

[パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API リファレンス](/java/api/overview/azure/storage-file-datalake-readme) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](../common/storage-account-create.md)の手順に従います。

## <a name="set-up-your-project"></a>プロジェクトの設定

開始するには、[こちらのページ](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)にアクセスして、最新バージョンの Java ライブラリを検索します。 次に、お使いのテキスト エディターで *pom.xml* ファイルを開きます。 該当のバージョンを参照する依存関係要素を追加します。

Azure Active Directory (AD) を使用してクライアント アプリケーションを認証したい場合には、Azure Secret Client Library に依存関係を追加します。 「[プロジェクトに Secret Client Library パッケージを追加する](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project) 」を参照してください。

次に、以下の import ステートメントをコード ファイルに追加します。

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>アカウントに接続する 

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用して接続する

[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して、**DataLakeServiceClient** インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンの取得する](../common/storage-auth-aad-app.md)」を参照してください。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> その他の例については、「[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)」のドキュメントを参照してください。


## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 これは、**DataLakeServiceClient.createFileSystem** メソッドを呼び出すことで作成できます。

この例では、`my-file-system` という名前のコンテナーを作成します。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>ディレクトリを作成する

**DataLakeFileSystemClient.createDirectory** メソッドを呼び出して、ディレクトリ参照を作成します。

この例では、`my-directory` という名前のディレクトリをコンテナーに追加してから、`my-subdirectory` という名前のサブディレクトリを追加します。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

**DataLakeDirectoryClient.rename** メソッドを呼び出して、ディレクトリを名前変更または移動します。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-subdirectory-renamed` に変更します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

この例では、`my-subdirectory-renamed` という名前のディレクトリを `my-directory-2` という名前のディレクトリのサブディレクトリに移動します。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>ディレクトリを削除する

**DataLakeDirectoryClient.deleteWithResponse** メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

まず、**DataLakeFileClient** クラスのインスタンスを作成して、ターゲット ディレクトリにファイル参照を作成します。 **DataLakeFileClient.append** メソッドを呼び出して、ファイルをアップロードします。 **DataLakeFileClient.FlushAsync** メソッドを呼び出して、アップロードを確実に完了します。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> ファイルサイズが大きい場合は、コードで **DataLakeFileClient.append** メソッドを複数回呼び出す必要があります。 代わりに、**DataLakeFileClient.uploadFromFile** メソッドの使用を検討してください。 この方法により、1 回の呼び出しでファイル全体をアップロードできます。 
>
> 次のセクションの例を参照してください。

## <a name="upload-a-large-file-to-a-directory"></a>大きなファイルをディレクトリにアップロードする

**DataLakeFileClient.append** メソッドを複数回呼び出すことなく、大きなファイルをアップロードするには、**DataLakeFileClient.uploadFromFile** メソッドを使用します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

まず、ダウンロードするファイルを表す **DataLakeFileClient** インスタンスを作成します。 **DataLakeFileClient.read** メソッドを使用してファイルを読み取ります。 任意の .NET ファイル処理 API を使用して、ストリームからファイルにバイトを保存します。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

この例では、`my-directory` という名前のディレクトリにある各ファイルの名前を出力します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>アクセス制御リスト (ACL) を管理する

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

### <a name="manage-a-directory-acl"></a>ディレクトリ ACL を管理する

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`""`) を渡します。

### <a name="manage-a-file-acl"></a>ファイル ACL を管理する

この例では、`upload-file.txt` という名前のファイルの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>ACL を再帰的に設定する

また、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することができます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL) を再帰的に設定する](recursive-access-control-lists.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [API リファレンス ドキュメント](/java/api/overview/azure/storage-file-datalake-readme)
* [パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)