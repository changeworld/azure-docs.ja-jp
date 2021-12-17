---
title: カスタマー マネージド キーまたは BYOK を使用する - ポータル
description: このチュートリアルでは、Azure portal を使用して、カスタマー マネージド キーまたは Bring Your Own Key (BYOK) を Azure Media Services ストレージ アカウントで使用できるようにします。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 1a5b4c8e387085b8fee6c6e82ae765a186f43e87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356668"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>チュートリアル:Azure portal を使用して Media Services でカスタマー マネージド キーまたは BYOK を使用する

2020-05-01 以降のバージョンの API を使用すると、システム マネージド ID が割り当てられた Azure Media Services アカウントでカスタマー マネージド RSA キーを使用できます。このチュートリアルでは、Azure portal での手順を取り上げます。

次のサービスが使用されます。

- Azure Storage
- Azure Key Vault
- Azure Media Services

このチュートリアルでは、Azure portal を使用して次のことを行います。

> [!div class="checklist"]
> - リソース グループを作成する。
> - システム マネージド ID が割り当てられたストレージ アカウントを作成する。
> - システム マネージド ID が割り当てられた Media Services アカウントを作成する。
> - カスタマー マネージド RSA キーを格納するためのキー コンテナーを作成する。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプション。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free/)。

## <a name="system-managed-keys"></a>システム マネージド キー

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> 以降のストレージ アカウント作成手順では、[Advanced settings]\(詳細設定\) でシステム マネージド キーを選択します。

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> 以降のストレージ暗号化手順では、**カスタマー マネージド キー** を選択します。

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>キーを変更する

キーが変更されると、Media Services によって自動的に検出されます。 省略可能:このプロセスをテストするには、同じキーの別のキー バージョンを作成します。 キーの変更が Media Services によって検出されるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを今後使用せず、*課金が継続されないようにする* 場合は、削除してください。

## <a name="next-steps"></a>次のステップ

次の記事に進み、以下の方法を学習してください。
> [!div class="nextstepaction"]
> [REST を使用して、リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する](stream-files-tutorial-with-rest.md)
