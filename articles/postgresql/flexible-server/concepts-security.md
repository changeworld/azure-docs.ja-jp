---
title: Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ
description: Azure Database for PostgreSQL のフレキシブル サーバー デプロイ オプションのセキュリティについて確認してください。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 6fcd772fce8a3f5e869f12e9b20c1219adf8155b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866857"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ

Azure Database for PostgreSQL サーバーで、データを保護するために複数のセキュリティのレイヤーを使用できます。 この記事では、これらのセキュリティ オプションについてまとめています。

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

Azure Database for PostgreSQL は、次の 2 つの方法でデータを暗号化します。

- **転送中のデータ**: Azure Database for PostgreSQL は、Secure Sockets Layer とトランスポート層セキュリティ (SSL/TLS) を使用して転送中のデータを暗号化します。 暗号化は既定で適用されます。
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

[Azure Active Directory 認証](../concepts-aad-authentication.md)を使用してサーバーに接続することもできます。 [監査ログ](../concepts-audit.md)を使うと、データベースのアクティビティを追跡できます。 

> [!NOTE]
> 現在、Azure Database for PostgreSQL - フレキシブル サーバーでは、[Azure Defender の保護](../../security-center/azure-defender.md)をサポートしていません。 

## <a name="next-steps"></a>次のステップ
- パブリック アクセス ネットワークの [IP アドレスのファイアウォール規則](concepts-firewall-rules.md)を有効にする。
- [Azure Database for PostgreSQL - フレキシブル サーバーを使用したプライベート アクセス ネットワーク](concepts-networking.md)について学習する。
- Azure Database for PostgreSQL の [Azure Active Directory 認証](../concepts-aad-authentication.md)について学習する。