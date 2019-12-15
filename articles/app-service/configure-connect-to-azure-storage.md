---
title: カスタム ストレージの追加 (Windows コンテナー)
description: Azure App Service でカスタムの Windows コンテナーにカスタム ネットワーク共有をアタッチする方法について説明します。 アプリ間でのファイルの共有、静的なコンテンツのリモート管理、ローカルでのアクセスなどを行います。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: ad70bbe36369c03225079d1194043e6ceb109c6f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671006"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>App Service の Windows コンテナーで Azure Files を構成します。

> [!NOTE]
> この記事は、カスタムの Windows コンテナーに適用されます。 _Linux_ の App Service にデプロイする場合は、「[Azure Storage からコンテンツを提供する](./containers/how-to-serve-content-from-azure-storage.md)」を参照してください。
>

このガイドでは、Windows コンテナーで Azure Storage にアクセスする方法を示します。 [Azure Files Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) および [Premium ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)のみがサポートされています。 この手順では、Azure Files Shares を使用します。 利点としては、セキュリティで保護されたコンテンツ、コンテンツの移植性、複数のアプリへのアクセス、複数の転送方法などがあります。

## <a name="prerequisites"></a>前提条件

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 以降)。
- [Azure App Service の既存の Windows コンテナー アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure ファイル共有の作成](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Azure File 共有へのファイルのアップロード](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files は、既定ではないストレージであり、別途請求され、Web アプリには含まれません。 インフラストラクチャの制限のため、ファイアウォール構成の使用はサポートされません。
>

## <a name="link-storage-to-your-web-app-preview"></a>ストレージを Web アプリにリンクする (プレビュー)

 Azure Files 共有を App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類は、AzureFiles である必要があります。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Azure Files 共有にリンクする他のすべてのディレクトリについて、これを行う必要があります。

## <a name="verify"></a>確認

Azure Files 共有を Web アプリにリンクしたら、次のコマンドを実行してこれを確認できます。

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>次の手順

- [Windows コンテナー (プレビュー) を使用して Azure App Service に ASP.NET アプリを移行する](app-service-web-tutorial-windows-containers-custom-fonts.md)。
