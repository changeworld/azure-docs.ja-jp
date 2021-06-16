---
title: Azure Database for MySQL 単一サーバーの新機能
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービスである Azure Database for MySQL 単一サーバーの最近の更新について説明します。
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 6b77ede348073dd09c0ba44bfe282d3bf546a092
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958546"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 単一サーバーの新機能

Azure Database for MySQL は、Microsoft クラウドのリレーショナル データベース サービスです。 このサービスは、[MySQL コミュニティ エディション](https://www.mysql.com/products/community/) (GPLv2 ライセンスで入手可能) のデータベース エンジンを基にしており、バージョン 5.6、5.7、および 8.0 をサポートしています。 [Azure Database for MySQL 単一サーバー](./overview.md#azure-database-for-mysql---single-server)は、最小限の要件でデータベースをカスタマイズできる、フル マネージド データベース サービスを提供するデプロイ モードです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、セキュリティなど、データベース管理機能のほとんどを、最小限のユーザー構成と制御ですべて処理するよう設計されています。

この記事では、2021 年 1 月に利用開始される Azure Database for MySQL 単一サーバーの新しいリリースと機能について説明します。 リストは新しい順に記載されており、最新の更新が最初に示されています。

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
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL の価格](https://azure.microsoft.com/pricing/details/mysql/server/)についての詳細を確認します。
- Azure Database for MySQL 単一サーバー用の[公開ドキュメント](./single-server/index.yml)を参照します。
- [一般的なエラーのトラブルシューティング](./howto-troubleshoot-common-errors.md)について詳しく検討します。