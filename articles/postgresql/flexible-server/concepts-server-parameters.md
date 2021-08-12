---
title: サーバー パラメーター - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーのサーバー パラメーターについて説明する
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/04/2021
ms.openlocfilehash: 662faef8b7a3afbf3d3d3b021c52754af3a3c0bc
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559916"
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
 
## <a name="next-steps"></a>次のステップ

サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
