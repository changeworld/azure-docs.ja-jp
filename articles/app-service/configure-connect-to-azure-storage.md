---
title: カスタム ストレージの追加 (Windows コンテナー)
description: Azure App Service でカスタムの Windows コンテナーにカスタム ネットワーク共有をアタッチする方法について説明します。 アプリ間でのファイルの共有、静的コンテンツのリモート管理、ローカルでのアクセスなどを行います。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120667"
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

## <a name="limitations"></a>制限事項

- Windows コンテナーの Azure Storage は**プレビュー段階**であり、**運用シナリオ**では**サポートされていません**。
- Windows コンテナーの Azure Storage では、**Azure Files コンテナー** (読み取り/書き込み) のみをマウントできます。
- Windows コンテナーの Azure Storage は、Windows App Service プランでの独自のコード使用のシナリオでは**サポートされていません**。
- Windows コンテナーの Azure Storage では、インフラストラクチャの制限により、**Storage Firewall** 構成の使用は**サポートされていません**。
- Windows コンテナーの Azure Storage では、アプリあたり**最大 5 つ**のマウント ポイントを指定できます。
- アプリにマウントされた Azure Storage に App Service の FTP または FTPS エンドポイント経由でアクセスすることはできません。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用します。
- Azure Storage は個別に課金され、Web アプリには**含まれていません**。 [Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage)の詳細を確認してください。

## <a name="link-storage-to-your-web-app-preview"></a>ストレージを Web アプリにリンクする (プレビュー)

 Azure Files 共有を App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類は、AzureFiles である必要があります。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Azure Files 共有にリンクする他のすべてのディレクトリについて、これを行う必要があります。

## <a name="verify"></a>Verify (英語の可能性あり)

Azure Files 共有を Web アプリにリンクしたら、次のコマンドを実行してこれを確認できます。

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>次のステップ

- [Windows コンテナー (プレビュー) を使用して Azure App Service に ASP.NET アプリを移行する](app-service-web-tutorial-windows-containers-custom-fonts.md)。
