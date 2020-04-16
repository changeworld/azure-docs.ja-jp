---
title: TLS - Hyperscale (Citus) - Azure Database for PostgreSQL
description: TLS 接続を正しく使用するように Azure Database for PostgreSQL - Hyperscale (Citus) と関連アプリケーションを構成するための手順および情報。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422332"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) での TLS の構成
Hyperscale (Citus) コーディネーター ノードへのクライアント アプリケーション接続には、トランスポート層セキュリティ (TLS) が必要です。これは、以前は Secure Sockets Layer (SSL) と呼ばれていました。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。

## <a name="enforcing-tls-connections"></a>TLS 接続の適用
Azure portal を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーでは、TLS 接続の適用が既定で有効になります。 

同様に、Azure portal のサーバー下にある [接続文字列] 設定で事前定義された接続文字列には、TLS を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 TLS パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認
データベース サービスに PostgreSQL を使用しているアプリケーション フレームワークの中には、インストール時に既定で TLS が有効にならないものもあります。 PostgreSQL サーバーが TLS 接続を適用していても、アプリケーションで TLS が構成されていない場合、アプリケーションはデータベース サーバーに接続できない可能性があります。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 接続で証明書検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL - Hyperscale (Citus) に接続するための証明書は、 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem にあります。 証明書ファイルをダウンロードし、希望の場所に保存します。

### <a name="connect-using-psql"></a>psql を使用した接続
次の例は、psql コマンド ライン ユーティリティを使用して Hyperscale (Citus) コーディネーター ノードに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、TLS 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

psql の接続文字列の例を次に示します。
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="next-steps"></a>次のステップ
[Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則](concepts-hyperscale-firewall-rules.md)を使用して、セキュリティをさらに強化します。
