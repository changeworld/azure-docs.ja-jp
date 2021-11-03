---
title: スキャン用の資格情報を作成して管理する
description: Azure Purview で資格情報を作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdce380d09cc2992f4e77f9385b1d176a6ae68eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076663"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview でのソース認証用の資格情報

この記事では、Azure Purview で資格情報を作成する方法について説明します。 保存した資格情報を使用すると、保存された認証情報を簡単に再利用し、データ ソース スキャンに適用できます。

## <a name="prerequisites"></a>前提条件

- Azure Key Vault。 作成方法については、「[クイック スタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。

## <a name="introduction"></a>はじめに

資格情報は、Azure Purview で、登録済みのデータ ソースに対する認証に使用できる認証情報です。 資格情報オブジェクトは、さまざまな種類の認証シナリオ (ユーザー名とパスワードを必要とする基本認証など) のために作成できます。 資格情報では、選択した認証方法の種類に基づいて、認証に必要な特定の情報がキャプチャされます。 資格情報の作成プロセス中は、機密性の高い認証情報を取得するために Azure Key Vault の既存のシークレットが使用されます。

Azure Purview では、次のオプションのようなデータ ソースをスキャンするための認証方法として使用するオプションはほとんどありません。

- Azure Purview マネージド ID
- アカウントキー (キー コンテナーを使用)
- SQL 認証 (キー コンテナーを使用)
- サービス プリンシパル (キー コンテナーを使用)

資格情報を作成する前に、データソースの種類とネットワークの要件を考慮して、シナリオに必要な認証方法を決定してください。 次のデシジョン ツリーを確認して、最も適切な資格情報を見つけます。

   :::image type="content" source="media/manage-credentials/manage-credentials-decision-tree-small.png" alt-text="資格情報デシジョンツリーの管理" lightbox="media/manage-credentials/manage-credentials-decision-tree.png":::

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Purview マネージド ID を使用したスキャンの設定

Purview マネージド ID を使用してスキャンを設定する場合は、資格情報を明示的に作成し、キー コンテナーを Purview にリンクしてそれらを格納する必要はありません。 Purview マネージド ID を追加してデータ ソースのスキャンを利用できるようにする詳細な手順については、以下のデータ ソース固有の認証に関するセクションをご覧ください。

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Purview アカウントに Azure Key Vault の接続を作成する

資格情報を作成する前に、まず既存の Azure Key Vault インスタンスの 1 つまたは複数を Azure Purview アカウントに関連付けます。

1. [Azure portal](https://portal.azure.com) から、Azure Purview アカウントを選択して [Purview Studio](https://web.purview.azure.com/resource/) を開きます。 Studio で **[管理センター]** に移動してから、**資格情報** に移動します。

2. **[資格情報]** ページで、 **[Key Vault 接続の管理]** を選択します。

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault 接続を管理する":::

3. [Key Vault 接続の管理] ページで **[+ 新規]** を選択します。

4. 必要な情報を入力して、 **[作成]** を選択します。

5. この例に示すように、Key Vault が Azure Purview アカウントに正常に関連付けられていることを確認します。

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="確認のために Azure Key Vault 接続を表示します。":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Purview マネージド ID に Azure Key Vault へのアクセス権を付与する

現在 Azure Key Vault では、次の 2 つのアクセス許可モデルがサポートされています。

- オプション 1 - アクセス ポリシー 
- オプション 2 - ロールベースのアクセス制御 

Purview マネージド ID へのアクセス権の割り当てを行う前に、まず、メニューにある、Key Vault リソースの **[アクセス ポリシー]** から自分の Azure Key Vault のアクセス許可モデルを識別してください。 関連するアクセス許可モデルに基づいて、次の手順を行います。  

:::image type="content" source="media/manage-credentials/akv-permission-model.png" alt-text="Azure Key Vault アクセス許可モデル"::: 

### <a name="option-1---assign-access-using-key-vault-access-policy"></a>オプション 1 - Key Vault アクセス ポリシーを使用してアクセス権を割り当てる  

ご利用の Azure Key Vault リソース内のアクセス許可モデルが **コンテナー アクセス ポリシー** に設定されている場合にのみ、次の手順に従います。

1. お使いの Azure Key Vault に移動します。

2. **[アクセス ポリシー]** ページを選択します。

3. **[アクセス ポリシーの追加]** を選択します。

   :::image type="content" source="media/manage-credentials/add-msi-to-akv-2.png" alt-text="AKV への Purview MSI の追加":::

4. **[シークレットのアクセス許可]** ドロップダウンで、 **[取得]** および **[一覧]** のアクセス許可を選択します。

5. **[プリンシパルの選択]** で、Purview マネージド ID を選択します。 Purview インスタンス名 **または** マネージ ID アプリケーション ID を使用して、Purview MSI を検索できます。 現在、複合 ID (マネージド ID 名 + アプリケーション ID) はサポートされていません。

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="アクセス ポリシーの追加":::

6. **[追加]** を選択します。

7. **[保存]** を選択してアクセス ポリシーを保存します。

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="アクセス ポリシーの保存":::

### <a name="option-2---assign-access-using-key-vault-azure-role-based-access-control"></a>オプション 2 - Key Vault の Azure ロールベースのアクセス制御を使用してアクセス権を割り当てる 

ご利用の Azure Key Vault リソース内のアクセス許可モデルが **Azure ロールベースのアクセス制御** に設定されている場合にのみ、次の手順に従います。

1. お使いの Azure Key Vault に移動します。

2. 左側のナビゲーション メニューから **[アクセス制御 (IAM)]** を選択します。

3. **[+ 追加]** を選択します。

4. **[ロール]** を **[Key Vault Secrets User]** に設定し、 **[選択]** 入力ボックスに、ご利用の Azure Purview アカウント名を入力してください。 次に、[保存] を選択して、このロールの割り当てを、自分の Purview アカウントに付与します。

   :::image type="content" source="media/manage-credentials/akv-add-rbac.png" alt-text="Azure Key Vault RBAC":::


## <a name="create-a-new-credential"></a>新しい資格情報を作成する

これらの資格情報の種類が Purview でサポートされています。

- 基本認証:**パスワード** をシークレットとしてキー コンテナーに追加します。
- サービス プリンシパル:**サービス プリンシパルのキー** をシークレットとしてキー コンテナーに追加します。
- SQL 認証:**パスワード** をシークレットとしてキー コンテナーに追加します。
- アカウント キー:**アカウント キー** をシークレットとしてキー コンテナーに追加します。
- ロール ARN: Amazon S3 データ ソースの場合は、AWS に **ロール ARN** を追加します。 

詳細については、「[Key Vault にシークレットを追加する](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)」および「[Purview 用の新しい AWS ロールを作成する](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)」を参照してください。

シークレットをキー コンテナーに格納した後、次のようにします。

1. Azure Purview で、[資格情報] ページに移動します。

2. **[+ 新規]** を選択して、新しい資格情報を作成します。

3. 必要な情報を入力します。 **[認証方法]** と、シークレットの選択元となる **[Key Vault インスタンス]** を選択します。

4. すべての詳細の入力が完了したら、 **[作成]** を選択します。

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新しい資格情報":::

5. 新しい資格情報がリスト ビューに表示され、使用する準備ができていることを確認します。

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="資格情報の表示":::

## <a name="manage-your-key-vault-connections"></a>キー コンテナーの接続を管理する

1. Key Vault 接続を名前で検索します

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Key Vault の検索":::

2. 1 つ以上の Key Vault 接続を削除します

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="キー コンテナーを削除する":::

## <a name="manage-your-credentials"></a>資格情報を管理する

1. 資格情報を名前で検索します。
  
2. 既存の資格情報を選択して更新します。

3. 1 つ以上の資格情報を削除します。

## <a name="next-steps"></a>次のステップ

[スキャン ルール セットを作成する](create-a-scan-rule-set.md)
