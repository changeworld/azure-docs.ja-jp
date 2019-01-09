---
title: Linux 上の Azure Storage からコンテンツを提供する - App Service
description: Linux 上の Azure App Service 内の Azure Storage を構成してコンテンツを提供する方法を説明します。
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 11/01/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 855df2820db6bba2b47d543ab671bee2193d7d9b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250921"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Linux 上の App Service 内の Azure Storage からコンテンツを提供する

このガイドでは、[Azure Storage](/azure/storage/common/storage-introduction) を使用して Linux 上の App Service 内の静的コンテンツを提供する方法を示します。 利点としては、セキュリティで保護されたコンテンツ、コンテンツの移植性、複数のアプリへのアクセス、複数の転送方法などがあります。 このガイドでは、[カスタム ストレージを構成する](https://blogs.msdn.microsoft.com/appserviceteam/2018/09/24/announcing-bring-your-own-storage-to-app-service/)ことにより Azure Storage 上のコンテンツを提供する方法を説明します。

## <a name="prerequisites"></a>前提条件

- 既存の Web アプリ (Linux 上の App Service または Web App for Containers)。
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 以降)。

## <a name="create-azure-storage"></a>Azure Storage を作成する

> [!NOTE]
> Azure Storage は、既定ではないストレージであり、別途請求され、Web アプリには含まれません。
>

[Azure ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)を作成します。

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Azure Storage にファイルをアップロードする

ローカル ディレクトリをストレージ アカウントにアップロードするには、次の例のような [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) コマンドを使用します。

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app"></a>ストレージを Web アプリにリンクする

> [!CAUTION]
> Web アプリ内の既存のディレクトリをストレージ アカウントにリンクすると、ディレクトリの内容が削除されます。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
>

ストレージ アカウントを App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類としては、AzureBlob または AzureFiles を使用できます。 このコンテナーには AzureBlob を使用します。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

ストレージ アカウントにリンクする他のすべてのディレクトリについて、これを行う必要があります。

## <a name="verify"></a>確認

ストレージ コンテナーを Web アプリにリンクしたら、次のコマンドを実行してこれを確認できます。

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>次の手順

- [Azure App Service での Web アプリの構成](https://docs.microsoft.com/azure/app-service/web-sites-configure)。
