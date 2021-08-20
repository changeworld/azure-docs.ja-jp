---
title: 'クイックスタート: Azure Storage ファイル共有ライブラリ v12 - C++'
description: このクイックスタートでは、C++ 用 Azure Storage ファイル共有クライアント ライブラリ バージョン 12 を使用して、ファイル共有とファイルを作成する方法について説明します。 次に、メタデータを設定して取得し、ファイルをローカル コンピューターにダウンロードする方法について説明します。
author: kyle-patterson
ms.author: kylepa
ms.date: 06/22/2021
ms.service: storage
ms.subservice: files
ms.topic: quickstart
ms.openlocfilehash: 4da02f46ef793ae03a11fa4895471488f78d0db1
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894310"
---
# <a name="develop-for-azure-files-with-c"></a>C++ での Azure Files 用の開発

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="about-this-tutorial"></a>このチュートリアルについて

このチュートリアルでは、C++ を使用して Azure Files で基本的な操作を行う方法について説明します。 Azure Files を初めて利用する場合は、各セクションの概念をお読みください。サンプルを理解する上で役立ちます。 ここでは、次のサンプルについて説明します。

* Azure ファイル共有を作成および削除する
* ディレクトリを作成および削除する
* ファイルのアップロード、ダウンロード、および削除
* ファイルのメタデータを設定して一覧表示する

> [!Note]  
> Azure Files には SMB 経由でアクセスできるため、標準の C++ I/O クラスおよび関数を使用して Azure ファイル共有にアクセスする単純なアプリケーションを記述できます。 この記事では、[File REST API](/rest/api/storageservices/file-service-rest-api) を使用して Azure Files と通信する Azure Storage C++ SDK を使用するアプリケーションを記述する方法について説明します。


## <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Storage アカウント](../common/storage-account-create.md)
- [C++ コンパイラ](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C および C++ パッケージ マネージャー](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>設定

このセクションでは、C++ 用 Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="install-the-packages"></a>パッケージのインストール

`vcpkg install` コマンドにより、C++ 用の Azure Storage Blob SDK と必要な依存関係がインストールされます。

```console
vcpkg.exe install azure-storage-files-shares-cpp:x64-windows
```

詳細については、GitHub にアクセスし、[C++ 用 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/) を入手してビルドします。

### <a name="create-the-project"></a>プロジェクトの作成

Visual Studio で、*FilesShareQuickstartV12* という、Windows 向けの新しい C++ コンソール アプリケーションを作成します。

:::image type="content" source="./media/quickstart-files-c-plus-plus/visual-studio-create-project.png" alt-text="新しい C++ Windows コンソール アプリを構成するための Visual Studio ダイアログ":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットでは、C++ 用 Azure Files 共有クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

- [インクルード ファイルを追加する](#add-include-files)
- [接続文字列を取得する](#get-the-connection-string)
- [ファイル共有を作成する](#create-a-files-share)
- [ファイル共有にファイルをアップロードする](#upload-files-to-a-files-share)
- [ファイルのメタデータを設定する](#set-the-metadata-of-a-file)
- [ファイルのメタデータを一覧表示する](#list-the-metadata-of-a-file)
- [ファイルのダウンロード](#download-files)
- [ファイルを削除する](#delete-a-file)
- [ファイル共有を削除する](#delete-a-files-share)

### <a name="add-include-files"></a>インクルード ファイルを追加する

プロジェクト ディレクトリで次の操作を行います。

1. Visual Studio で *FilesShareQuickstartV12.sln* ソリューション ファイルを開きます。
1. Visual Studio 内で、*FilesShareQuickstartV12.cpp* ソース ファイルを開きます。
1. `main` 内の自動生成されたコードをすべて削除します。
1. `#include` ステートメントを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」で作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

このコードを `main()` 内に追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-files-share"></a>ファイル共有を作成する

[CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a7462bcad8a9144f84b0acc70735240e2) 関数を呼び出して、[ShareClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html) クラスのインスタンスを作成します。 次に、[CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a6432b4cc677ac03257c7bf234129ec5b) を呼び出して、ストレージ アカウントに実際のファイル共有を作成します。

`main()` の末尾に次のコードを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_CreateFilesShare":::

### <a name="upload-files-to-a-files-share"></a>ファイル共有にファイルをアップロードする

次のコード スニペット:

1. "Hello Azure!" を含む文字列を宣言します。
1. ルート [ShareDirectoryClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html) を取得し、[[ファイル共有の作成]](#create-a-files-share) セクションのファイル共有で [GetFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html#a93545b8d82ee94f9de183c4d277059d8) を呼び出すことによって、[ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) オブジェクトへの参照を取得します。
1. [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#aa80c1f0da6e6784aa0a67cff03d128ba) 関数を呼び出して、文字列をファイルにアップロードします。 この関数では、ファイルがまだ存在しない場合は作成し、既に存在する場合は更新します。

`main()` の末尾に次のコードを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_UploadFile":::

### <a name="set-the-metadata-of-a-file"></a>ファイルのメタデータを設定する

[ShareFileClient.SetMetadata](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a2f5a40b71040003e41ba8495101f67bb) 関数を呼び出して、ファイルのメタデータ プロパティを設定します。

`main()` の末尾に次のコードを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_SetFileMetadata":::

### <a name="list-the-metadata-of-a-file"></a>ファイルのメタデータを一覧表示する

[ShareFileClient.GetProperties](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a220017815847877cfe8f244b482fb45c) 関数を呼び出して、ファイルのメタデータ プロパティを取得します。 メタデータは、返された `Value` の `Metadata` フィールドの下に あります。 メタデータは、[ファイルのメタデータを設定する](#set-the-metadata-of-a-file)例と同様に、キーと値のペアになります。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_GetFileMetadata":::

### <a name="download-files"></a>ファイルをダウンロードする

アップロードされたファイルのプロパティを使用して、「[ファイルのメタデータを一覧表示する](#list-the-metadata-of-a-file)」で取得したファイルのプロパティを新しい `std::vector<uint8_t>` オブジェクトに保存します。 [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) 基底クラスの [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a256e7b317fbf762c4f8f5023a2cd8cb9) 関数を呼び出して、先ほど作成したファイルを新しい `std::vector<uint8_t>` オブジェクトにダウンロードします。 最後に、ダウンロードされたファイルのデータを表示します。

`main()` の末尾に次のコードを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DownloadFile":::

### <a name="delete-a-file"></a>ファイルを削除する

次のコードでは、[ShareFileClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a86036b445dce67a96f7bf6c45f163f59) 関数を呼び出して、Azure Storage ファイル共有から BLOB を削除します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFile":::

### <a name="delete-a-files-share"></a>ファイル共有を削除する

次のコードでは、[ShareClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a9915630503f1675b5f49eb9c01ca2601) を使用してファイル共有全体を削除することで、アプリによって作成されたリソースをクリーンアップします。

`main()` の末尾に次のコードを追加します。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFilesShare":::

## <a name="run-the-code"></a>コードの実行

このアプリは、コンテナーを作成し、テキスト ファイルを Azure Blob Storage にアップロードします。 さらに、この例では、コンテナー内の BLOB を一覧表示し、ファイルをダウンロードして、ファイルの内容を表示します。 最後に、BLOB とコンテナーを削除します。

アプリの出力は、次の例のようになります。

```output
Azure Files Shares storage v12 - C++ quickstart sample
Creating files share: sample-share
Uploading file: sample-file
Listing file metadata...
key1:value1
key2:value2
Downloaded file contents: Hello Azure!
Deleting file: sample-file
Deleting files share: sample-share
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、C++ を使用してファイルをアップロード、ダウンロード、一覧表示する方法について学習しました。 また、Azure Storage ファイル共有の作成方法と削除方法についても学習しました。

C++ Blob Storage サンプルを確認するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Storage ファイル共有 SDK v12 for C++ のサンプル](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-files-shares/sample)
