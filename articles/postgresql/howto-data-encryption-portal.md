---
title: ポータルを使用した Azure Database for PostgreSQL 単一サーバーのデータ暗号化
description: Azure portal を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9c935ad8f77e2f8a6198a8ac095e0cc60c025a72
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028640"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>ポータルを使用した Azure Database for PostgreSQL 単一サーバーのデータ暗号化

この記事では、Azure portal を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。

## <a name="prerequisites-for-cli"></a>CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* カスタマー マネージド キーに使用する Azure Key Vault とキーを作成します。
* キー コンテナーには、カスタマー マネージド キーとして使用するための次のプロパティが必要です。
  * [論理的な削除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* キーには、カスタマー マネージド キーに使用するための次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * _[取得]_ 、 _[キーを折り返す]_ 、 _[キーの折り返しを解除]_ の各操作を実行できる

## <a name="setting-the-right-permissions-for-key-operations"></a>キー操作のための適切なアクセス許可の設定

1. Azure Key Vault で、 **[アクセス ポリシー]** 、 **[アクセス ポリシーの追加]** の順に選択します。

   ![アクセス ポリシーの概要](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **[キーのアクセス許可]** で、 **[取得]** 、 **[ラップ]** 、 **[ラップ解除]** 、および PostgreSQL サーバーの名前である **[プリンシパル]** を選択します。 サーバー プリンシパルが既存のプリンシパルの一覧に見つからない場合は、初めてのデータ暗号化の設定を試みる (これは失敗します) ことによって登録する必要があります。  

   ![アクセス ポリシーの概要](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 設定を**保存**します。

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーのデータ暗号化の設定

1. **[Azure Database for PostgreSQL]** で、 **[データの暗号化]** を選択して、カスタマー マネージド キーのセットアップを設定します。

   ![データ暗号化の設定](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. **[キー コンテナー]** と **[キー]** のペアを選択するか、または **[キー識別子]** を渡すことができます。

   ![キー コンテナーの設定](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 設定を**保存**します。

4. すべてのファイル (**一時ファイル**を含む) が完全に暗号化されようにするには、サーバーの**再起動**が**必須**です。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>データ暗号化が有効になっているサーバーのレプリカの復元または作成

Azure Database for PostgreSQL 単一サーバーが、キー コンテナーに格納されているカスタマー マネージド キーで暗号化されていると、そのサーバーの新しく作成されるすべてのコピーに対して、ローカル/geo リストア操作またはレプリカ (ローカル/リージョン間) 操作のどちらかが実行されます。 そのため、暗号化された PostgreSQL サーバーの場合は、次の手順に従って、暗号化済みの復元されたサーバーを作成できます。

1. サーバーで、 **[概要]** を選択してから、 **[復元]** を選択します。

   ![復元を開始する](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   または、レプリケーションが有効になっているサーバーの場合は、次に示すように、 **[設定]** 見出しで **[レプリケーション]** を選択します。

   ![レプリケーションを開始する](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 復元操作が完了すると、作成された新しいサーバーは、プライマリ サーバーのキーでデータが暗号化されます。 ただし、サーバー上の機能やオプションは無効になっており、サーバーは **[アクセス不可]** 状態でマークされます。 新しいサーバーの ID にはキー コンテナーにアクセスするためのアクセス許可がまだ与えられていないため、この動作はすべてのデータ操作を防止するために設計されています。

   ![サーバーを [アクセス不可] としてマークする](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. [アクセス不可] 状態を解決するには、復元されたサーバーでキーを再検証する必要があります。 **[データの暗号化]** ウィンドウを選択し、 **[キーの再検証]** ボタンを選択します。

   > [!NOTE]
   > 新しいサーバーのサービス プリンシパルにキー コンテナーへのアクセス権を付与する必要があるため、最初の再検証の試行は失敗します。 サービス プリンシパルを生成するには、 **[キーの再検証]** を選択します。これにより、エラーが発生しますが、サービス プリンシパルが生成されます。 それ以降の操作については、上の[セクション 2](#setting-the-right-permissions-for-key-operations) の手順を参照してください。

   ![サーバーを再検証する](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   新しいサーバーにキー コンテナーへのアクセス権を付与する必要があります。

4. サービス プリンシパルを登録した後、再びキーを再検証する必要があります。それにより、サーバーがその通常の機能を再開します。

   ![通常のサーバーの復元](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、[Azure のデータ暗号化の概要](concepts-data-encryption-postgresql.md)に関するページを参照してください。
