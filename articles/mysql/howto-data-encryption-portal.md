---
title: ポータルを使用した Azure Database for MySQL のデータ暗号化
description: Azure portal を使用して Azure Database for MySQL のデータ暗号化を設定し、管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5bd100a5858660d80f3704844ed974f2bc9257b4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028945"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Azure portal を使用した Azure Database for MySQL サーバーのデータ暗号化

この記事では、Azure portal を使用して Azure Database for MySQL のデータ暗号化を設定するための設定および管理の方法について説明します。

## <a name="prerequisites-for-cli"></a>CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* カスタマー マネージド キーに使用する Azure Key Vault とキーを作成します。
* カスタマー マネージド キーとして使用するには、Key Vault に次のプロパティが必要です。
  * [論理的な削除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* カスタマー マネージド キーに使用するには、キーに次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * "_取得_"、"_キーのラップ_"、"_キーのラップ解除_" の各操作を実行できる

## <a name="setting-the-right-permissions-for-key-operations"></a>キー操作に対する適切なアクセス許可の設定

1. Azure Key Vault で、 **[アクセス ポリシー]** 、 **[アクセス ポリシーの追加]** の順に選択します。

   ![アクセス ポリシーの概要](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **[キーのアクセス許可]** を選択して、 **[取得]** 、 **[ラップ]** 、 **[ラップ解除]** 、および MySQL サーバーの名前である **[プリンシパル]** を選択します。 サーバー プリンシパルが既存のプリンシパルの一覧に見つからない場合は、初めてのデータ暗号化の設定を試みる (これは失敗します) ことによって登録する必要があります。

   ![アクセス ポリシーの概要](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 設定を**保存**します。

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ暗号化の設定

1. **[Azure Database for MySQL]** で、 **[データの暗号化]** を選択して、カスタマー マネージド キーのセットアップを設定します。

   ![データ暗号化の設定](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. **Key Vault** と**キー**の組を選択するか、**キー識別子**を渡すことができます。

   ![Key Vault の設定](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 設定を**保存**します。

4. すべてのファイル (**一時ファイル**を含む) が完全に暗号化されていることを確認するには、サーバーを**再起動**する**必要があります**。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>データ暗号化が有効になっているサーバーのレプリカの復元または作成

Azure Database for MySQL 単一サーバーが、キー コンテナーに格納されているカスタマー マネージド キーで暗号化されていると、そのサーバーの新しく作成されるすべてのコピーに対して、ローカル/geo リストア操作またはレプリカ (ローカル/リージョン間) 操作のどちらかが実行されます。 そのため、暗号化された MySQL サーバーの場合は、次の手順に従って、暗号化済みの復元されたサーバーを作成できます。

1. サーバーで、 **[概要]** を選択してから、 **[復元]** を選択します。

   ![開始-復元](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   レプリケーションが有効なサーバーでは、次に示すように、 **[設定]** 見出しの下にある **[レプリケーション]** を選択します。

   ![開始-レプリカ](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 復元操作が完了すると、作成された新しいサーバーは、プライマリ サーバーの暗号化に使用されたキーで暗号化されます。 ただし、サーバーの機能とオプションは無効になっており、サーバーは「**アクセス不可**」状態でマークされます。 この動作は、Key Vault にアクセスするためのアクセス許可がこの時点で新しいサーバーの ID に付与されていないため、あらゆるデータ操作を禁止するように設計されています。

   ![サーバーをアクセス不可としてマーク](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. アクセス不可状態を修正するには、復元されたサーバーでキーを再検証する必要があります。 **[データの暗号化]** ウィンドウを選択し、 **[Revalidate key]\(キーの再検証\)** ボタンを選択します。

   > [!NOTE]
   > 新しいサーバーのサービス プリンシパルに Key Vault へのアクセス権を付与する必要があるため、最初の再検証の試行は失敗します。 サービス プリンシパルを生成するには、 **[Revalidate key]\(キーの再検証\)** を選択します。これにより、エラーが発生しますが、サービス プリンシパルが生成されます。 それ以降については、上記の[セクション 2](#setting-the-right-permissions-for-key-operations) の手順を参照してください。

   ![サーバーの再検証](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   新しいサーバーへのアクセス権を Key Vault に付与する必要があります。

4. サービス プリンシパルを登録した後、キーを再検証する必要があります。これにより、サーバーは通常の機能を再開します。

   ![正常なサーバーの復元](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>次のステップ

 データの暗号化の詳細については、[Azure のデータ暗号化の概要](concepts-data-encryption-mysql.md)に関する記事を参照してください。
