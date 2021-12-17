---
title: PgBouncer - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、組み込みの PgBouncer 拡張機能の概要を示します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d49deec6a782e0b36f110b7c2046b89e314e4011
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387337"
---
# <a name="pgbouncer-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL の PgBouncer - フレキシブル サーバー

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL – フレキシブル サーバーでは、組み込みの接続プール ソリューションとして [PgBouncer](https://github.com/pgbouncer/pgbouncer) が提供されます。 これは、データベース サーバーごとに有効にすることができるオプションのサービスであり、パブリックおよびプライベート アクセスの両方でサポートされています。 PgBouncer は、Postgres データベース サーバーと同じ仮想マシンで実行されます。 Postgres では接続にプロセスベースのモデルを使用するため、アイドル状態の接続の保持に負荷がかかります。 そのため、サーバーで数千を超える接続が実行されると、Postgres 自体はリソースの制約を受けます。 PgBouncer の主なベネフィットは、データベース サーバーでアイドル状態の接続と短期間の接続を向上させることです。

PgBouncer では、非同期 I/O を利用するより軽量なモデルを使用します。また、必要な場合 (つまり、開いているトランザクション内で、あるいはクエリがアクティブな場合) にのみ、実際の Postgres 接続を使用します。 このモデルでは、オーバーヘッドが低い数千の接続をより簡単にサポートでき、オーバーヘッドが低い最大 1 万の接続にスケーリングできます。

有効になっている場合、PgBouncer はデータベース サーバーのポート 6432 で実行されます。 同じホスト名を使用するようにアプリケーションのデータベース接続構成を変更できますが、ポートを 6432 に変更して PgBouncer の使用を開始し、アイドル状態の接続のスケーリングを向上させるというベネフィットを得ることができます。

> [!Note]
> PgBouncer は、パブリック アクセスとプライベート アクセスの両方のネットワークで、General Purpose とメモリ最適化のコンピューティング レベルでサポートされています。 

## <a name="enabling-and-configuring-pgbouncer"></a>PgBouncer の有効化と構成

PgBouncer を有効にするには、Azure portal の [サーバー パラメーター] ブレードに移動し、"PgBouncer" を検索して、PgBouncer が有効になるように pgbouncer.enabled 設定を "true" に変更します。 サーバーを再起動する必要はありません。 しかし、その他の PgBouncer パラメーターを設定する場合は、制限事項のセクションを参照してください。

これらのパラメーターを使用して、PgBouncer の設定を構成できます。

| パラメーター名             | 説明 | Default | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | このパラメーター値を、ユーザーとデータベースのペアごとの接続数に設定します      | 50       | 
| pgBouncer.max_client_conn | このパラメーター値を、サポートする必要がある、PgBouncer へのクライアント接続の最大数に設定します。     | 5000     | 
| pgBouncer.pool_mode | トランザクション プールについて、このパラメーター値を TRANSACTION に設定します (ほとんどのワークロードで推奨される設定です)。      | TRANSACTION     |
| pgBouncer.min_pool_size | この数を下回る場合は、プールするサーバー接続をさらに追加します。    |   0 (無効)   |
| pgbouncer.ignore_startup_parameters | PgBouncer が無視できるパラメーターのコンマ区切りのリスト。 たとえば、PgBouncer で `extra_float_digits` パラメーターを無視させることができます。|   |
| pgbouncer.query_wait_timeout | クエリの実行の待機に費やすことができる最大時間 (秒)。 その時間内にクエリがサーバーに割り当てられない場合、クライアントは切断されます。 | 120 秒 |
| pgBouncer.stats_users | 省略可能。 このパラメーター値を既存のユーザーの名前に設定し、特殊な PgBouncer 統計データベース ("PgBouncer" という名前) にログインできるようにします。    |      |

PgBouncer の構成の詳細については、[pgbouncer.ini](https://www.pgbouncer.org/config.html) を参照してください。

> [!Note] 
> PgBouncer のアップグレードは Azure によって管理されます。

## <a name="switching-your-application-to-use-pgbouncer"></a>PgBouncer を使用するようにアプリケーションを切り替える

PgBouncer の使用を開始するには、これらの手順に従います。
1. データベース サーバーに接続しますが、通常のポート 5432 ではなくポート **6432** を使用し、この接続が機能することを確認します
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. PgBouncer に対して QA 環境でアプリケーションをテストし、互換性の問題がないことを確認します。 PgBouncer プロジェクトでは互換性マトリックスが提供されます。ほとんどのユーザーに、**トランザクション プール** を使用することをお勧めしています (https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes を参照)。
3. 実稼働アプリケーションを変更して **5432** ではなくポート **6432** に接続し、互換性の問題を指している可能性のあるアプリケーション側のエラーを監視します。

> [!Note] 
> PgBouncer を有効にした場合でも、同じホスト名を使用して、ポート 5432 経由でデータベース サーバーに直接接続できます。

## <a name="pgbouncer-in-zone-redundant-high-availability"></a>ゾーン冗長による高可用性における PgBouncer

ゾーン冗長による高可用性構成サーバーでは、プライマリ サーバーによって PgBouncer が実行されます。 ポート 6432 経由でプライマリ サーバーの PgBouncer に接続できます。 フェールオーバー後、新しく昇格されたスタンバイ (新しいプライマリ サーバー) で PgBouncer が再起動されます。 そのため、アプリケーションの接続文字列は、フェールオーバー後も同じままです。 

## <a name="using-pgbouncer-with-other-connection-pools"></a>PgBouncer を他の接続プールと共に使用する

場合によっては、アプリケーション側の接続プールが既に存在するか、アプリケーション側 (AKS サイドカーなど) に PgBouncer が設定されていることがあります。 このような場合でも、アイドル状態の接続スケーリングのベネフィットが得られるため、組み込みの PgBouncer を利用すると便利です。

アプリケーション側のプールをデータベース サーバー上の PgBouncer と共に利用すると役立つ場合があります。 ここでは、アプリケーション側プールによって、初期接続の待機時間が短縮されるというベネフィットが得られます (接続を初期化するための初期ラウンドトリップが非常に速くなります)。また、データベース側の PgBouncer では、アイドル状態の接続がスケーリングされます。

## <a name="limitations"></a>制限事項
 
* PgBouncer は現在、バースト可能なサーバー コンピューティング レベルではサポートされていません。 
* コンピューティング レベルを General Purpose またはメモリ最適化からバースト可能なレベルに変更した場合、PgBouncer の機能は失われます。
* スケール操作中、HA フェールオーバー中、または再起動時にサーバーが再起動されるたびに、PgBouncer もサーバー仮想マシンと共に再起動されます。 そのため、既存の接続を再確立する必要があります。
* 既知の問題により、ポータルには PgBouncer パラメーターがすべて表示されません。 PgBouncer を有効にしてパラメーターを保存したら、パラメーター画面を終了して (たとえば、[概要] をクリックして) から、[パラメーター] ページに戻る必要があります。 
* [SCRAM 認証](how-to-connect-scram.md)は PgBouncer ではサポートされていません。
  
## <a name="next-steps"></a>次のステップ

- [ネットワークの概念](./concepts-networking.md)について学習する
- フレキシブル サーバーの[概要](./overview.md)
