---
title: ゲートウェイ トラフィックの移行に関する注意事項
description: Azure SQL Database ゲートウェイの IP アドレスの移行についてユーザーにお知らせする記事です
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 22bfab5b9f00a392054fa1aef6a93195180fd968
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373489"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database トラフィックの新しいゲートウェイへの移行
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure インフラストラクチャの機能強化に伴い、Microsoft は可能な限り最高のカスタマー エクスペリエンスを提供するためにハードウェアを定期的に更新しています。 今後数か月間で、より新しいハードウェア世代に基づくゲートウェイを追加し、それらにトラフィックを移行して、最終的に一部のリージョンでは以前のハードウェアに基づくゲートウェイの使用を停止する予定です。  

各リージョンで使用できるゲートウェイが変更される場合は、事前にメールと Azure portal でお客様にお知らせします。 最新の情報は、「[Azure SQL Database ゲートウェイ IP アドレス](connectivity-architecture.md#gateway-ip-addresses)」の表に保持される予定です。

## <a name="status-updates"></a>ステータスの更新

# <a name="in-progress"></a>[[実行中]](#tab/in-progress-ip)
### <a name="september-2020"></a>2020 年 9 月

新しい SQL ゲートウェイが、次のリージョンに追加されます。

- 北ヨーロッパ:13.74.104.113 
- 米国西部 2:40.78.248.10 
- 西ヨーロッパ:52.236.184.163 
- 米国中南部:20.45.121.1、20.49.88.1 

既存の SQL ゲートウェイで、次のリージョンのトラフィックの受け入れを開始します。
- 東日本:40.79.184.8、40.79.192.5

これらの SQL ゲートウェイは、2020 年 9 月 1 日にお客様のトラフィックの受け入れを開始します。 

### <a name="august-2020"></a>2020 年 8 月

新しい SQL ゲートウェイが、次のリージョンに追加されます。

- オーストラリア東部: 13.70.112.9
- カナダ中部: 52.246.152.0、20.38.144.1 
- 米国西部 2: 40.78.240.8

これらの SQL ゲートウェイは、2020 年 8 月 10 日にお客様のトラフィックの受け入れを開始します。 

# <a name="completed"></a>[完了](#tab/completed-ip)

次のゲートウェイの移行が完了しました。 

### <a name="october-2019"></a>2019 年 10 月
- ブラジル南部
- 米国西部
- 西ヨーロッパ
- 米国東部
- 米国中部
- 東南アジア
- 米国中南部
- 北ヨーロッパ
- 米国中北部
- 西日本
- 東日本
- 米国東部 2
- 東アジア

---

## <a name="impact-of-this-change"></a>この変更の影響

トラフィックの移行により、Azure SQL Database のデータベースに対して DNS で解決されるパブリック IP アドレスが変更される場合があります。
次の場合に影響を受ける可能性があります。

- オンプレミスのファイアウォールで特定のゲートウェイに対して IP アドレスをハードコーディングしている
- サービス エンドポイントとして Microsoft.SQL を使用しているサブネットがあるが、ゲートウェイ IP アドレスと通信できない
- データベースに対して[ゾーン冗長構成](high-availability-sla.md#zone-redundant-configuration)を使用している

次の場合は影響を受けません。

- 接続ポリシーとしてのリダイレクトがある
- Azure の内部から SQL Database へのサービス タグを使用した接続がある
- サポートされているバージョンの SQL Server 用 JDBC ドライバーを使用して行われる接続は影響を受けません。 サポートされている JDBC のバージョンについては、「[Microsoft SQL Server 用 JDBC Driver のダウンロード](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)」を参照してください。

## <a name="what-to-do-you-do-if-youre-affected"></a>影響を受ける場合の対処方法

リージョンの TCP ポート 1433 上のすべての[ゲートウェイ IP アドレス](connectivity-architecture.md#gateway-ip-addresses)、およびポート範囲 11000-11999 の IP アドレスへの送信トラフィックを許可することをお勧めします。 この推奨事項は、オンプレミスから接続しているクライアントと、サービス エンドポイント経由で接続しているクライアントに適用されます。 ポート範囲の詳細については、「[接続ポリシー](connectivity-architecture.md#connection-policy)」を参照してください。

バージョン 4.0 より前の Microsoft JDBC ドライバーを使用しているアプリケーションからの接続は、証明書の検証に失敗する可能性があります。 以前のバージョンの Microsoft JDBC は、証明書のサブジェクト フィールドにある共通名 (CN) に依存しています。 軽減策は、hostNameInCertificate プロパティを *.database.windows.net に設定することです。 hostNameInCertificate プロパティを設定する方法の詳細については、「[暗号化を使用した接続](/sql/connect/jdbc/connecting-with-ssl-encryption)」を参照してください。

上記の軽減策がうまくいかない場合は、URL https://aka.ms/getazuresupport を使用して SQL Database または SQL Managed Instance のサポート リクエストを提出してください。

## <a name="next-steps"></a>次のステップ

- [Azure SQL の接続アーキテクチャ](connectivity-architecture.md)について詳細を確認します。
