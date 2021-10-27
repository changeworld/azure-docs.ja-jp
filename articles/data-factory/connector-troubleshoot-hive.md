---
title: Hive コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics での Hive コネクタに関する問題のトラブルシューティング方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8412d5d49a0e457b42fce408440693fa5ffc6d4c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067353"
---
# <a name="troubleshoot-the-hive-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse での Hive コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse での Hive コネクタに関する一般的な問題のトラブルシューティングのための提案を示します。

## <a name="the-performance-difference-between-the-odbc-connector-and-the-hive-connector"></a>ODBC コネクタと Hive コネクタのパフォーマンスの相違

- **現象**: ODBC コネクタと Hive コネクタのパフォーマンスの相違に関する懸念があります。 

- **原因**: ODBC コネクタには独自のドライバーが必要です。このため、サードパーティのドライバーの品質が原因でパフォーマンスに相違が生じる可能性があります。

- **推奨事項**: 最初に Hive コネクタを使用します。 


## <a name="unexpected-response-received-from-the-server-when-connecting-to-the-hive-server-via-odbc"></a>ODBC 経由で Hive サーバーに接続するときに、予期しない応答をサーバーから受信する

- **現象**: ODBC のリンクされたサービスを使用して Hive サーバーに接続すると、次のエラー メッセージが表示されます: `ERROR [HY000] [Cloudera][DriverSupport] (1110) Unexpected response received from server. Please ensure the server host and port specified for the connection are correct and confirm if SSL should be enabled for the connection.`

- **原因**: Azure Data Factory でサポートされていない Kerberos 認証を使用しています。

- **推奨事項**: 次の手順をお試しください。 うまくいかない場合は、提供されているドライバーを確認してこの問題を解決してください。
    1. **kr5.ini** ファイルは、**C:\Program Files\MIT\Kerberos\bin** フォルダーにあります。
    2. システム変数にも `KRB5_CONFIG` と `KRB5CCNAME` を追加します。
    3. **krb.ini** ファイルを編集します。
    4. マシンから VM と SHIR をシャットダウンして再起動します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)