---
title: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) クイック スタート
description: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) で分散型テーブルを作成し、クエリを実行するためのクイック スタート。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406443"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を作成する

Azure Database for PostgreSQL は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケールできるマネージド サービスです。 このクイック スタートでは、Azure portal を使用して Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) サーバー グループを作成する方法について説明します。 ノード間でテーブルをシャード化したり、サンプル データを取り込んだり、複数のノードでクエリを実行したり、分散データをいろいろ試してみます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-an-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の作成

Azure Database for PostgreSQL サーバーを作成するには、次の手順に従います。
1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[新規]** ページで **[データベース]** を選択し、**[データベース]** ページで **[Azure Database for PostgreSQL]** を選択します。
3. デプロイ オプションについては、**[Hyperscale (Citus) サーバー グループ - プレビュー]** の下にある **[作成]** ボタンをクリックします。
4. 新しいサーバーの詳細フォームには次の情報を入力してください。
   - リソース グループ: このフィールドのテキスト ボックスの下の **[新規作成]** リンクをクリックします。 **myresourcegroup** などの名前を入力します。
   - サーバー グループ名: 新しいサーバー グループの一意の名前を入力します。これはサーバー サブドメインにも使用されます。
   - 管理者ユーザー名: 一意のユーザー名を入力します。これは後でデータベースへの接続に使用されます。
   - パスワード: 少なくとも 8 文字で、次のカテゴリのうち 3 つのカテゴリの文字が含まれている必要があります。英字大文字、英字小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など)。
   - 場所: データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。

   > [!IMPORTANT]
   > ここで指定するサーバー管理者ログインとパスワードは、このクイック スタートの後の方でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

5. **[サーバー グループの構成]** をクリックします。 そのセクションの設定を変更しないで、**[保存]** をクリックします。
6. **[確認と作成]**、**[作成]** の順にクリックして、サーバーをプロビジョニングします。 プロビジョニングには数分かかります。
7. ページは、デプロイを監視するためにリダイレクトされます。 ライブ状態が **[デプロイが進行中です]** から **[デプロイが完了しました]** に変わったら、ページの左側にある **[出力]** メニュー項目をクリックします。
8. 出力ページには、コーディネーターのホスト名が含まれており、値をクリップボードにコピーするためのボタンが横に付いています。 後で使用するために、この情報を記録しておきます。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) サービスは、サーバーレベルでファイアウォールを使用します。 既定では、ファイアウォールは、すべての外部のアプリケーションとツールがコーディネーター ノードやデータベースに接続することを防止します。 特定の IP アドレス範囲のファイアウォールを開放する規則を追加する必要があります。

1. コーディネーター ノードのホスト名を以前にコピーした **[出力]** セクションから、[戻る] をクリックして **[概要]** メニュー項目に戻ります。

2. デプロイのスケーリング グループの名前には "sg-" という接頭辞が付きます。 リソースの一覧でそれを見つけ、クリックします。

3. 左側のメニューの **[セキュリティ]** の下にある **[ファイアウォール]** をクリックします。

4. **[+ 現在のクライアント IP アドレスに対するファイアウォール規則の追加]** リンクをクリックします。 最後に、**[保存]** ボタンをクリックします。

5. **[Save]** をクリックします。

   > [!NOTE]
   > Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell で psql を使用してデータベースに接続する

ここでは [psql](https://www.postgresql.org/docs/current/app-psql.html) コマンド ライン ユーティリティを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。
1. 上部のナビゲーション ウィンドウで、ターミナルのアイコンをクリックして Azure Cloud Shell を起動します。

   ![Azure Database for PostgreSQL - Azure Cloud Shell ターミナル アイコン](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Azure Cloud Shell がブラウザで開き、bash コマンドを入力できます。

   ![Azure Database for PostgreSQL - Azure Shell Bash プロンプト](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. Cloud Shell プロンプトで、psql コマンドを使用して Azure Database for PostgreSQL サーバーに接続します。 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティで Azure Database for PostgreSQL サーバーに接続するには、次の形式を使用します。
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **citus** に接続します。 サーバー管理者のパスワードを求められたら、入力します。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>テーブルを作成して配布する

psql を使用し、ハイパースケール コーディネーター ノードに接続したら、基本的なタスクの一部を完了できます。

ハイパースケール サーバー内には、3 種類のテーブルがあります。

- 分散またはシャード化されたテーブル (パフォーマンスと並列処理のための拡張を支援する目的で分散されます)
- 参照テーブル (複数のコピーを保持)
- ローカル テーブル (多くの場合、内部管理テーブルとして使用)

このクイック スタートでは、分散テーブルとこれをよく理解することに重点を置きます。

これから使用するデータ モデルはシンプルです。GitHub のユーザーとイベント データを使用します。 イベントには、フォーク作成や組織に関連する git コミットなどがあります。

psql 経由で接続できたら、テーブルを作成しましょう。 psql コンソールで次を実行します。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` の `payload` フィールドには JSONB データ型があります。 JSONB は Postgres におけるバイナリ形式の JSON データ型です。 これにより、より柔軟なスキーマを 1 つの列に簡単に格納できます。

Postgres では、その中にあるすべてのキーと値にインデックスを作成するこの型で `GIN` インデックスを作成できます。 インデックスがあると、さまざまな条件でペイロードに簡単にクエリを実行できます。 それでは、データを読み込む前にインデックスをいくつか作成してみましょう。 psql で:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

次に、コーディネーター ノードの Postgres テーブルを選択し、worker 間でシャード化するように Hyperscale に指示します。 その際、シャード化するためのキーを指定し、各テーブルにクエリを実行します。 現在の例では、`user_id` でイベントとユーザーのテーブルをシャード化します。

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

データを読み込む準備ができました。 [users.csv](https://examples.citusdata.com/users.csv) と [events.csv](https://examples.citusdata.com/events.csv) という 2 つのサンプル ファイルをダウンロードします。 ファイルをダウンロードしたら、psql を使用してデータベースに接続します。ダウンロードしたファイルが含まれるディレクトリから psql を慎重に実行します。 `\copy` コマンドでデータを読み込みます。

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>クエリを実行する

次は面白い部分です。クエリをいくつか実際に実行します。 単純な `count (*)` から初めて読み込んだデータの量を確認しましょう。

```sql
SELECT count(*) from github_events;
```

問題なく動作しました。 この種の集計にはすぐに戻りますが、今は他のクエリをいくつか見てみましょう。 JSONB `payload` 列内に、データが数多くありますが、これはイベントの種類によって異なります。 `PushEvent` イベントには、プッシュ用の個別コミットが多く含まれるサイズがあります。 これを使用し、時間あたりのコミット総数を見つけることができます。

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

これまでのところ、クエリには github\_events だけが関係していましたが、この情報を github\_users と組み合わせることができます。 ユーザーとイベントを両方、同じ ID (`user_id`) でシャード化したため、ユーザー ID が一致する両テーブルの行は同じデータベース ノードと[同じ場所に配置](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)され、簡単に結合できます。

`user_id` で結合した場合、Hyperscale は、worker ノードで並列実行するため、結合実行をシャードにプッシュできます。 たとえば、リポジトリを最も多く作成したユーザーを見つけましょう。

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの **[概要]** ページで、**[削除]** ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の **[削除]** ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Hyperscale (Citus) サーバー グループのプロビジョニング方法を学習しました。 そのサーバー グループに psql で接続し、スキーマを作成して、データを分散しました。

次に、チュートリアルに従い、拡張性の高いマルチテナント アプリケーションを構築します。
> [!div class="nextstepaction"]
> [マルチテナント データベースを設計する](https://aka.ms/hyperscale-tutorial-multi-tenant)
