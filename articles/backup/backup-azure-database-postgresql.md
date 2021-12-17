---
title: Azure Database for PostgreSQL のバックアップ
description: 長期保有を指定した Azure Database for PostgreSQL のバックアップ (プレビュー) について説明します。
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 4121326bf36c71c3f98894bd8413dc1ae5f57a06
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458927"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>長期保有を指定した Azure Database for PostgreSQL のバックアップ (プレビュー)

この記事では、Azure Database for PostgreSQL サーバーをバックアップする方法について説明します。

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL データベースでバックアップを構成する

複数の Azure PostgreSQL サーバーにある複数のデータベースのバックアップを構成できます。 Azure Backup を使用して Azure PostgreSQL データベースでバックアップを構成するには、次の手順を行います。

1. **[バックアップ コンテナー]**  ->  **[+ バックアップ]** に移動します。

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="バックアップを追加するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="バックアップ情報を追加するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   また、[バックアップ センター](./backup-center-overview.md)からこのページに移動することもできます。 

1. バックアップ スケジュールと保持期間を定義するバックアップ ポリシーを選択または[作成](#create-backup-policy)します。

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="バックアップ ポリシーを追加するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **バックアップする Azure Postgres データベースを選択する**: コンテナーと同じリージョンにある場合は、サブスクリプション全体で Azure PostgreSQL サーバーの 1 つを選択します。 矢印を展開して、サーバー内のデータベースの一覧を表示します。

   >[!Note]
   >データベース *azure_maintenance* と *azure_sys* はバックアップできず、またバックアップする必要もありません。 また、既にバックアップ コンテナーにバックアップされているデータベースをバックアップすることはできません。

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Azure PostgreSQL データベースを選択するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL サーバーを選択する方法が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. 選択したデータベースに接続するための資格情報を格納する **Azure キー コンテナーを割り当てます**。 個々の行レベルでキー コンテナーを割り当てるには、 **[Select a key vault and secret]\(キー コンテナーとシークレットを選択\)** をクリックします。 また、行を複数選択してキー コンテナーを割り当て、グリッドの上部メニューの [Assign key vault]\(キー コンテナーの割り当て\) をクリックすることもできます。 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Azure キー コンテナーの割り当て方法が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. シークレット情報を指定するには、次のいずれかのオプションを使用します。 

   1. **シークレット URI を入力する**: このオプションは、シークレット URI が共有されている、または既知の場合に使用します。 **シークレット URI をキー コンテナーから** コピー ->  **[シークレット] (シークレットを選択する)**  ->  **[シークレット識別子]** することができます。

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="シークレット URI の入力方法が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      ただし、このオプションを使用すると、参照しているキー コンテナーについて、Azure Backup に表示されません。 そのため、キー コンテナーに対するアクセス許可をインラインで付与することができません。 バックアップ管理者は、Postgres またはキー コンテナーの管理者、あるいはその両方と共に、バックアップ操作を成功させるために、バックアップの構成フローの外で、バックアップ コンテナーの[キー コンテナーに対するアクセス権が手動で付与されている](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server)ようにする必要があります。

   1. **キー コンテナーを選択する**: このオプションは、キー コンテナーとシークレット名がわかっている場合に使用します。 このオプションを使用すると、ユーザー (キー コンテナーに対する書き込みアクセス権を持つバックアップ管理者) は、キー コンテナーに対するアクセス許可をインラインで付与することができます。 キー コンテナーとシークレットは、事前に存在しているものを利用するか、途中で作成することもできます。 シークレットが、サーバーで "バックアップ" 権限が付与されているデータベース ユーザーの資格情報を使って更新される、ADO.net 形式の PG サーバー接続文字列であるようにします。 詳細については、[キー コンテナーにシークレット](#create-secrets-in-the-key-vault)を作成する方法に関する記事をご覧ください。

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="シークレット ストアを割り当てる方法が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Azure Key Vault のシークレットの選択が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. シークレット情報の更新が完了すると、キー コンテナー情報が更新された後で検証が開始されます。 ここで、バックアップ サービスによって、キー コンテナーからシークレットの詳細を読み取り、データベースに接続するために必要なすべての[アクセス許可](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model)() があるかどうかが検証されます。 1 つまたは複数のアクセス許可が見つからない場合は、"_ロールの割り当てが完了していません、または、ユーザーがロールを割り当てることができません_" というエラー メッセージのいずれかが表示されます。

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text=" シークレットの検証が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. **ユーザーはロールを割り当てることができません**: このメッセージは、 **[詳細の表示]** に一覧表示されているように、不足している権限を割り当てるため、PostgreSQL サーバーまたはキー コンテナー、あるいはその両方への書き込みアクセス権をユーザー (バックアップ管理者) が持っていない場合に表示されます。 アクション ボタンから割り当てテンプレートをダウンロードし、PostgreSQL またはキー コンテナー管理者、あるいはその両方が実行するようにします。これは、必要なリソースに対して必須のアクセス許可を割り当てるのに役立つ ARM テンプレートです。 テンプレートが正常に実行されたら、[バックアップの構成] ページで **[再検証]** をクリックします。

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="ロールの割り当てテンプレートをダウンロードするオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. **ロールの割り当てが行われていません**: このメッセージは、 **[詳細の表示]** に一覧表示されているように、不足している権限を割り当てるため、PostgreSQL サーバーまたはキー コンテナー、あるいはその両方への書き込みアクセス権をユーザー (バックアップ管理者) が持っている場合に表示されます。 上部のアクション メニューの **[Assign missing roles]\(不足しているロールの割り当て\)** アクション ボタンを使用して、PostgreSQL サーバーまたはキー コンテナー、あるいはその両方に対するアクセス許可をインラインで付与します。

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="ロールの割り当てが完了していないというエラー メッセージが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. 上部のメニューで **[Assign missing roles]\(不足しているロールの割り当て\)** を選択し、ロールを割り当てます。 プロセスが開始されると、KV または PG サーバー、あるいはその両方に対する[不足しているアクセス許可](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server)がバックアップ コンテナーに付与されます。 アクセス許可を付与するスコープを定義できます。 アクションが完了すると、再検証が開始されます。

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="不足しているロールを割り当てるオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="アクセス許可のスコープの定義が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - バックアップ コンテナーから、キー コンテナーのシークレットにアクセスし、データベースへのテスト接続を実行して、資格情報が正しく入力されているかどうかを検証します。 データベース ユーザーの権限は、[データベース ユーザーがデータベースに対するバックアップ関連の権限を持っているかどうか](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database)も確認されます。

   - PostgreSQL 管理者は、既定では、データベースに対するバックアップと復元のすべての権限を持ちます。 その結果、検証は成功します。
   - 権限の低いユーザーは、データベースに対するバックアップ/復元権限を持っていない可能性があります。 その結果、検証は失敗します。 PowerShell スクリプトが動的に生成されます (レコード、または選択されたデータベースごとに 1 つ)。 [PowerShell スクリプトを実行して、データベースのデータベース ユーザーにこれらの特権を付与します](#create-secrets-in-the-key-vault)。 または、PG 管理または PSQL ツールを使用してこれらの権限を割り当てることもできます。

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="バックアップ コンテナーからキー コンテナーのシークレットへのアクセスが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="テスト接続を開始するプロセスが表示されているスクリーンショット。":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="テストを実行するためのユーザー認証情報を提供する方法が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. バックアップの準備ができているレコードを成功として保持して、操作を送信する最後の手順に進みます。

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="バックアップの準備が正常に完了したことが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="バックアップの構成の確認ページが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. バックアップの構成操作を送信し、**バックアップ インスタンス** の進行状況を追跡します。

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="バックアップ構成の送信と進行状況の追跡が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーは、バックアップの構成フローの途中で作成できます。 または、 **[バックアップ センター]**  ->  **[バックアップ ポリシー]**  ->  **[追加]** に移動します。

1. 新しいポリシーの名前を入力します。

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="新しいポリシーの名前を入力するプロセスが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. バックアップ スケジュールを定義します。 現時点では、毎週のバックアップ オプションのみを使用できます。 ただし、バックアップを複数の曜日にスケジュールすることができます。

1. **保持** の設定を定義します。 保持規則を 1 つ以上追加できます。 各保持規則は、特定のバックアップの入力と、それらのバックアップのデータ ストアと保持期間を前提とします。

1. バックアップを 2 つのデータ ストア (または層) のいずれかに保存するには、**バックアップ データ ストア** (標準層) または **アーカイブ データ ストア** (プレビュー段階) を選択します。

1. **[On-expiry]\(期限切れ時\)** を選択して、バックアップ データ ストアで期限切れになったときにバックアップをアーカイブ データ ストアに移動します。

   他の保持規則が存在しない場合、**既定の保持規則** が適用されます。既定値は 3 か月です。

   - **バックアップ データ ストア** では、保持期間の範囲は 7 日から 10 年です。
   - **アーカイブ データ ストア** では、保持期間の範囲は 6 か月から 10 年です。

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="[On-expiry]\(期限切れ時\) を選択して、期限切れになったときにバックアップをアーカイブ データ ストアに移動することが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>保持規則は、事前に決められた優先順位で評価されます。 優先順位は、高い方から順に、年単位の規則、月単位の規則、週単位の規則となります。 他の規則が適用されない場合、既定の保持設定が適用されます。 たとえば、最初に成功した週単位のバックアップと、最初に成功した月単位のバックアップで、復旧ポイントが同じになる場合があります。 しかし、月単位の規則の方が週単位の規則よりも優先順位が高いため、毎月最初に成功したバックアップに対応する保持が適用されます。
## <a name="create-secrets-in-the-key-vault"></a>キー コンテナーにシークレットを作成する

シークレットは、サーバーで **バックアップ** 権限が付与されているデータベース ユーザーの資格情報を使って更新される _ADO.net_ 形式の PG サーバー接続文字列です。 PG サーバーから接続文字列をコピーし、テキスト エディターで編集して、"_ユーザー ID とパスワード_" を更新します。 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="PG サーバーの接続文字列がシークレットとして表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="シークレット PG サーバーの接続文字列を作成するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>PowerShell スクリプトを実行してデータベース ユーザーに権限を付与する

バックアップの構成中に PowerShell スクリプトが動的に生成され、データベース ユーザーが PG 管理者の資格情報と共に入力として受け入れられ、データベースでデータベース ユーザーにバックアップ関連の特権が付与されます。

[PSQL ツール](https://www.enterprisedb.com/download-postgresql-binaries)はコンピューター上に存在する必要があり、PATH 環境変数が PSQL ツールのパスに適切に設定されている必要があります。

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="環境設定アプリケーションを検索するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="[システムのプロパティ] で環境を設定するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="既定の環境変数が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="設定が必要な環境変数が表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

ネットワーク接続を許可するコンピューターの IP アドレスが、Azure PostgreSQL インスタンスの **接続セキュリティ設定** の許可リストに載っていることを保証します。

## <a name="generate-an-on-demand-backup"></a>オンデマンド バックアップの生成

ポリシーで指定されたスケジュールにないバックアップをトリガーするには、 **[バックアップ インスタンス]**  ->  **[今すぐバックアップ]** に移動します。
関連するバックアップ ポリシーで定義された保持規則の一覧から選択します。

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="関連するバックアップ ポリシーに定義された保持規則の一覧に移動するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="関連するバックアップ ポリシーに定義された保持規則を選択するオプションが表示されているスクリーンショット。" lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>次のステップ

[Azure Backup を使用した PostgreSQL データベースのバックアップのトラブルシューティング](backup-azure-database-postgresql-troubleshoot.md)