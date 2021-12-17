---
title: Azure Database for PostgreSQL のバックアップについて
description: Azure Database for PostgreSQL バックアップ (プレビュー) の概要
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: b868af4c96691c9496a0c5382d9416e784d3eb8c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219396"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>Azure Database for PostgreSQL のバックアップについて (プレビュー)

Azure Backup と Azure Database Services を連携させることで、バックアップを最大 10 年間保持する、Azure Database for PostgreSQL サーバー向けのエンタープライズ クラスのバックアップ ソリューションが構築されます。 このソリューションには、長期的な保有期間以外に、次の機能が用意されています。

- お客様の管理による個々のデータベース レベルでのスケジュール バックアップとオンデマンド バックアップ。
- PostgreSQL サーバーへのデータベース レベルでの復元、または任意の BLOB ストレージへのデータベース レベルでの復元。
- すべての操作とジョブの一元的な監視。
- バックアップは、個別のセキュリティ ドメインと障害ドメインに保存されます。 何らかの状況で元のサーバーまたはサブスクリプションが侵害された場合は、[バックアップ コンテナー](./backup-vault-overview.md) (Azure Backup マネージド ストレージ アカウント) でバックアップが安全に保たれます。
- **pg_dump** を使用すると、復元の柔軟性が向上します。 これは、データベースのバージョン間での復元に役立ちます。 

このソリューションは個別に使用できます。また、データ保有期間が最大 35 日の [Azure PostgreSQL 提供のネイティブ バックアップ ソリューション](../postgresql/concepts-backup.md)に加えて使用することもできます。 ネイティブ ソリューションは、最新のバックアップから復旧する場合など、運用復旧に適しています。 Azure Backup ソリューションを使用すると、コンプライアンスのニーズを満たし、より細かく柔軟なバックアップと復元を行うことができます。

## <a name="support-matrix"></a>サポート マトリックス

|サポート  |詳細  |
|---------|---------|
|サポートされているデプロイ   |  [Azure Database for PostgreSQL - 単一サーバー](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|サポート対象の Azure リージョン |  米国東部、米国東部 2、米国中部、米国中南部、米国西部、米国西部 2、米国中西部、ブラジル南部、カナダ中部、北ヨーロッパ、西ヨーロッパ、英国南部、英国西部、ドイツ中西部、スイス北部、スイス西部、東アジア、東南アジア、東日本、西日本、韓国中部、韓国南部、インド中部、オーストラリア東部、オーストラリア中部、オーストラリア中部 2、アラブ首長国連邦北部  |
|サポートされる Azure PostgreSQL のバージョン    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>機能の考慮事項と制限事項

- すべての操作は、Azure portal からのみサポートされます。 
- データベースの最大サイズの上限として 400 GB が推奨されています。
- リージョン間のバックアップはサポートされていません。 したがって、Azure PostgreSQL サーバーは別のリージョンのコンテナーにバックアップできません。 同様に、バックアップは、コンテナーと同じリージョン内のサーバーにのみ復元できます。 ただし、サブスクリプション間のバックアップと復元はサポートされます。 
- 復元中に復旧されるのはデータのみです。"ロール" は復元されません。
- プレビューでは、テスト環境でのみソリューションを実行することをお勧めします。

## <a name="backup-process"></a>バックアップ プロセス

1. バックアップ管理者は、バックアップする Azure PostgreSQL データベースを指定できます。 また、指定されたデータベースに接続するために必要な資格情報を格納する Azure Key Vault の詳細を指定することもできます。 これらの資格情報は、Azure Key Vault のデータベース管理者によって安全にシード処理されます。
1. 次に、バックアップ サービスは、指定された PostgreSQL サーバーで[認証するための適切なアクセス許可](#azure-backup-authentication-with-the-postgresql-server)と、データベースをバックアップするためのアクセス許可があるかどうかを検証します。
1. Azure Backup は、保護された PostgreSQL サーバーと通信するために、バックアップ拡張機能がインストールされている worker ロール (VM) をスピン アップします。 この拡張機能は、コーディネーターと PostgreSQL プラグインで構成されます。 コーディネーターは、バックアップや復元などのさまざまな操作のためにワークフローをトリガーし、プラグインは実際のデータ フローを管理します。
1. スケジュールされた時刻になると、コーディネーターはプラグインと通信し、**pg_dump (カスタム)** を使用して、PostgreSQL サーバーからバックアップ データのストリーミングを開始します。
1. このプラグインによって、データが Azure Backup マネージド ストレージ アカウント (バックアップ コンテナーによってマスクされます) に直接送信されるため、ステージング場所は不要になります。 データは Microsoft マネージド キーを使用して暗号化され、Azure Backup サービスによってストレージ アカウントに保存されます。

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="バックアップ プロセスを示す図。":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>PostgreSQL サーバーでの Azure Backup 認証

Azure Backup は、Azure によって配置される厳密なセキュリティ ガイドラインに従います。ここではバックアップ対象のリソースに対する権限は想定されておらず、ユーザーが明示的に指定する必要があります。 

### <a name="key-vault-based-authentication-model"></a>キーコンテナー ベースの認証モデル

Azure Backup サービスは、各バックアップの実行中に Azure PostgreSQL に接続する必要があります。 この接続には、データベースに対応する ‘username + password’ (または接続文字列) が使用されますが、これらの資格情報は Azure Backup と一緒には格納されません。 代わりに、これらの資格情報は、[シークレットとして Azure Key Vault](../key-vault/secrets/about-secrets.md) 内にデータベース管理者によって安全にシード処理される必要があります。 ワークロード管理者は、資格情報の管理とローテーションを担当します。Azure Backup、バックアップを取得するために、キー コンテナーから最新のシークレットの詳細を呼び出します。
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="ワークロードまたはデータベース フローを示す図。":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Azure PostgreSQL データベースのバックアップに必要なアクセス許可のセット

1. バックアップ資格情報コンテナーの MSI に、次のアクセス許可を付与します。

   - Azure PostgreSQL サーバーの _閲覧者_ アクセス権。
   - Azure Key Vault の _Key Vault シークレット ユーザー_ (またはシークレットの get、list) アクセス権。

1. 次の場所に対するネットワーク通信経路アクセス権を設定します。

   - Azure PostgreSQL サーバー: **[Azure サービスへのアクセス許可]** フラグを **[はい]** に設定します。
   - キー コンテナー: **[信頼された Microsoft サービスを許可する]** フラグを **[はい]** に設定します。

1. データベースに対するデータベース ユーザーのバックアップ権限

>[!Note]
>目的のリソースに対する ‘write’ アクセス権がある場合は、[バックアップの構成](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)フローでこれらのアクセス許可を 1 回のクリックで付与できます。また、必要なアクセス許可がない場合 (複数のペルソナが関係する場合) は、ARM テンプレートを使用します。 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Azure PostgreSQL データベースの復元に必要なアクセス許可のセット

復元のアクセス許可は、バックアップに必要なアクセス許可と似ており、ターゲット PostgreSQL サーバーとそれに対応するキー コンテナーに対するアクセス許可を付与する必要があります。 バックアップの構成フローとは異なり、現在これらのアクセス許可をインラインで付与する操作は用意されていません。 そのため、[Postgres サーバーとそれに対応するキー コンテナーへのアクセス許可を手動で付与](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)する必要があります。

さらに、データベース ユーザーが、データベースに対し、キー コンテナーに格納されている資格情報に対応する次の復元権限を持っていることを確認します。

- ALTER USER username CREATEDB;
- _azure_pg_admin_ ロールをデータベース ユーザーに割り当てます。

### <a name="azure-active-directory-based-authentication-model"></a>Azure Active Directory ベースの認証モデル

Azure Active Directory (Azure AD) に全面的に基づく別の認証モデルを以前に発表しています。 しかし、新しいキー コンテナー ベースの認証モデル (前述のとおり) が、構成プロセスを容易にする代替オプションとして提供されるようになりました。 

この認証モデルを使用するための自動スクリプトと関連する指示を入手するには、[こちらのドキュメントをダウンロード](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)してください。 これにより、バックアップと復元のために、Azure PostgreSQL サーバーへの適切な一連のアクセス許可が付与されます。

>[!Note]
>新しいすべての構成の保護は、新しいキー コンテナー認証モデルでのみ実行されます。 ただし、Azure AD ベースの認証を使用して保護が構成されている既存のすべてのバックアップ インスタンスは、今後も存続され、定期的なバックアップが行われます。 これらのバックアップを復元するには、Azure AD ベースの認証に従う必要があります。

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>Azure PostgreSQL サーバーとキー コンテナーに対するアクセス許可を手動で付与する

Azure Backup に必要なすべてのアクセス許可を付与するには、次のセクションを参照してください。

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Azure PostgreSQL サーバーのアクセス許可

1. Azure PostgreSQL サーバーで、Azure Backup コンテナーの MSI **閲覧者** アクセス権を設定します。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Azure Backup コンテナーの MSI 閲覧者アクセスを Azure PostgreSQL サーバーに設定するためのオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Azure PostgreSQL サーバーでのネットワーク通信経路アクセス権: [Azure サービスへのアクセス許可] フラグを [はい] に設定します。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL サーバーでネットワーク通信経路へのアクセス権を設定するためのオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>Azure Key Vault に対するアクセス許可 (PostgreSQL サーバーに関連付けられている)

1. バックアップ コンテナーの MSI **Key Vault シークレット ユーザー** (またはシークレットの **get**、**list**) アクセス権を設定します。 アクセス許可を割り当てるには、ロールの割り当てまたはアクセス ポリシーを使用します。 両方のオプションを使用してアクセス許可を追加するのは役に立たないため、その必要はありません。

   - Azure のロールベースのアクセス制御 (Azure RBAC) 認可を使用する方法は次のとおりです。つまり、アクセス許可モデルは Azure ロールベースのアクセス制御に設定されます。

     - [アクセス制御] で、バックアップ コンテナーの MSI _Key Vault シークレット ユーザー_ に、キー コンテナーへのアクセス権を付与します。 そのロールのベアラーは、シークレットを読み取ることができます。
     - [Azure RBAC を使用して Azure キー コンテナーへのアクセス許可をアプリケーションに付与します](../key-vault/general/rbac-guide.md?tabs=azure-cli)。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="シークレットのユーザー アクセス許可を提供するオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="バックアップ コンテナーの MSI Key Vault シークレット ユーザーのアクセス権をキー コンテナーに付与するためのオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - アクセス ポリシーを使用する方法は次のとおりです。つまり、アクセス許可モデルはコンテナー アクセス ポリシーに設定されます。

     - シークレットに対する Get 権限と List 権限を設定します。
     - 「[Azure Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy.md?tabs=azure-portal)」を参照してください。

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="コンテナー アクセス ポリシー モデルに設定されるアクセス許可モデルを使用してアクセス許可を付与するオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. キー コンテナーでのネットワーク通信経路アクセス権: **[信頼された Microsoft サービスを許可する]** フラグを **[はい]** に設定します。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="キー コンテナーでのネットワーク通信経路アクセス権のために [信頼された Microsoft サービスを許可する] フラグを [はい] に設定することを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>データベースに対するデータベース ユーザーのバックアップ権限

[PG 管理](#using-the-pg-admin-tool)ツールで次のクエリを実行します (_username_ をデータベース ユーザー ID に置き換えてください)。

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
```

## <a name="using-the-pg-admin-tool"></a>PG 管理ツールを使用する

まだ入手していない場合は、[PG 管理ツールをダウンロード](https://www.pgadmin.org/download/)します。 このツールを使用して Azure PostgreSQL サーバーに接続できます。 また、データベースと新しいユーザーをこのサーバーに追加することもできます。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="PG 管理ツールを使用して Azure PostgreSQL サーバーに接続するプロセスを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

任意の名前で新しいサーバーを作成します。 Azure portal の Azure PostgreSQL リソース ビューに表示される **サーバー名** と同じホスト名またはアドレス名を入力します。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="PG 管理ツールを使用して新しいサーバーを作成するオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="[サーバー名] と同じホスト名またはアドレス名を入力するオプションを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

接続を通過するためのファイアウォール規則に、_現在のクライアント ID アドレス_ を追加してください。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="現在のクライアント ID アドレスをファイアウォール規則に追加するプロセスを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

新しいデータベースとデータベース ユーザーをサーバーに追加できます。 データベース ユーザーの場合は、新しい **ログイン/グループ ロール** を追加します。 **[ログインできますか?]** が **[はい]** に設定されていることを確認します。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="新しいデータベースとデータベース ユーザーをサーバーに追加するプロセスを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="データベース ユーザーの新しいログイン ロールまたはグループ ロールを追加するプロセスを示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="ログイン可能オプションが [はい] に設定されていることの確認を示すスクリーンショット。" lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL のバックアップ](backup-azure-database-postgresql.md)