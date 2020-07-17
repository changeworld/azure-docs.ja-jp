---
title: TLS - Azure Database for PostgreSQL - 単一サーバー
description: Azure Database for PostgreSQL - 単一サーバーの TLS 接続を構成する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141743"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) で TLS 接続を構成する

Azure Database for PostgreSQL では、トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) を使用してクライアント アプリケーションを PostgreSQL サービスに接続することが推奨されます。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。

既定では、PostgreSQL データベース サービスは TLS 接続を要求するように構成されます。 クライアント アプリケーションで TLS 接続がサポートされていない場合は、TLS の要求を無効にすることもできます。

## <a name="enforcing-tls-connections"></a>TLS 接続の適用

Azure portal や CLI を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーで、TLS 接続の適用が既定で有効になります。 

同様に、Azure portal のサーバー下にある [接続文字列] 設定で事前定義された接続文字列には、TLS を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 TLS パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="configure-enforcement-of-tls"></a>TLS 適用の構成

必要に応じて、TLS 接続の適用を無効にできます。 Microsoft Azure では、セキュリティ強化のため **[Enforce SSL connection (SSL 接続の適用)]** 設定は常に有効にしておくことをお勧めします。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure Database for PostgreSQL サーバーにアクセスし、 **[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、 **[Enforce SSL connection] \(SSL 接続の適用)** 設定を有効または無効にします。 その後、 **[保存]** をクリックします。

![接続のセキュリティ ‐ TLS/SSL 適用の無効化](./media/concepts-ssl-connection-security/1-disable-ssl.png)

この設定は、 **[概要]** ページの **SSL 適用ステータス** インジケーターで確認できます。

### <a name="using-azure-cli"></a>Azure CLI の使用

**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ `Enabled` 値と `Disabled` 値を使用します。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認

データベース サービスに PostgreSQL を使用しているアプリケーション フレームワークの中には、インストール時に既定で TLS が有効にならないものもあります。 PostgreSQL サーバーが TLS 接続を適用していても、アプリケーションで TLS が構成されていない場合、アプリケーションはデータベース サーバーに接続できない可能性があります。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 接続で証明書検証を必要とするアプリケーション

安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for PostgreSQL サーバーに接続するための証明書は、 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem にあります。 証明書ファイルをダウンロードし、希望の場所に保存します。

### <a name="connect-using-psql"></a>psql を使用した接続

次の例は、psql コマンド ライン ユーティリティを使用して PostgreSQL サーバーに接続する方法を示しています。 `sslmode=verify-full` 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `sslrootcert` パラメーターに渡します。

次のコマンドは、psql 接続文字列の例です。

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> `sslrootcert` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)に関する記事で、さまざまなアプリケーション接続オプションを確認します。
