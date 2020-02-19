---
title: Azure Storage から Linux コンテナーにコンテンツを提供する
description: Azure App Service で Linux コンテナーにカスタムネットワーク共有をアタッチする方法を学びます。 アプリ間でのファイルの共有、静的コンテンツのリモート管理、ローカルでのアクセスなどを行います。
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: b2be84625035bb368784f3f423d63121c29255ad
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121421"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Linux 上の App Service 内の Azure Storage からコンテンツを提供する

> [!NOTE]
> この記事は、Linux コンテナーに適用されます。 カスタム Windows コンテナーにデプロイする場合は、「[App Service の Windows コンテナーで Azure Files を構成する](../configure-connect-to-azure-storage.md)」を参照してください。 App Service on Linux 内の Azure Storage は、**プレビュー**機能です。 この機能は、**運用シナリオではサポートされていません**。
>

このガイドでは、Azure Storage を App Service on Linux にアタッチする方法について説明します。 利点としては、セキュリティで保護されたコンテンツ、コンテンツの移植性、永続ストレージ、複数のアプリへのアクセス、複数の転送方法などがあります。

## <a name="prerequisites"></a>前提条件

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 以降)。
- 既存の [App Service on Linux アプリ](https://docs.microsoft.com/azure/app-service/containers/)。
- [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure のファイル共有とディレクトリ](https://docs.microsoft.com/azure/storage/common/storage-azure-cli#create-and-manage-file-shares)。


## <a name="limitations-of-azure-storage-with-app-service"></a>App Service を使用する Azure Storage の制限事項

- App Service を使用する Azure Storage は、App Service on Linux と Web App for Containers では**プレビュー段階**にあります。 **運用シナリオ**では**サポートされていません**。
- App Service を使用する Azure Storage では、**Azure Files コンテナー** (読み取り/書き込み) と **Azure Blob コンテナー** (読み取り専用) のマウントがサポートされています
- App Service を使用する Azure Storage では、インフラストラクチャの制限により、**Storage Firewall** 構成の使用は**サポートされていません**。
- App Service を使用する Azure Storage では、アプリあたり**最大 5 つ**のマウント ポイントを指定できます。
- アプリにマウントされた Azure Storage に App Service の FTP または FTPS エンドポイント経由でアクセスすることはできません。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用します。
- Azure Storage は Web アプリに**含まれていない**ため、別途請求されます。 [Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage)の詳細を確認してください。

> [!WARNING]
> Azure Blob Storage を使用する App Service 構成は、2020 年 2 月に読み取り専用になります。 [詳細情報](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Azure Storage を使用してアプリを構成する

[Azure Storage アカウント、ファイル共有、ディレクトリ](#prerequisites)を作成したら、Azure Storage を使用してアプリを構成できるようになります。

ストレージ アカウントを App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類としては、AzureBlob または AzureFiles を使用できます。 この例では、AzureFiles が使用されています。


> [!CAUTION]
> Web アプリでマウント パスとして指定されたディレクトリは、空である必要があります。 このディレクトリに格納されているコンテンツは、外部マウントが追加されるときに削除されます。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

ストレージ アカウントにリンクする他のすべてのディレクトリについて、これを行う必要があります。

## <a name="verify-azure-storage-link-to-the-web-app"></a>Web アプリへの Azure Storage のリンクを確認する

ストレージ コンテナーを Web アプリにリンクしたら、次のコマンドを実行してこれを確認できます。

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Docker Compose で Azure Storage を使用する

Azure Storage は、custom-id を使用してマルチコンテナー アプリを使用してマウントできます。custom-id 名を表示するには、[`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list) を実行します。

*docker-compose.yml* ファイルで、`volumes` オプションを `custom-id` にマップします。 次に例を示します。

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>次のステップ

- [Azure App Service での Web アプリの構成](../configure-common.md)。

