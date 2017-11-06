---
title: "Azure Database for PostgreSQL のサーバー ログ | Microsoft Docs"
description: "Azure Database for PostgreSQL でクエリ ログとエラーログを生成します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 696af85cd5609171a719a7e77efbfcdeba0aaaaa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2017
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のサーバー ログ 
Azure Database for PostgreSQL ではクエリ ログとエラー ログが生成されます。 ただし、トランザクション ログへのアクセスはサポートされていません。 クエリとエラー ログを使用して、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復を行えます。 詳細については、「[Error Reporting and Logging (エラー レポートとログ記録)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)」を参照してください。

## <a name="access-server-logs"></a>サーバー ログへのアクセス
Azure PostgreSQL サーバーのエラー ログは、Azure Portal、[Azure CLI](howto-configure-server-logs-using-cli.md)、Azure REST API を使用して一覧表示およびダウンロードできます。

## <a name="log-retention"></a>ログのリテンション期間
システム ログのリテンション期間を設定するには、サーバーに関連付けられている **log\_retention\_period** パラメーターを使用します。 このパラメーターの単位は日数です。 既定値は 3 日間です。 最大値は 7 日間です。 サーバーには、保持されているログ ファイルを格納できるストレージが十分に割り当てられている必要があります。
ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。

## <a name="configure-logging-for-azure-postgresql-server"></a>Azure Database for PostgreSQL サーバーのログ記録の構成
サーバーに対してクエリとエラーのログ記録を有効にできます。 エラー ログには、自動バキューム、接続、チェックポイントなどの情報を含めることができます。

PostgreSQL DB インスタンスのクエリのログ記録を有効にするには、`log\_statement` と `log\_min\_duration\_statement` の 2 つのサーバー パラメーターを設定します。

**log\_statement** パラメーターは、どの SQL ステートメントをログに記録するかを制御します。 このパラメーターを ***all*** に設定して、すべてのステートメントを記録することをお勧めします。既定値は none です。

**log\_min\_duration\_statement** パラメーターでは、記録するステートメントの制限をミリ秒単位で設定します。 このパラメーター設定よりも長く実行されているすべての SQL ステートメントが記録されます。 既定ではこのパラメーターは無効になっており、マイナス 1 (-1) に設定されています。 このパラメーターを有効にすると、アプリケーションで最適化されていないクエリを追跡する際に役に立ちます。

**log\_min\_messages** を使用すると、サーバー ログに書き込まれるメッセージ レベルを制御できます。 既定値は "警告" です  

こうした設定の詳細については、「[Error Reporting and Logging (エラー レポートとログ記録)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)」を参照してください。 特に Azure Database for PostgreSQL サーバー パラメーターの構成については、「[Azure CLI を使用したサーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- Azure CLI コマンド ライン インターフェイスを使用してログにアクセスするには、「[Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス](howto-configure-server-logs-using-cli.md)」を参照してください。
- サーバー パラメーターの詳細については、「[Customize server configuration parameters using Azure CLI (Azure CLI を使用したサーバー構成パラメーターのカスタマイズ)](howto-configure-server-parameters-using-cli.md)」を参照してください。
