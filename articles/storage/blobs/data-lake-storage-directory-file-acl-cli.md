---
title: Azure Data Lake Storage Gen2 でファイルと ACL に Azure CLI を使用する (プレビュー)
description: Azure CLI を使用して、階層型名前空間を持つストレージ アカウントでディレクトリ、ファイル、ディレクトリのアクセス制御リスト (ACL) を管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486136"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Azure CLI を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する (プレビュー)

この記事では、[Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用して、階層型名前空間を持つストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

> [!IMPORTANT]
> この記事で紹介されている `storage-preview` 拡張は、現在パブリック プレビュー段階です。

[サンプル](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [フィードバックの送信](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。
> * Azure CLI バージョン `2.0.67` 以上。

## <a name="install-the-storage-cli-extension"></a>ストレージ CLI 拡張機能をインストールする

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を開きます。または、Azure CLI をローカルに[インストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.0.67` 以上であることを確認します。

   ```azurecli
    az --version
   ```
   Azure CLI のバージョンが `2.0.67` より低い場合は、新しいバージョンをインストールします。 「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

3. `storage-preview` 拡張機能をインストールします。

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>アカウントに接続する

1. Azure CLI をローカルで使用している場合は、login コマンドを実行します。

   ```azurecli
   az login
   ```

   CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

   それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。 次に、ブラウザーでアカウントの資格情報を使用してサインインします。

   さまざまな認証方法の詳細については、「Azure CLI を使用してサインインする」を参照してください。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

## <a name="create-a-file-system"></a>ファイル システムを作成する

ファイル システムは、ファイルのコンテナーとして機能します。 `az storage container create` コマンドを使用して、ファイル システムを作成できます。 

この例では、`my-file-system` という名前のファイル システムを作成します。

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>ディレクトリを作成する

`az storage blob directory create` コマンドを使用して、ディレクトリ参照を作成します。 

この例では、`mystorageaccount` という名前のアカウントにある `my-file-system` という名前のファイル システムに `my-directory` という名前のディレクトリを追加します。

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>ディレクトリのプロパティを表示する

`az storage blob show` コマンドを使用して、ディレクトリのプロパティをコンソールに出力できます。

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

`az storage blob directory move` コマンドを使用して、ディレクトリの名前変更または移動を行います。

この例では、ディレクトリの名前を `my-directory` から `my-new-directory` に変更します。

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

`az storage blob directory delete` コマンドを使用してディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>ディレクトリが存在するかどうか確認する

`az storage blob directory exist` コマンドを使用して、ファイル システムに特定のディレクトリが存在するかどうかを確認します。

この例では、`my-file-system` ファイル システムに、`my-directory` という名前のディレクトリが存在するかどうかを確認します。 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

`az storage blob directory download` コマンドを使用して、ディレクトリからファイルをダウンロードします。

この例では、`my-directory` という名前のディレクトリから `upload.txt` という名前のファイルをダウンロードします。 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

この例では、ディレクトリ全体をダウンロードします。

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

`az storage blob directory list` コマンドを使用して、ディレクトリの内容を一覧表示します。

この例では、`mystorageaccount` という名前のストレージ アカウントの `my-file-system` ファイル システムにある `my-directory` という名前のディレクトリの内容を一覧表示します。 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

`az storage blob directory upload` コマンドを使用して、ファイルをディレクトリにアップロードします。

この例では、`upload.txt` という名前のファイルを `my-directory` という名前のディレクトリにアップロードします。 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

この例では、ディレクトリ全体をアップロードします。

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>ファイルのプロパティを表示する

`az storage blob show` コマンドを使用して、ファイルのプロパティをコンソールに出力できます。

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>ファイルを名前変更または移動する

`az storage blob move` コマンドを使用して、ファイルの名前変更または移動を行います。

この例では、ファイルの名前を `my-file.txt` から `my-file-renamed.txt` に変更します。

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>ファイルを削除する

`az storage blob delete` コマンドを使用してファイルを削除します。

この例では、`my-file.txt` という名前のファイルを削除します。

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>アクセス許可の管理

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してコマンドを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

### <a name="get-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を取得する

`az storage blob directory access show` コマンドを使用して、**ディレクトリ**の ACL を取得します。

この例では、ディレクトリの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

`az storage blob access show` コマンドを使用して、**ファイル**のアクセス許可を取得します。 

この例では、ファイルの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

次の画像は、ディレクトリの ACL を取得した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可があります。 所有グループには、読み取りと実行のアクセス許可のみがあります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="set-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を設定する

`az storage blob directory access set` コマンドを使用して、**ディレクトリ**の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに*既定の* ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

`az storage blob access set` コマンドを使用して、**ファイル**の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのファイルに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
次の画像は、ファイルの ACL を設定した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

この例では、所有ユーザーと所有グループには、読み取りと書き込みのアクセス許可のみがあります。 他のすべてのユーザーには、書き込みと実行のアクセス許可があります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="update-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を更新する

`az storage blob directory access update` コマンドまたは `az storage blob access update` コマンドを使用して、このアクセス許可を設定することもできます。 

ディレクトリまたはファイルの ACL を更新するには、`-permissions` パラメーターを短縮形の ACL に設定します。

この例では、**ディレクトリ**の ACL を更新します。

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

この例では、**ファイル**の ACL を更新します。

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

`--owner` または `group` パラメーターをユーザーのエンティティ ID またはユーザー プリンシパル名 (UPN) に設定して、ディレクトリまたはファイルの所有ユーザーとグループを更新することもできます。 

この例では、ディレクトリの所有者を変更します。 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

この例では、ファイルの所有者を変更します。 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>ユーザー定義のメタデータを管理する

1 つ以上の名前と値のペアと共に `az storage blob directory metadata update` コマンドを使用することにより、ユーザー定義のメタデータをファイルまたはディレクトリに追加できます。

この例では、`my-directory` という名前のディレクトリのユーザー定義のメタデータを追加します。

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

この例では、`my-directory` という名前のディレクトリのすべてのユーザー定義メタデータを表示します。

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>関連項目

* [サンプル](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [フィードバックのご提供](https://github.com/Azure/azure-cli-extensions/issues)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [ソース コード](https://github.com/Azure/azure-cli-extensions/tree/master/src)

