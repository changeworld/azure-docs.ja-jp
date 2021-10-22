---
title: サーバー パラメーター - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーのサーバー パラメーターについて説明する
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711905"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーのサーバー パラメーター

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL には、各サーバーの構成可能なパラメーターのサブセットが用意されています。 Postgres パラメーターの詳細については、 [PostgreSQL のドキュメント](https://www.postgresql.org/docs/13/config-setting.html)を参照してください。

## <a name="an-overview-of-postgresql-parameters"></a>PostgreSQL パラメーターの概要 

Azure Database for PostgreSQL サーバーは、作成時に各パラメーターに対して最適な既定値で事前構成されています。 静的パラメーターはサーバーを再起動する必要があり、スーパーユーザーのアクセスを必要とするパラメーターをユーザーが構成することはできません。 

表示または変更に使用できるパラメーターを確認するには、Azure portal、およびサーバー パラメーターのページに進むことをお勧めします。 `ALTER DATABASE` コマンドまたは `ALTER ROLE` コマンドを使用して、ユーザー単位またはデータベース単位でパラメーターを構成することもできます。

>[!NOTE]
> Azure Database for PostgreSQL はマネージド データベース サービスであるため、`postgresql.conf` などの構成ファイルを表示または変更するホストまたは OS アクセス権はユーザーに提供されません。 ファイルの内容は、[サーバーパラメーター] ページのパラメーターの変更に基づいて自動的に更新されます。

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="サーバー パラメーター - portal":::

いくつかのパラメーターの一覧を次に示します。


   | パラメーター名             | 説明 |
|----------------------|--------|
| **max_connections** | Postgres フレキシブル サーバーで max_connections を調整して、5000 接続に設定できます。 詳細については、[制限ドキュメント](concepts-limits.md)を参照してください。 | 
| **shared_buffers**    | 'shared_buffers' 設定は、選択した SKU に応じて変わります (SKU によって、使用可能なメモリが決まります)。 汎用サーバーには 2 つの仮想コア用に 2 GB shared_buffers があり、メモリ最適化サーバーには 2 つの仮想コア用に 4 GB の shared_buffers があります。 shared_buffers 設定は、階層で仮想コアが増えると、線形的 (大まか) にスケーリングされます。 | 
| **shared_preload_libraries** | このパラメーターは、サポートされている拡張機能の定義済みセットを使用して構成できます。 `pg_stat_statements` 拡張機能に加えて (メンテナンスタスクに使用される) 、 `azure` 拡張機能が常に読み込まれていることに注意してください (pg_stat_statements パラメーターは、拡張機能がアクティブかどうかを制御するために使用できます)。 |
| **connection_throttling** | 無効なパスワード ログイン エラーが多すぎる場合に、IP ごとに一時的な接続のスロットリング有効化または無効化できます。 |
 | **work_mem** | このパラメーターには、それを超えると一時ディスク ファイルへの書き込みが行われる、内部並べ替え操作とハッシュ テーブルによって使用されたメモリの量を指定します ワークロードに複雑な並べ替えを行うクエリがほとんど含まれておらず、使用可能なメモリが多い場合、このパラメーターを増やすと、ディスクへの書き出しと比べて、Postgres によるメモリ内のスキャン量が多くなり、高速になります。  ただし、1 つの複雑なクエリに含まれる多数の並べ替えやハッシュ操作が同時に実行される可能性があるため、注意が必要です。 ディスク ベースの一時ファイルへの書き込みが開始される前に、これらの個々の操作に、値で許容される限りのメモリが使用されます。 そのため、比較的ビジーなシステムの全体のメモリ使用量は、個々の work_mem パラメーターの何倍にもなります。 この値をグローバルに調整する場合は、初期値として合計 RAM * 0.25/max_connections という式を使用できます。 Azure Database for PostgreSQL - フレキシブル サーバーは、このパラメーターについて 4096 から 2097151 バイトの範囲をサポートしています。|
| **effective_cache_size** |effective_cache_size パラメーターを使用すると、オペレーティング システムとデータベース自体のディスク キャッシュに使用できるメモリの量を推定できます。 PostgreSQL のクエリ プラン作成者は、RAM に固定されるかどうかを判断します。 値が大きい場合はインデックス スキャンが使用される可能性が高く、値が小さい場合はシーケンシャル スキャンが使用されます。 推奨されるのは、Effective_cache_size をマシンの合計 RAM の 50% に設定することです。 |
| **maintenance_work_mem** | maintenance_work_mem パラメーターには、基本的に、バキューム、インデックス作成、テーブルの変更、外部キーの追加という操作に使用するメモリの最大量を指定します。  そのパラメーターの既定値は 64 KB です。 この値は work_mem よりも高く設定することをお勧めします。これにより、バキュームのパフォーマンスを向上させることができます。 |
| **effective_io_concurrency** | PostgreSQL で想定される同時実行可能なディスク I/O 操作数を設定します。 この値を大きくすると、個々の PostgreSQL セッションによって並行して開始が試行される I/O 操作数が増えます。 指定できる範囲は 1 から 1000 であり、非同期 I/O 要求の発行を無効にする場合は 0 です。 現在、この設定はビットマップ ヒープ スキャンにのみ影響します。 |
 |**require_secure_transport** | アプリケーションがサーバーへの SSL 接続をサポートしていない場合は、必要に応じて、このパラメーター値を `OFF` にすることで、クライアントからのセキュリティで保護されたトランスポートを無効にできます。 |

>[!NOTE]
> Azure Database for PostgreSQL - フレキシブル サーバー SKU をスケールアップまたはスケールダウンすると、サーバーに使用できるメモリに影響するため、前述の情報に基づいて、work_mem や effective_cache_size などのメモリ グローバル パラメーターを調整することもできます。 

 
## <a name="next-steps"></a>次のステップ

サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
