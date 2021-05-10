---
title: サーバーの概念 - Azure Database for MySQL
description: このトピックでは、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb8394de49c2c5daeae156a9316466928eded148
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628477"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Azure Database for MySQL のサーバーの概念

この記事では、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーとは

Azure Database for MySQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な MySQL サーバー コンストラクトと同じです。 具体的には、Azure Database for MySQL サービスは管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for MySQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳細については、[サポートされる Azure Database for MySQL データベース バージョン](./concepts-supported-versions.md)に関するページをご覧ください。

Azure Database for MySQL サーバー内には 1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを使用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、仮想コア、ストレージ (GB) の構成に基づき、サーバーごとに構造化されています。 詳細については、「[価格レベル](./concepts-pricing-tiers.md)」をご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーへの接続および認証方法

次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

| セキュリティの概念 | 説明     |
| :-- | :-- |
| **認証と権限承認** | Azure Database for MySQL サーバーは、ネイティブ MySQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、MySQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」を参照してください。 |
| **SSL** | アプリケーションとデータベース サーバーの間に SSL 接続を適用できます。  「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。 |

## <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL を停止または開始する

Azure Database for MySQL を使用すると、使用されていないときにサーバーを **停止** し、アクティビティを再開したらサーバーを **開始** することができます。 これは基本的に、データベース サーバーのコストを節約し、使用したリソースに対してのみ課金されるようにするために行われます。 これは、開発テスト ワークロードの場合、および 1 日の一部でしかサーバーを使用しない場合に、いっそう重要になります。 サーバーを停止すると、すべてのアクティブな接続が切断されます。 後で、サーバーをオンラインに戻すときは、[Azure portal](how-to-stop-start-server.md) または [CLI](how-to-stop-start-server.md) のいずれかを使用できます。

サーバーが **停止** 状態にある場合、サーバーのコンピューティングは課金されません。 ただし、サーバーが再び起動されたときにデータ ファイルを使用できるようにするため、サーバーのストレージは維持されているため、ストレージは引き続き課金されます。

> [!IMPORTANT]
> サーバーを **停止** すると、それ以降連続して 7 日間は、その状態のままになります。 この期間内に手動で **開始** しないと、サーバーは 7 日の終わりに自動的に開始されます。 サーバーを使用していない場合は、再び **停止** することができます。

サーバーが停止されている間、サーバーで管理操作を実行することはできません。 サーバーの構成設定を変更するには、[サーバーを起動する](how-to-stop-start-server.md)必要があります。

### <a name="limitations-of-stopstart-operation"></a>停止/開始操作の制限事項
- 読み取りレプリカ構成 (ソースとレプリカの両方) ではサポートされていません。

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法

Azure portal または Azure CLI を使用して、Azure Database for MySQL サーバーの作成、削除、サーバー パラメーター構成 (my.cnf)、スケーリング、ネットワーク、セキュリティ、高可用性、バックアップと復元、監視を管理できます。 また、Azure Database for MySQL サーバーではスーパー ユーザー特権がサポートされていませんが、必要となる特定のデータベース管理タスクを実行するための以下のストアド プロシージャが用意されています。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|該当なし|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で実行されているステートメントを終了した後、指定された processlist_id に関連する接続を終了します。|
|*mysql.az_kill_query*|processlist_id|該当なし|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で現在実行されているステートメントを終了します。 接続自体をアクティブのままにします。|
|*mysql.az_load_timezone*|該当なし|なし|[タイム ゾーン テーブル](howto-server-parameters.md#working-with-the-time-zone-parameter)を読み込み、`time_zone`パラメーターを名前付きの値に設定できるようにします (例: "US/Pacific")。|

## <a name="next-steps"></a>次のステップ

- サービスの概要については、[Azure Database for MySQL の概要](./overview.md)に関するページをご覧ください
- **価格レベル** に基づく特定のリソース クォータと制限については、[価格レベル](./concepts-pricing-tiers.md)に関するページをご覧ください。
- サービスへの接続については、「[Azure Database for MySQL の接続ライブラリ](./concepts-connection-libraries.md)」を参照してください。
