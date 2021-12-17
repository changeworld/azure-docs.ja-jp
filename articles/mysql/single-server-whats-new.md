---
title: Azure Database for MySQL 単一サーバーの新機能
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービスである Azure Database for MySQL 単一サーバーの最近の更新について説明します。
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 7d07518d29626448957525ee36e1170e4b912835
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707907"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 単一サーバーの新機能

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL は、Microsoft クラウドのリレーショナル データベース サービスです。 このサービスは、[MySQL Community Edition](https://www.mysql.com/products/community/) (GPLv2 ライセンスで利用可能) のデータベース エンジンを基にしており、バージョン 5.6 (廃止)、5.7、および 8.0 をサポートしています。 [Azure Database for MySQL 単一サーバー](./overview.md#azure-database-for-mysql---single-server)は、最小限の要件でデータベースをカスタマイズできる、フル マネージド データベース サービスを提供するデプロイ モードです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、セキュリティなど、データベース管理機能のほとんどを、最小限のユーザー構成と制御ですべて処理するよう設計されています。

この記事では、2021 年 1 月に利用開始される Azure Database for MySQL 単一サーバーの新しいリリースと機能について説明します。 リストは新しい順に記載されており、最新の更新が最初に示されています。

## <a name="october-2021"></a>2021 年 10 月

- **既知の問題**

MySQL 8.0.27 クライアントは Azure Database for MySQL - 単一サーバーと互換性がありません。 mysql.exe またはワークベンチ経由で作成された MySQL 8.0.27 クライアントからのすべての接続は失敗します。 回避策として、代わりに以前のバージョンのクライアント (MySQL 8.0.27 より前) を使用するか、[Azure Database for MySQL - フレキシブル サーバー](./flexible-server/overview.md)のインスタンスを作成することを検討してください。

## <a name="june-2021"></a>2021 年 6 月
  
Azure Database for MySQL 単一サーバーのこのリリースには、次の更新が行われています。

- **MySQL 8.0 のポータル/CLI からサーバー パラメーター `activate_all_roles_on_login` を変更する機能が有効**

  ユーザーは、Azure portal と CLI を使用して、activate_all_roles_on_login パラメーターの値を変更できるようになりました。 このパラメーターは、ユーザーがサーバーにサインインするときに、許可されたロールの自動アクティブ化を有効にするかどうかを構成するのに役立ちます。 詳細については、「[サーバー システム変数](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)」を参照してください。

- **MySQL Community バグ #29596969 および #94668 に対処**

  このリリースでは、フィールドが MySQL 8.0 の PRIMARY KEY としてマークされている場合に、CREATE TABLE クエリで既定の式が無視される問題に対処しています (MySQL Community バグ #29596969、バグ #94668)。 詳細については、「[MySQL Bugs: #94668: Expression Default is made NULL during CREATE TABLE query, if field is made PK](https://bugs.mysql.com/bug.php?id=94668)」を参照してください。

- **"SHOW TABLE" クエリでのテーブル名の重複に関する問題に対処**

  テーブル操作中にテーブル キャッシュをきめ細かく制御する新しい関数が導入されました。 新機能のコードの欠陥により、ディレクトリ キャッシュ内のエントリが構成されていないか追加されていない可能性があります。また、同じ名前の 2 つのテーブルを返すなど、予期しない動作が発生する可能性があります。 ディレクトリ キャッシュは、"SHOW TABLE" 関連のクエリに対してのみ機能します。DML クエリや DDL クエリには影響しません。 この問題は今回のリリースで完全に解決されました。

- **一時テーブルのディスクへの書き込みを減らすために、サーバー パラメーター `max_heap_table_size` の既定値を増やしました**

  このリリースでは、パラメーター `max_heap_table_size` の最大許容値が、General Purpose 64 仮想コアおよびメモリ最適化 32 仮想コア向けの 8589934592 に変更されました。

- **ポータルからパラメーターの `sql_require_primary_key` 値を設定する問題に対処**

  ユーザーは、パラメーター `sql_require_primary_key` の値を Azure portal から直接変更できるようになりました。

- **計画メンテナンス通知の一般提供**

  このリリースでは、Azure Database for MySQL - 単一サーバーでの計画メンテナンス通知の一般提供が用意されています。 詳細については、「[計画メンテナンスの通知](concepts-planned-maintenance-notification.md)」の記事を参照してください。

- **パラメーター `redirect_enabled` が既定で有効**

  このリリースでは、パラメーター `redirect_enabled` が既定で有効になります。 リダイレクトは、アプリケーションをバックエンド サーバー ノードに直接接続できるようにすることで、クライアント アプリケーションと MySQL サーバー間のネットワーク待機時間を短縮することを目的としています。 PHP アプリケーションでのリダイレクトのサポートは、Microsoft によって開発された [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 拡張機能を通じて利用できます。 詳細については、「[リダイレクトを使用して Azure Database for MySQL に接続する](howto-redirection.md)」を参照してください。

>[!Note]
> * リダイレクトは、プライベート リンクのセットアップでは機能しません。 Azure Database for MySQL のプライベート リンクを使用している場合は、接続の問題が発生する可能性があります。 この問題を解決するには、パラメーター redirect_enabled を "OFF" に設定し、クライアント アプリケーションを再起動してください。</br>
> * mysqlnd_azure リダイレクト ドライバーを使用して Azure Database for MySQL に接続する PHP アプリケーションがある場合 (リダイレクトが既定で有効になっている場合)、挿入トランザクションに影響するデータ エンコードの問題が発生する可能性があります。</br>
> この問題を解決するには、次のいずれかを実行します。
>    - Azure portal で、redirect_enabled パラメーターを "OFF" に設定してリダイレクトを無効にし、変更後のドライバー キャッシュをクリアするために、PHP アプリケーションを再起動します。
>     - 接続が確立された後の設定に基づいて、charset 関連のパラメーターをセッション レベルで明示的に設定します (例: "set names utf8mb4" など)。

## <a name="february-2021"></a>2021 年 2 月

Azure Database for MySQL 単一サーバーのこのリリースには、次の更新が行われています。

- バージョン 5.7 および 8.0 大容量ストレージ サーバーのデータインのグローバル トランザクション識別子 (GTID) をサポートする新しいストアド プロシージャが追加されました。
- MySQL バージョン 5.6.50 および 5.7.32 までをサポートするように更新されました。

## <a name="january-2021"></a>2021 年 1 月

Azure Database for MySQL 単一サーバーのこのリリースには、次の更新が行われています。

- 最初の管理者アクセス許可を自動的に修正するための "パスワードのリセット" が有効になりました。
- `auto_increment_increment/auto_increment_offset` サーバー パラメーター と `session_track_gtids` が公開されました。
- コントロール innodb バッファー プール ダンプ/復元用の新しいストアド プロシージャが追加されました。
- 大容量ストレージ サーバー用の innodb ウォームアップ関連のサーバー パラメーターが公開されました。

## <a name="contacts"></a>連絡先

Azure Database for MySQL についての質問や提案がある場合は、Azure Database for MySQL チームにお問い合わせください ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、<bpt id="p1">[</bpt>UserVoice<ept id="p1">](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)</ept> でエントリを作成します。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL の価格](https://azure.microsoft.com/pricing/details/mysql/server/)についての詳細を確認します。
- Azure Database for MySQL 単一サーバー用の[公開ドキュメント](./single-server/index.yml)を参照します。
- [一般的なエラーのトラブルシューティング](./howto-troubleshoot-common-errors.md)について詳しく検討します。