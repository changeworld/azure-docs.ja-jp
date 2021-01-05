---
title: スキャン用の資格情報を作成して管理する
description: この記事では、Azure Purview で資格情報を作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 30004306d9ff44df04a26640a2bd7a09256fce25
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516686"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview でのソース認証用の資格情報

この記事では、Azure Purview で資格情報を作成して、保存された認証情報をすばやく再利用したり、データ ソースのスキャンに適用したりする方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure Key Vault。 作成方法については、「[クイック スタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。

## <a name="introduction"></a>はじめに
資格情報は、Azure Purview で、登録済みのデータ ソースに対する認証に使用できる認証情報です。 資格情報オブジェクトは、さまざまな種類の認証シナリオ (ユーザー名とパスワードを必要とする基本認証など) のために作成でき、選択した認証方法の種類に基づいて必要な特定の情報がキャプチャされます。 資格情報の作成プロセス中は、機密性の高い認証情報を取得するために Azure Key Vault の既存のシークレットが使用されます。

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Purview マネージド ID を使用したスキャンの設定
Purview マネージド ID を使用してスキャンを設定する場合は、資格情報を明示的に作成し、キー コンテナーを Purview にリンクしてそれらを格納する必要はありません。 Purview マネージド ID を追加してデータ ソースのスキャンを利用できるようにする詳細な手順については、以下のデータ ソース固有の認証に関するセクションをご覧ください。

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Purview アカウントに Azure Key Vault の接続を作成する

資格情報を作成する前に、まず既存の Azure Key Vault インスタンスの 1 つまたは複数を Azure Purview アカウントに関連付ける必要があります。

1. Azure Purview ナビゲーション メニューで、[管理センター] に移動してから、資格情報に移動します

2. [資格情報] のコマンド バーで、[Key Vault 接続の管理] を選択します

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="AKV 接続の管理":::

3. [Key Vault 接続の管理] パネルで [+ 新規] を選択します 

4. 必要な情報を入力して、[作成] を選択します

5. Key Vault が Azure Purview アカウントに正常に関連付けられていることを確認します

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="AKV 接続の表示":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Purview マネージド ID に Azure Key Vault へのアクセス権を付与する

お使いのキー コンテナー -> [アクセス ポリシー] -> [アクセス ポリシーの追加] の順に移動します。 [シークレットのアクセス許可] ドロップダウンで [Get] アクセス許可を付与し、プリンシパルが Purview MSI になるように選択します。 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="AKV への Purview MSI の追加":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="アクセス ポリシーの追加":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="アクセス ポリシーの保存":::

## <a name="create-a-new-credential"></a>新しい資格情報を作成する

Purview で現在サポートされている資格情報の種類は次のとおりです。
* 基本認証: **パスワード** をシークレットとしてキー コンテナーに追加します
* サービス プリンシパル: **サービス プリンシパルのキー** をシークレットとしてキー コンテナーに追加します 
* SQL 認証: **パスワード** をシークレットとしてキー コンテナーに追加します
* アカウント キー: **アカウント キー** をシークレットとしてキー コンテナーに追加します

キー コンテナーにシークレットを追加する方法について詳しくは、こちらをご覧ください: (キー コンテナー関する記事を挿入する)

キー コンテナーにシークレットを格納した後、[資格情報] のコマンド バーで [+ 新規] を選択して新しい資格情報を作成します。 必要な情報を入力します (認証方法と、シークレットの選択元となる Key Vault インスタンスの選択を含む)。 すべての詳細の入力が完了したら、[作成] をクリックします。

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新しい資格情報":::

新しい資格情報が [資格情報] リスト ビューに表示され、使用する準備ができていることを確認します

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="資格情報の表示":::

## <a name="manage-your-key-vault-connections"></a>キー コンテナーの接続を管理する

1. Key Vault 接続を名前で検索します

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Key Vault の検索":::

1. 1 つ以上の Key Vault 接続を削除します
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="キー コンテナーを削除する":::

## <a name="manage-your-credentials"></a>資格情報を管理する

1. 資格情報を名前で検索します
  
2. 既存の資格情報を選択して更新します

3. 1 つ以上の資格情報を削除します
