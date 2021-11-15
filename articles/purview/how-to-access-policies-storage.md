---
title: Azure Storage のデータ アクセス ポリシー プロビジョニング
description: Azure Storage を Azure Purview アクセス ポリシーに統合し、データ所有者がアクセス ポリシーを作成する方法に関するステップバイステップ ガイド。
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ea5285c5fd29bfe34f97c87b2ac0c9bd7a5502a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425237"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Azure Storage のデータ所有者によるデータセットのプロビジョニング

## <a name="supported-capabilities"></a>サポートされる機能

Purview ポリシーの作成では、次の機能がサポートされています。
-   BLOB または ADLS Gen2 ファイルに格納されているデータへのアクセスを制御する Azure Storage のデータ アクセス ポリシー

> [!IMPORTANT]
> これらの機能は現在、プレビュー段階にあります。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



## <a name="prerequisites"></a>前提条件

### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Azure Purview データ使用ポリシー プレビューに参加するためのオプトイン
この機能は現在プレビュー段階であり、[Purview データ使用ポリシー プレビューにオプトイン](https://aka.ms/opt-in-data-use-policy)する必要があります。

### <a name="provision-new-accounts-in-an-isolated-test-subscription"></a>分離テスト サブスクリプションで新しいアカウントをプロビジョニングする
次の手順に従って、分離テスト サブスクリプションに新しい Azure Purview アカウントと新しい Azure Storage アカウントを作成します。 次に、これらのアカウントのアクセス ポリシー機能を有効にします。

### <a name="supported-regions"></a>サポートされているリージョン

> [!IMPORTANT]
> 1. アクセス ポリシー機能は、新しい Azure Purview アカウントと Azure Storage アカウントのみで利用できます。
> 2. この機能は、アクセス ポリシー機能がデプロイされている以下のリージョンでのみ使用できます。

#### <a name="azure-purview"></a>Azure Purview 

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


#### <a name="azure-storage"></a>Azure Storage

-   フランス中部
-   カナダ中部


### <a name="create-azure-purview-account"></a>Azure Purview アカウントを作成する

新しい機能用に分離されたサブスクリプションの下で、新しい機能が有効になっているリージョンに、新しい Azure Purview アカウントを作成します。

新しい Purview アカウントを作成するには、「[クイック スタート: Azure portal で Azure Purview アカウントを作成する](create-catalog-portal.md)」を参照してください。

### <a name="create-azure-storage-account"></a>Azure ストレージ アカウントを作成する

新しい Azure Storage アカウントを作成するには、「[ストレージ アカウントの作成 - Azure Storage](../storage/common/storage-account-create.md)」を参照してください

### <a name="configure-azure-purview-and-storage-for-access-policies"></a>アクセス ポリシー用に Azure Purview および Storage を構成する

このセクションでは、アクセス ポリシーを有効にするために Azure Purview と Storage を構成する手順について説明します。

#### <a name="register-the-access-policies-functionality-in-azure-storage"></a>Azure Storage でアクセス ポリシー機能を登録する

この機能がサブスクリプションで有効になるよう登録して確認するには、PowerShell で次のコマンドを実行します

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

最後のコマンドの出力に "RegistrationState" の値が "Registered" と表示されている場合、この機能に対するサブスクリプションは有効になっています。

#### <a name="register-and-scan-data-sources-in-purview"></a>Purview でデータ ソースを登録してスキャンする

ポリシーを定義するには、データ ソースを Purview に登録してスキャンする必要があります。 Purview 登録ガイドに従って、ストレージ アカウントを登録します。

-   [Azure Storage BLOB をスキャンする方法 - Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Azure Data Lake Storage (ADLS) Gen2 の登録とスキャン - Azure Purview](register-scan-adls-gen2.md)

登録時に、図に示すように、データ使用ガバナンスのデータ ソースを有効にします。

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="ポリシーのデータ ソースを登録する方法を示す画像。":::

#### <a name="configure-permissions-for-policy-management-actions"></a>ポリシー管理アクションのアクセス許可を構成する

-   ポリシーの作成および管理アクションを実行するには、ユーザーは Purview データ キュレーター ロールの一部である必要があります。
-   ポリシーを公開するには、ユーザーは Purview データ ソース管理者ロールの一部である必要があります。

このガイドのロールの割り当ての管理に関するセクションを参照してください。[コレクションを作成および管理する方法](how-to-create-and-manage-collections.md)

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

1. データ ソースの一覧が表示されます。 名前を入力してリストをフィルター処理することができます。 次に、このポリシーを公開する各データ ソースを選択し、 **[Publish]\(公開\)** ボタンを選択します。 公開はバックグラウンド操作です。 変更がデータ ソースに反映されるには、最大で 2 時間かかる場合があります。

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="ポリシーが公開されるデータ ソースをデータ所有者が選択する方法を示す画像。":::

## <a name="next-steps"></a>次のステップ

この記事をチェックして Azure Purview に関連した概念を理解します。

* [Azure Purview の概要](overview.md)
