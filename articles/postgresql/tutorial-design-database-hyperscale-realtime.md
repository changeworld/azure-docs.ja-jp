---
title: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用したリアルタイム ダッシュボードの設計のチュートリアル
description: このチュートリアルでは、Azure Database for PostgreSQL Hyperscale (Citus) (プレビュー) で分散テーブルの作成、設定、クエリを行う方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 9f3473d83678ffea888dad736a9620006b2961f7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406392"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>チュートリアル:Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用したリアルタイム分析ダッシュボードの設計

このチュートリアルでは、Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) を使用して、次の作業を実行する方法を説明します。

> [!div class="checklist"]
> * Hyperscale (Citus) サーバー グループを作成する
> * psql ユーティリティを使用してスキーマを作成する
> * ノード全体でテーブルをシャード化する
> * サンプル データを作成する
> * ロールアップを実行する
> * 生データと集計データのクエリを実行する
> * データを期限切れにする

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
   - サーバー グループ名: **mydemoserver** (サーバーの名前は DNS 名にマップするため、グローバルに一意である必要があります)。
   - 管理ユーザー名: **myadmin** (これは後でデータベースへの接続に使用されます)。
   - パスワード: 少なくとも 8 文字で、次のカテゴリのうち 3 つのカテゴリの文字が含まれている必要があります。英字大文字、英字小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など)。
   - 場所: データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。

   > [!IMPORTANT]
   > ここで指定するサーバー管理者のログイン名とパスワードは、このチュートリアルの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

5. **[サーバー グループの構成]** をクリックします。 そのセクションの設定を変更しないで、**[保存]** をクリックします。
6. **[確認と作成]**、**[作成]** の順にクリックして、サーバーをプロビジョニングします。 プロビジョニングには数分かかります。
7. ページは、デプロイを監視するためにリダイレクトされます。 ライブ状態が **[デプロイが進行中です]** から **[デプロイが完了しました]** に変わったら、ページの左側にある **[出力]** メニュー項目をクリックします。
8. 出力ページには、コーディネーターのホスト名が含まれており、値をクリップボードにコピーするためのボタンが横に付いています。 後で使用するために、この情報を記録しておきます。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL サービスは、サーバーレベルでファイアウォールを使用します。 既定では、ファイアウォールにより、すべての外部のアプリケーションとツールが、サーバーやサーバー上のデータベースに接続することが防止されます。 特定の IP アドレス範囲のファイアウォールを開放する規則を追加する必要があります。

1. コーディネーター ノードのホスト名を以前にコピーした **[出力]** セクションから、[戻る] をクリックして **[概要]** メニュー項目に戻ります。

2. リソースの一覧でデプロイのスケーリング グループを見つけて、クリックします (グループ名の先頭には "sg-" が付きます)。

3. 左側のメニューの **[セキュリティ]** の下にある **[ファイアウォール]** をクリックします。

4. **[+ 現在のクライアント IP アドレスに対するファイアウォール規則の追加]** リンクをクリックします。 最後に、**[保存]** ボタンをクリックします。

5. **[Save]** をクリックします。

   > [!NOTE]
   > Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell で psql を使用してデータベースに接続する

ここでは [psql](https://www.postgresql.org/docs/current/app-psql.html) コマンド ライン ユーティリティを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。
1. 上部のナビゲーション ウィンドウで、ターミナルのアイコンをクリックして Azure Cloud Shell を起動します。

   ![Azure Database for PostgreSQL - Azure Cloud Shell ターミナル アイコン](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Azure Cloud Shell がブラウザで開き、bash コマンドを入力できます。

   ![Azure Database for PostgreSQL - Azure Shell Bash プロンプト](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. Cloud Shell プロンプトで、psql コマンドを使用して Azure Database for PostgreSQL サーバーに接続します。 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティで Azure Database for PostgreSQL サーバーに接続するには、次の形式を使用します。
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **citus** に接続します。 サーバー管理者のパスワードを求められたら、入力します。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>psql ユーティリティを使用してスキーマを作成する

psql を使用して Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) に接続すると、いくつかの基本的なタスクを完了することができます。 このチュートリアルでは、Web 分析からトラフィック データを取り込んでから、データをロールアップし、そのデータに基づいてリアルタイムのダッシュボードを提供する方法について説明します。

生の Web トラフィック データをすべて使用するテーブルを作成しましょう。 psql ターミナルで次のコマンドを実行します。

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

また、1 分あたりの集計を保持するテーブルと、前回のロールアップの位置を維持するテーブルを作成します。 psql で以下も実行します。

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

この psql コマンドを使用すると、テーブルの一覧に新しく作成されたテーブルが表示されます。

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>ノード全体でテーブルをシャード化する

ハイパースケールのデプロイでは、テーブルの行はユーザー指定の列の値に基づいて異なるノードに保存されます。 この "ディストリビューション列" には、ノード全体でデータがどのようにシャード化されているかが示されます。

ディストリビューション列がシャード キーである site\_id になるように設定しましょう。 psql で、次の関数を実行します。

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>サンプル データを作成する

これで、サーバー グループがデータを取り込む準備が整いました。 `psql` 接続からローカルで以下を実行して、継続的にデータを挿入することができます。

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('https://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

このクエリによって、約 4 分の 1 秒ごとに行が追加されます。 行は、ディストリビューション列 `site_id` の指示に従って、さまざまなワーカー ノードに格納されます。

   > [!NOTE]
   > データ生成クエリを実行したままにして、このチュートリアルのその他のコマンド用に 2 つ目の psql 接続を開きます。
   >

## <a name="query"></a>Query

ハイパースケール ホスティング オプションを使用すると、複数のノードでクエリを並行処理して速度を上げることができます。 たとえば、データベースではワーカー ノード上で SUM や COUNT などの集計が計算され、その結果が最終的な回答にまとめられます。

1 分あたりの Web 要求といくつかの統計情報をカウントするクエリを次に示します。
psql で実行して結果を確認してみてください。

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>ロールアップを実行する

上記のクエリは早い段階では問題なく動作しますが、データの規模が大きくなるにつれてパフォーマンスは低下します。 分散処理でも、繰り返し再計算するよりも、このデータを事前に計算する方が高速です。

生データを集計テーブルに定期的にロールアップすることで、ダッシュボードを確実に高速に維持させることができます。 このケースでは、1 分の集計テーブルにロールアップしますが、5 分、15 分、1 時間などの集計も可能です。

このロールアップを継続的に実行するので、それを実行する関数を作成します。 psql でこれらのコマンドを実行して `rollup_http_request` 関数を作成します。

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

関数を用意できたら、それを実行してデータをロールアップします。

```sql
SELECT rollup_http_request();
```

また、事前に集計されたフォームのデータを使用して、ロールアップ テーブルのクエリを実行し、前と同じレポートを取得することもできます。 次のコマンドレットを実行します。

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>古いデータを期限切れにする

ロールアップによってクエリは高速になりますが、無制限のストレージ コストを回避するために古いデータを期限切れにする必要があります。 細分性ごとにデータを保持する期間を決定し、標準のクエリを使用して期限切れのデータを削除するだけです。 次の例では、生データは 1 日、分ごとの集計は 1 か月間保持することにしました。

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

運用環境では、これらのクエリを関数にラップして cron ジョブで毎分呼び出すことができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの *[概要]* ページで、*[削除]* ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の *[削除]* ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Hyperscale (Citus) サーバー グループのプロビジョニング方法を学習しました。 そのサーバー グループに psql で接続し、スキーマを作成して、データを分散しました。 生のフォームで両方のデータのクエリを実行すること、そのデータを定期的に集計すること、集計されたテーブルのクエリを実行すること、古いデータを期限切れにすることを学習しました。

次は、ハイパースケールの概念について学習します。
> [!div class="nextstepaction"]
> [ハイパースケールのノードの種類](https://aka.ms/hyperscale-concepts)