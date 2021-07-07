---
title: Key Vault キーを使用して Media Services アカウントにデータを暗号化する
description: Media Services で Key Vault のキーを使用してデータを暗号化する場合は、Media Services アカウントに Key Vault への ''*アクセス*'' が許可されている必要があります。 下の手順に従って、Media Services アカウントのマネージド ID を作成し、Media Services CLI を使用してこの ID に Key Vault へのアクセスを許可します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: inhenkel
ms.openlocfilehash: 20b0ec0025fcac72005eb8b2343ede0ef9e5d9b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464333"
---
# <a name="tutorial-use-a-key-vault-key-to-encrypt-data-into-a-media-services-account"></a>チュートリアル: Key Vault キーを使用して Media Services アカウントにデータを暗号化する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services で Key Vault のキーを使用してデータを暗号化する場合は、Media Services アカウントに Key Vault への ''*アクセス*'' が許可されている必要があります。 下の手順に従って、Media Services アカウントのマネージド ID を作成し、Media Services CLI を使用してこの ID に Key Vault へのアクセスを許可します。

:::image type="content" source="media/diagrams/managed-identities-scenario-keyvault-media-services-account.svg" alt-text="マネージド ID を使って、Media Services アカウントで Key Vault を使用する":::



このチュートリアルでは、2020-05-01 Media Services API を使用します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

この記事のいずれかのコマンドを使用するには、まず、使用するサブスクリプションにサインインする必要があります。

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>サブスクリプションの設定

このコマンドを使って、使用するサブスクリプションを設定します。

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>リソース名

作業を開始する前に、作成するリソースの名前を決定します。  これらは、テストの完了後に使用する予定がない場合は特に、セットとして簡単に識別できる必要があります。 名前付けルールは多くのリソースの種類で異なるため、すべて小文字を使用することをお勧めします。 たとえば、リソース グループ名は "mediatest1rg"、ストレージ アカウント名は "mediatest1stor" にします。 この記事の各手順では、同じ名前を使用します。

これらの名前は、下のコマンドで参照されます。  必要なリソースの名前は次のとおりです。

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-keyvault-name
- your-key-name
- your-region

> [!NOTE]
> 上記のハイフンは、ガイダンス ワードを分離するためにのみ使用されています。 Azure サービスではリソースの名前付けに一貫性がないため、リソースに名前を付けるときにハイフンを使用しないでください。
> また、リージョン名は作成しないでください。  リージョン名は Azure によって決定されます。

### <a name="list-azure-regions"></a>Azure リージョンを一覧表示する

使用する実際のリージョン名が不明な場合は、次のコマンドを使用して一覧を取得します。

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>シーケンス

JSON 応答から 1 つまたは複数の値がシーケンスの次の手順で使用されるため、下の各手順は特定の順序で行われます。

## <a name="create-a-resource-group"></a>リソース グループを作成する

作成するリソースは、リソース グループに属している必要があります。 まず、リソース グループを作成します。 Media Services アカウントの作成手順と後続の手順には、`your-resource-group-name` を使用します。

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

作成する Media Services アカウントには、ストレージ アカウントが関連付けられている必要があります。 最初に、Media Services アカウントのストレージ アカウントを作成します。 以降の手順には、`your-storage-account-name` を使用します。

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>サービス プリンシパル (マネージド ID) を使用して Media Services アカウントを作成する

ここで、サービス プリンシパル (マネージド ID ともいう) を使用して、Media Services アカウントを作成します。

> [!IMPORTANT]
> コマンドでは --mi フラグを必ず使用してください。  そうしないと、後の手順で `principalId` を見つけることができなくなります。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="create-a-key-vault"></a>Key Vault の作成

Key Vault を作成します。  この Key Vault はメディア データの暗号化に使用されます。 `your-keyvault-name` を使用してキーを作成し、後の手順で使用します。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-key-vault-cli.md)]

## <a name="grant-the-media-services-system-assigned-managed-identity-access-to-the-key-vault"></a>Media Services システム割り当てマネージド ID に Key Vault へのアクセスを許可する

Media Services マネージド ID に Key Vault へのアクセスを許可します。 次の 2 つのコマンドがあります。

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Media Services アカウントのマネージド ID を取得 (表示) する

下の最初のコマンドは、Media Services アカウントのマネージド ID を示しています。これは、コマンドによって返される JSON で一覧表示される `principalId` です。

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="set-the-key-vault-policy"></a>Key Vault ポリシーを設定する

2 番目のコマンドでは、プリンシパル ID に Key Vault へのアクセスを許可します。 `object-id` は、前の手順で取得した `principalId` の値に設定します。

[!INCLUDE [Set the Key Vault policy with the CLI](./includes/task-set-key-vault-policy-cli.md)]

### <a name="set-media-services-to-use-the-key-from-key-vault"></a>Key Vault のキーを使用するように Media Services を設定する

作成したキーを使用するように Media Services を設定します。 `key-identifier` プロパティの値は、キーの作成時に出力から取得されます。 アクセス制御の変更を伝達するのに時間がかかるため、このコマンドは失敗する可能性があります。 そのような場合は、数分後に再試行してください。

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-set-encryption-cli.md)]

## <a name="validation"></a>検証

アカウントがカスタマー マネージド キーを使用して暗号化されていることを確認するには、アカウントの暗号化プロパティを表示します。

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-encryption-cli.md)]

`type` プロパティでは `CustomerKey` が示され、`currentKeyIdentifier` は顧客の Key Vault 内のキーのパスに設定されている必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを使用する予定がない場合は、そのリソース グループを削除します。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
