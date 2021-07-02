---
title: Media Services マネージド ID を使用してストレージにアクセスする
description: 不明な IP アドレスからの要求をブロックするようにストレージ アカウントが構成されている場合に、そのストレージ アカウントにアクセスするには、Media Services アカウントにそのストレージ アカウントへのアクセスを許可する必要があります。 下の手順に従って、Media Services アカウントのマネージド ID を作成し、Media Services CLI を使用してこの ID にストレージへのアクセスを許可します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 77eb737a48213701e424bbc8b1a49ead0c95e88c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464297"
---
# <a name="tutorial-access-storage-with-a-media-services-managed-identity"></a>チュートリアル: Media Services マネージド ID を使用してストレージにアクセスする

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

不明な IP アドレスからの要求をブロックするようにストレージ アカウントが構成されている場合に、そのストレージ アカウントにアクセスするには、Media Services アカウントにそのストレージ アカウントへのアクセスを許可する必要があります。 下の手順に従って、Media Services アカウントのマネージド ID を作成し、Media Services CLI を使用してこの ID にストレージへのアクセスを許可します。

:::image type="content" source="media/diagrams/managed-identities-scenario-storage-permissions-media-services-account.svg" alt-text="Media Services アカウントは、マネージド ID を使用してストレージにアクセスします":::

このチュートリアルでは、2020-05-01 Media Services API を使用します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

この記事のいずれかのコマンドを使用するには、まず、使用するサブスクリプションにサインインする必要があります。

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>サブスクリプションの設定

このコマンドを使用して、使用するサブスクリプションを設定します。

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>リソース名

作業を開始する前に、作成するリソースの名前を決定します。  これらは、テストの完了後に使用する予定がない場合は特に、セットとして簡単に識別できる必要があります。 名前付けルールは多くのリソースの種類で異なるため、すべて小文字を使用することをお勧めします。 たとえば、リソース グループ名は "mediatest1rg"、ストレージ アカウント名は "mediatest1stor" にします。 この記事の各手順では、同じ名前を使用します。

これらの名前は、下のコマンドで参照されます。  必要なリソースの名前は次のとおりです。

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region

> [!NOTE]
> 上記のハイフンは、ガイダンス ワードを分離するためにのみ使用されています。 Azure サービスではリソースの名前付けに一貫性がないため、リソースに名前を付けるときにハイフンを使用しないでください。
> また、リージョン名は作成しないでください。  リージョン名は Azure によって決定されます。

### <a name="list-azure-regions"></a>Azure リージョンの一覧表示

使用する実際のリージョン名が不明な場合は、次のコマンドを使用して一覧を取得します。

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>シーケンス

JSON 応答から 1 つ以上の値がシーケンスの次のステップで使用されるため、下の各ステップは特定の順序で実行されます。

## <a name="create-a-resource-group"></a>リソース グループを作成する

作成するリソースは、リソース グループに属している必要があります。 まず、リソース グループを作成します。 Media Services アカウントの作成ステップと後続のステップには、`your-resource-group-name` を使用します。

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

作成する Media Services アカウントには、ストレージ アカウントが関連付けられている必要があります。 最初に、Media Services アカウントのストレージ アカウントを作成します。 以降のステップには、`your-storage-account-name` を使用します。

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>サービス プリンシパル (マネージド ID) を使用して Media Services アカウントを作成する

ここで、サービス プリンシパル (マネージド ID とも呼ばれます) を使用して、Media Services アカウントを作成します。

> [!IMPORTANT]
> コマンドでは --mi フラグを必ず使用してください。  そうしないと、後のステップで `principalId` を見つけることができなくなります。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="grant-the-media-services-managed-identity-access-to-the-storage-account"></a>Media Services マネージド ID にストレージ アカウントへのアクセスを許可する

Media Services マネージド ID にストレージ アカウントへのアクセスを許可します。 3 つのコマンドがあります。

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Media Services アカウントのマネージド ID を取得 (表示) する

下の最初のコマンドは、Media Services アカウントのマネージド ID を表示します。これはコマンドによって返される JSON で一覧表示される `principalId` です。

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="create-the-storage-blob-contributor-role-assignment"></a>ストレージ BLOB 共同作成者ロールの割り当てを作成する

[!INCLUDE [Create the Storage Blob Contributor role assignment](./includes/task-create-storage-blob-contributor-role-cli.md)]

### <a name="create-the-reader-role-assignment"></a>閲覧者ロールの割り当てを作成する

[!INCLUDE [Create the Reader role assignment](./includes/task-create-reader-role-cli.md)]

## <a name="use-the-managed-identity-to-access-the-storage-account"></a>マネージド ID を使用してストレージ アカウントにアクセスする

[!INCLUDE [Use the Managed Identity to access the Storage account](./includes/task-set-storage-managed-identity-cli.md)]

## <a name="validation"></a>検証

アカウントがカスタマー マネージド キーを使用して暗号化されていることを確認するには、アカウントの暗号化プロパティを表示します。

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-managed-identity-cli.md)]

`storageAuthentication` プロパティに、"ManagedIdentity" が表示されるはずです。

追加の検証を行うには、Azure Storage ログを確認して、各要求に対してどの認証方法が使用されているかを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを使用する予定がない場合は、そのリソース グループを削除します。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
