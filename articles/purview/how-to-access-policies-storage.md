---
title: Azure Storage のデータ アクセス ポリシー プロビジョニング
description: Azure Storage を Azure Purview アクセス ポリシーに統合し、データ所有者がアクセス ポリシーを作成する方法に関するステップバイステップ ガイド。
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/15/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eab2c720aafe2cfd5a1ca46f2549b42d6f644b25
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555719"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Azure Storage のデータ所有者によるデータセットのプロビジョニング

## <a name="supported-capabilities"></a>サポートされる機能
このガイドでは、Azure Purview から作成および管理されるデータ アクセス ポリシーを適用するように Azure Storage を構成する方法について説明します。 Azure Purview ポリシーの作成では、次の機能がサポートされています。
-   BLOB または Azure Data Lake Storage (ADLS) Gen2 ファイルに格納されているデータへのアクセスを制御するデータ アクセス ポリシー

> [!IMPORTANT]
> これらの機能は現在、プレビュー段階にあります。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="important-limitations"></a>重要な制限事項
1. アクセス ポリシー機能は、新しい Azure Purview アカウントと Azure Storage アカウントのみで利用できます。
2. ガバナンスを使用するためにすべてのデータ ソースを登録し、関連付けられているすべてのアクセス ポリシーを 1 つの Azure Purview アカウントで管理します。
3. この機能は、アクセス ポリシーの管理と適用機能がデプロイされている、以下のリストに含まれているリージョンでのみ使用できます。

### <a name="supported-regions"></a>サポートされているリージョン

#### <a name="azure-purview-management-side"></a>Azure Purview (管理側)
-   北ヨーロッパ
-   西ヨーロッパ
-   英国南部
-   米国東部
-   米国東部 2
-   米国中南部
-   米国西部 2
-   東南アジア
-   オーストラリア東部
-   カナダ中部
-   フランス中部

#### <a name="azure-storage-enforcement-side"></a>Azure Storage (適用側)
-   フランス中部
-   カナダ中部

## <a name="prerequisites"></a>必須コンポーネント
> [!Important]
> このセクションの内容を慎重にお読みください。 アクセス ポリシーが正しく機能するには、複数の前提条件があります。

### <a name="select-an-isolated-test-subscription"></a>分離されたテスト サブスクリプションを選択する
分離されたテスト サブスクリプションを作成または使用し、次の手順に従ってそのサブスクリプションに新しい Azure Storage アカウントと新しい Azure Purview アカウントを作成します。

### <a name="create-azure-storage-account"></a>Azure ストレージ アカウントを作成する
上記 (制限事項の下) のリストに含まれるリージョンに、新しい Azure Storage アカウントを作成します。 [Azure Storage の 「ストレージ アカウントを作成する」](../storage/common/storage-account-create.md)を参照してください。

### <a name="configure-azure-storage-to-enforce-access-policies-from-purview"></a>Purview からアクセス ポリシーを適用するように Azure Storage を構成する

#### <a name="enable-access-policy-enforcement-in-the-subscription"></a>サブスクリプションでアクセス ポリシーの適用を有効にする
Azure Storage アカウントが存在するサブスクリプションでアクセス ポリシー機能が有効になっているのを登録して確認するには、PowerShell で次のコマンドを実行します。

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
最後のコマンドの出力に "RegistrationState" の値が "Registered" と表示されている場合、この機能に対するサブスクリプションは有効になっています。

#### <a name="check-access-permissions-in-azure-storage"></a>Azure Storage でアクセス許可を確認する
ユーザーは、アクセス ポリシー用に後でこのデータ ソースを Azure Purview に登録するには、Azure Storage アカウントで "所有者" ロールを持っている必要があります。「[Azure リソースに対するユーザーのアクセス権を確認する](../role-based-access-control/check-access.md)」

### <a name="create-azure-purview-account"></a>Azure Purview アカウントを作成する
分離されたテスト サブスクリプションの下で、新しい機能が有効になっているリージョンに、新しい Azure Purview アカウントを作成します。 新しい Purview アカウントを作成するには、「[クイック スタート: Azure portal で Azure Purview アカウントを作成する](create-catalog-portal.md)」を参照してください。

### <a name="configure-azure-purview-to-manage-access-policies"></a>アクセス ポリシーを管理するように Azure Purview を構成する
Azure Purview でアクセス ポリシーを管理するには、次のすべての手順を行います 

#### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Azure Purview データ使用ポリシー プレビューに参加するためのオプトイン
この機能は現在プレビュー段階であり、[Purview データ使用ポリシー プレビューにオプトイン](https://aka.ms/opt-in-data-use-policy)する必要があります。

#### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>Purview を他のサブスクリプションのリソース プロバイダーとして登録する
この手順は、アクセスを管理するストレージ アカウントが Azure Purview アカウントとは異なるサブスクリプションにある場合にのみ実行します。 このガイドに従って、Azure Purview をそのサブスクリプションのリソース プロバイダーとして登録します。  
[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)

#### <a name="configure-permissions-for-policy-management-actions"></a>ポリシー管理アクションのアクセス許可を構成する
- データ使用ガバナンスのためのソースを登録するには、ユーザーがデータソースの所有者で、なおかつ Purview データ ソース管理者である必要があります。 ただし、これらのロールのどれでも単独で、データ使用ガバナンスのためソースの登録を解除できます。
- ポリシーの作成または管理アクションを実行するためには、ユーザーが、ルート コレクション レベルで Purview ポリシー作成者ロールの一員である必要があります。
- ポリシーを発行するためには、ユーザーが、ルート コレクション レベルで Purview データ ソース管理者ロールの一員である必要があります。

このガイドのロールの割り当ての管理に関するセクションを参照してください。[コレクションを作成および管理する方法](how-to-create-and-manage-collections.md)

これらに加えて、このドキュメントの下方にある「既知の問題」セクションを参照してください。

#### <a name="register-and-scan-data-sources-in-purview"></a>Purview でデータ ソースを登録してスキャンする
Purview に各データ ソースを登録してスキャンし、後でアクセス ポリシーを定義します。 Purview 登録ガイドに従って、ストレージ アカウントを登録します。

-   [Azure Storage Blob を登録してスキャンする方法 - Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Azure Data Lake Storage (ADLS) Gen2 の登録とスキャン - Azure Purview](register-scan-adls-gen2.md)

登録時に、図に示されているように、**データ使用ガバナンス** トグルを使用してデータ ソースのアクセス ポリシーを有効にします。

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="ポリシーのデータ ソースを登録する方法を示す画像。":::

> [!NOTE]
> トグルの動作により、特定のサブスクリプションのすべてのデータ ソースは、1 つの Purview アカウントのデータ使用ガバナンスにのみ登録できるようになります。 その Purview アカウント自体は、テナント内の任意のサブスクリプションに含まれている可能性があります。

## <a name="policy-authoring"></a>ポリシーの作成

このセクションでは、Purview アクセス ポリシーの作成、更新、公開を行う手順について説明します。

### <a name="create-a-new-policy"></a>新しいポリシーの作成

このセクションでは、Azure Purview で新しいポリシーを作成する手順について説明します。

1. Purview ポータルにログインします。

1. 左側のパネルを使用して、 **[ポリシー管理]** アプリに移動します。

1. ポリシー ページで **[新しいポリシー]** ボタンを選択します。

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="データ所有者がポリシーを作成するときに Azure Purview のポリシー機能にアクセスする方法を示す画像。":::

1. 新しいポリシー ページが表示されます。 ポリシーの **[名前]** と **[説明]** を入力します。

1. 新しいポリシーにポリシー ステートメントを追加するには、 **[New policy statement]\(新しいポリシー ステートメント\)** ボタンを選択します。 これにより、ポリシー ステートメント ビルダーが表示されます。

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="データ所有者が新しいポリシー ステートメントを作成する方法を示す画像。":::

1. **[アクション]** ボタンを選択し、ドロップダウン リストから [読み取り] または [変更] を選択します。

1. **[Effect]\(効果\)** ボタンを選択し、ドロップダウン リストから [許可] を選択します。

1. **[データ リソース]** ボタンを選択して、データ資産のパスを指定するオプションを表示します

1. **[資産]** ボックスに「**データ ソースの種類**」と入力し、以前に登録したデータ ソースの **名前** を選択します。

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="ポリシー ステートメントを編集するときにデータ所有者がデータ リソースを選択する方法を示す画像。":::

1. **[続行]** ボタンを選択し、階層を展開してフォルダーまたはファイルを選択します。 次に **[追加]** ボタンを選択します。 これにより、ポリシー エディターに戻ります。

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="ポリシー ステートメントを作成または編集するときに、データ所有者が資産を選択する方法を示す画像。":::

1. **[サブジェクト]** ボタンを選択し、プリンシパル、グループ、または MSI としてサブジェクト ID を入力します。 次に、**[OK]** ボタンを選択します。 これにより、ポリシー エディターに戻ります

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="ポリシー ステートメントを作成または編集するときに、データ所有者がサブジェクトを選択する方法を示す画像。":::

1. 手順 5 から 11 を繰り返して、ほかのポリシー ステートメントを入力します。

1. **[保存]** ボタンを選択してポリシーを保存します

### <a name="update-or-delete-a-policy"></a>ポリシーを更新または削除する

Purview で新しいポリシーを作成する手順は次のとおりです。

1. Purview ポータルにログインします。

1. 左側のパネルを使用して Purview ポリシー アプリに移動します。

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="データ所有者がポリシーを更新するときに Azure Purview のポリシー機能にアクセスする方法を示す画像。":::

1. ポリシー ポータルには、Purview に存在するポリシーの一覧が表示されます。 更新する必要があるポリシーを選択します。

1. [編集] オプションや [削除] オプションなど、ポリシーの詳細ページが表示されます。 **[編集]** ボタンを選択すると、このポリシー内のステートメントのポリシー ステートメント ビルダーが表示されます。 これで、このポリシー内のステートメントの任意の部分を更新できます。 ポリシーを削除するには、 **[削除]** ボタンを使用します。

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="データ所有者がポリシー ステートメントを編集または削除する方法を示す画像。":::

### <a name="publish-the-policy"></a>ポリシーを公開する

新しく作成されたポリシーは下書き状態です。 公開プロセスでは、ガバナンスの対象となっている 1 つ以上のデータ ソースに新しいポリシーが関連付けされます。
ポリシーを公開する手順は次のとおりです

1. Purview ポータルにログインします。

1. 左側のパネルを使用して Purview ポリシー アプリに移動します。

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="データ所有者がポリシーを公開するときに Azure Purview のポリシー機能にアクセスする方法を示す画像。":::

1. ポリシー ポータルには、Purview に存在するポリシーの一覧が表示されます。 公開する必要があるポリシーを見つけます。 ページの右上隅にある **[Publish]\(公開\)** ボタンを選択します。

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="データ所有者がポリシーを公開する方法を示す画像。":::

1. データ ソースの一覧が表示されます。 名前を入力してリストをフィルター処理することができます。 次に、このポリシーを公開する各データ ソースを選択し、 **[Publish]\(公開\)** ボタンを選択します。

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="ポリシーが公開されるデータ ソースをデータ所有者が選択する方法を示す画像。":::

>[!NOTE]
> 公開はバックグラウンド操作です。 変更がデータ ソースに反映されるには、最大で **2 時間** かかる場合があります。

## <a name="azure-purview-policy-action-to-azure-storage-action-mapping"></a>Azure Storage アクション マッピングに対する Azure Purview ポリシー アクション

このセクションでは、Azure Purview データ ポリシーのアクションが、Azure Storage データ ポリシー内の特定のアクションにどのようにマップされるのかについてのリファレンスを含みます。

| **Purview ポリシー アクション** | **データ ソース固有のアクション**                                                        |
|---------------------------|-----------------------------------------------------------------------------------------|
|||
| *読み取り*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|||
| *Modify*                  |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write               |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action          |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action         |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete              |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/write                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/delete                    |
|||

## <a name="known-issues"></a>既知の問題
以下に、現在のリリースでの既知の問題を示します
1. ユーザーは、データ所有者ポリシーを作成するには、ポリシー作成者ロールに加えて Azure Active Directory (AAD) でのディレクトリ閲覧者アクセス許可を持っている必要があります。
1. ポリシーを作成するには、ポリシー作成者ロールでは十分ではありません。 Purview データ ソース管理者ロールも必要になります。

## <a name="next-steps"></a>次の手順
この攻略ガイドで説明されている機能に関連するブログとデモをご確認ください

* [Microsoft Ignite 2021 で紹介された Azure Purview の新機能](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Azure Storage のアクセス ポリシーのデモ](https://www.youtube.com/watch?v=CFE8ltT19Ss)
