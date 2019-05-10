---
title: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用したマルチテナント データベースの設計に関するチュートリアル
description: このチュートリアルでは、Azure Database for PostgreSQL Hyperscale (Citus) (プレビュー) で分散テーブルを作成、設定、クエリする方法を説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079547"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>チュートリアル: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用して、マルチテナント データベースを設計する

このチュートリアルでは、Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用して、次の作業を実行する方法を説明します。

> [!div class="checklist"]
> * Hyperscale (Citus) サーバー グループを作成する
> * psql ユーティリティを使用してスキーマを作成する
> * ノード全体でテーブルをシャード化する
> * サンプル データを取り込む
> * テナント データをクエリする
> * テナント間でデータを共有する
> * テナントごとにスキーマをカスタマイズする

## <a name="prerequisites"></a>前提条件

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
   - パスワード: 少なくとも 8 文字で、次のカテゴリのうち 3 つのカテゴリの文字が含まれている必要があります。英字大文字、英字小文字、数字 (0 - 9)、英数字以外の文字 (!、$、#、% など)。
   - 場所: データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。

   > [!IMPORTANT]
   > ここで指定するサーバー管理者のログイン名とパスワードは、このチュートリアルの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

5. **[サーバー グループの構成]** をクリックします。 そのセクションの設定を変更しないで、**[保存]** をクリックします。
6. **[確認と作成]**、**[作成]** の順にクリックして、サーバーをプロビジョニングします。 プロビジョニングには数分かかります。
7. ページは、デプロイを監視するためにリダイレクトされます。 ライブ状態が **[デプロイが進行中です]** から **[デプロイが完了しました]** に変わったら、ページの左側にある **[出力]** メニュー項目をクリックします。
8. 出力ページには、コーディネーターのホスト名が含まれており、値をクリップボードにコピーするためのボタンが横に付いています。 後で使用するために、この情報を記録しておきます。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL サービスは、サーバーレベルでファイアウォールを使用します。 既定では、ファイアウォールは、すべての外部のアプリケーションとツールがサーバーやサーバー上のデータベースに接続することを防止します。 特定の IP アドレス範囲のファイアウォールを開放する規則を追加する必要があります。

1. コーディネーター ノードのホスト名を以前にコピーした **[出力]** セクションから、[戻る] をクリックして **[概要]** メニュー項目に戻ります。

2. リソースの一覧でデプロイのスケーリング グループを見つけて、クリックします。 (グループ名の先頭には "sg-" が付きます。)

3. 左側のメニューの **[セキュリティ]** の下にある **[ファイアウォール]** をクリックします。

4. **[+ 現在のクライアント IP アドレスに対するファイアウォール規則の追加]** リンクをクリックします。 最後に、**[保存]** ボタンをクリックします。

5. **[Save]** をクリックします。

   > [!NOTE]
   > Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell で psql を使用してデータベースに接続する

ここでは [psql](https://www.postgresql.org/docs/current/app-psql.html) コマンド ライン ユーティリティを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。
1. 上部のナビゲーション ウィンドウで、ターミナルのアイコンをクリックして Azure Cloud Shell を起動します。

   ![Azure Database for PostgreSQL - Azure Cloud Shell ターミナル アイコン](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. Azure Cloud Shell がブラウザで開き、bash コマンドを入力できます。

   ![Azure Database for PostgreSQL - Azure Shell Bash プロンプト](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. Cloud Shell プロンプトで、psql コマンドを使用して Azure Database for PostgreSQL サーバーに接続します。 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティで Azure Database for PostgreSQL サーバーに接続するには、次の形式を使用します。
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **citus** に接続します。 サーバー管理者のパスワードを求められたら、入力します。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>psql ユーティリティを使用してスキーマを作成する

psql を使用して Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) に接続すると、いくつかの基本的なタスクを完了することができます。 このチュートリアルでは、広告主が自分のキャンペーンを追跡できるようにする Web アプリを作成する方法について説明します。

複数の会社でこのアプリを使用できます。それでは、会社を保持するテーブルと会社のキャンペーンを保持する別のテーブルを作成しましょう。 psql コンソールで、次のコマンドを実行します。

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

各キャンペーンでは、広告の掲載は有料になります。 上記のコードの後に psql で次のコードを実行して、広告用のテーブルも追加します。

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

最後に、各広告のクリック数とインプレッション数に関する統計情報を追跡します。

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

次を実行すると、psql のテーブルの一覧に新しく作成されたテーブルが表示されます。

```postgres
\dt
```

マルチ テナント アプリケーションではテナントごとにのみ一意性を適用することができます。そのため、すべての主キーと外部キーには会社 ID が含まれています。

## <a name="shard-tables-across-nodes"></a>ノード全体でテーブルをシャード化する

ハイパースケールのデプロイでは、テーブルの行はユーザー指定の列の値に基づいて異なるノードに保存されます。 この "ディストリビューション列" は、どのテナントがどの行を所有しているかを示します。

ディストリビューション列がテナント識別子である company\_id になるように設定しましょう。 psql で、次の関数を実行します。

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>サンプル データを取り込む

ここで、psql 外にある通常のコマンド ラインで、サンプル データ セットをダウンロードします。

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

psql 内に戻って、データを一括読み込みします。 psql は必ず、データ ファイルをダウンロードしたディレクトリと同じディレクトリで実行してください。

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

これで、このデータは、ワーカー ノードにまたがって分散するようになります。

## <a name="query-tenant-data"></a>テナント データをクエリする

アプリケーションでシングル テナントのデータが要求されると、データベースでは単一のワーカー ノードでクエリを実行できます。 シングルテナント クエリでは、単一のテナント ID でフィルター処理が行われます。 たとえば、次のクエリでは広告とインプレッションに対して `company_id = 5` がフィルターされます。 結果を確認するには、psql で実行してみてください。

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>テナント間でデータを共有する

これまで、すべてのテーブルは `company_id` によって分散されていましたが、一部のデータは特定のテナントに自然に "属している" のではなく、共有することができます。 たとえば、サンプルの広告プラットフォームのすべての会社は、IP アドレスに基づいて対象ユーザーの地理情報を取得する必要があるかもしれません。

共有されている地理情報を保持するためのテーブルを作成します。 psql で、以下を実行します。

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

次に、`geo_ips` を "参照テーブル" にして、すべてのワーカー ノードにテーブルのコピーを保存します。

```sql
SELECT create_reference_table('geo_ips');
```

サンプル データで読み込みます。 これは必ず、データセットをダウンロードしたディレクトリ内の psql で実行してください。

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

クリック数テーブルを geo\_ips で結合すると、すべてのノードで効率的に作業できます。
広告をクリックしたすべてのユーザーの場所を検索するための結合を、以下に示します。
290. psql でクエリを実行してみてください。

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>テナントごとにスキーマをカスタマイズする

場合によっては、各テナントに他のテナントが必要としない特別な情報を保存する必要があります。 ただし、すべてのテナントは同一のデータベース スキーマを持つ共通インフラストラクチャを共有しています。 追加データはどこに移動できますか?

コツは、PostgreSQL の JSONB のような制約のない列タイプを使用することです。  スキーマには、`clicks` に `user_data` という JSONB フィールドがあります。
会社 (例: 会社 5) は、この列を使用して、ユーザーがモバイル デバイス上にいるかどうかを追跡できます。

クリックを多く行った実行元が、デバイスであるか従来の訪問者であるかを検索するためのクエリを、以下に示します。

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

[部分インデックス](https://www.postgresql.org/docs/current/static/indexes-partial.html)を作成することで、単一の会社に対してこのクエリを最適化することができます。

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

一般的には、列内のすべてのキーと値に [GIN インデックス](https://www.postgresql.org/docs/current/static/gin-intro.html)を作成できます。

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの *[概要]* ページで、*[削除]* ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の *[削除]* ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Hyperscale (Citus) サーバー グループのプロビジョニング方法を学習しました。 psql でそのサーバー グループに接続し、スキーマを作成して、データを分散しました。 テナント内とテナント間の両方でデータをクエリしたり、テナントごとにスキーマをカスタマイズしたりすることを学習しました。

次に、ハイパースケールの概念について説明します。
> [!div class="nextstepaction"]
> [ハイパースケールのノード タイプ](https://aka.ms/hyperscale-concepts)
