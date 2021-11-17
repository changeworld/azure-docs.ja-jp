---
title: Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ
description: Azure Database for PostgreSQL のフレキシブル サーバー デプロイ オプションのセキュリティについて確認してください。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 10/01/2021
ms.openlocfilehash: f31c013bbdf12f34cd73495e3885e17a35b368ef
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312288"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ

Azure Database for PostgreSQL サーバーで、データを保護するために複数のセキュリティのレイヤーを使用できます。 この記事では、これらのセキュリティ オプションについてまとめています。

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

Azure Database for PostgreSQL は、次の 2 つの方法でデータを暗号化します。

- **転送中のデータ**: Azure Database for PostgreSQL は、Secure Sockets Layer とトランスポート層セキュリティ (SSL/TLS) を使用して転送中のデータを暗号化します。 暗号化は既定で適用されます。 詳しくは、こちらの[ガイド](how-to-connect-tls-ssl.md)をご覧ください。 セキュリティの強化に、[SCRAM 認証](how-to-connect-scram.md)を有効にすることも選択できます。

   推奨されませんが、必要に応じて `require_secure_transport` サーバー パラメーターをオフに更新することで、Azure Database for PostgreSQL - フレキシブル サーバーへの接続の TLS\SSL を無効にすることもできます。 また、`ssl_min_protocol_version` と `ssl_max_protocol_version` のサーバー パラメーターを設定することで、TLS バージョンを設定することもできます。


- **保存データ**: ストレージ暗号化については、Azure Database for PostgreSQL は FIPS 140-2 で検証された暗号モジュールを使用します。 データは、バックアップと、クエリの実行中に作成される一時ファイルも含め、ディスク上で暗号化されます。 

  このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 これと同様の他の保存時暗号化テクノロジが、SQL Server や Oracle データベースの透過的データ暗号化のようなものです。 ストレージの暗号化は常にオンになっており、無効にすることはできません。


## <a name="network-security"></a>ネットワークのセキュリティ

Azure Database for PostgreSQL - フレキシブル サーバーを実行しているときは、主なネットワーク オプションとして次の 2 つがあります。

- **プライベート アクセス**: サーバーを Azure 仮想ネットワークにデプロイできます。 Azure 仮想ネットワークは、非公開で、セキュリティで保護されたネットワーク通信の提供に役立ちます。 仮想ネットワーク内のリソースでは、プライベート IP アドレスを通した通信が可能です。 詳細については、[Azure Database for PostgreSQL - フレキシブル サーバーのネットワークの概要](concepts-networking.md)に関するページを参照してください。

  ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 詳細については、[ネットワーク セキュリティ グループの概要](../../virtual-network/network-security-groups-overview.md)に関するページをご覧ください。

- **パブリック アクセス**: サーバーには、パブリック エンドポイントを介してアクセスできます。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 そこへのアクセスは、既定ですべての接続をブロックするファイアウォールによってセキュリティ保護されます。 

  IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。 詳細については、[ファイアウォール規則の概要](concepts-firewall-rules.md)に関する記事を参照してください。

## <a name="access-management"></a>アクセス管理

Azure Database for PostgreSQL サーバーを作成しているときに、管理者ロールの資格情報を指定します。 この管理者ロールを使用して、追加の [PostgreSQL ロール](https://www.postgresql.org/docs/current/user-manag.html)を作成することができます。

たとえば、次のように入力します。

```SQL
postgres=> create role demouser with password 'password123';
```

お使いのサーバーのロール一覧を定期的に監査することができます。 たとえば、`psql` クライアントを使用して接続し、`pg_roles` テーブルにクエリを実行すると、すべてのロールが、ロールの追加作成、データベースの作成、レプリケーションなどの権限と共に一覧表示されます。 

```SQL
postgres=> \x
Expanded display is on.
postgres=> select * from pg_roles where rolname='demouser';
-[ RECORD 1 ]--+---------
rolname        | demouser
rolsuper       | f
rolinherit     | t
rolcreaterole  | f
rolcreatedb    | f
rolcanlogin    | f
rolreplication | f
rolconnlimit   | -1
rolpassword    | ********
rolvaliduntil  |
rolbypassrls   | f
rolconfig      |
oid            | 24827

```

また、フレキシブル サーバーでは、お使いのデータベースのアクティビティを追跡する[ログの監査](concepts-audit.md)も使用できます。 

> [!NOTE]
> 現在、Azure Database for PostgreSQL - フレキシブル サーバーでは、[Microsoft Defender for Cloud の保護](../../security-center/azure-defender.md)はまだサポートされていません。 

## <a name="updating-passwords"></a>パスワードの更新

セキュリティの強化に、管理者パスワードとデータベースのユーザー パスワードを定期的にローテーションすることが推奨されます。 大文字と小文字、数字、および特殊文字を使用して、堅牢なパスワードを使用することが推奨されます。

### <a name="reset-administrator-password"></a>管理者パスワードのリセット

管理者パスワードをリセットするには、[手引き](./how-to-manage-server-portal.md#reset-admin-password)に従います。

### <a name="update-database-user-password"></a>データベース ユーザー パスワードの更新

クライアント ツールを使用して、データベース ユーザーのパスワードを更新できます。 たとえば、次のように入力します。
```SQL
postgres=> alter role demouser with password 'Password123!';
ALTER ROLE
```
## <a name="next-steps"></a>次のステップ
- パブリック アクセス ネットワークの [IP アドレスのファイアウォール規則](concepts-firewall-rules.md)を有効にする。
- [Azure Database for PostgreSQL - フレキシブル サーバーを使用したプライベート アクセス ネットワーク](concepts-networking.md)について学習する。
- Azure Database for PostgreSQL の [Azure Active Directory 認証](../concepts-aad-authentication.md)について学習する。
