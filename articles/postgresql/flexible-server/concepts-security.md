---
title: Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ
description: Azure Database for PostgreSQL - フレキシブル サーバーのフレキシブル サーバー デプロイ オプションのセキュリティについて説明します
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 79e308605a0d6262e95dc6ff673d74a5a1eaaec7
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732160"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーのセキュリティ

Azure Database for PostgreSQL サーバーには、データを保護するために使用できるセキュリティのレイヤーが複数あります。 この記事では、これらのセキュリティ オプションについてまとめています。

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="in-transit"></a>転送中
 Azure Database for PostgreSQL では、トランスポート層セキュリティを使用して転送中のデータを暗号化することによってデータをセキュリティで保護します。 既定では、暗号化 (SSL/TLS) が適用されます。

### <a name="at-rest"></a>保存
Azure Database for PostgreSQL サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 データ (バックアップを含む) は、クエリの実行中に作成される一時ファイルも含めて、ディスク上で暗号化されます。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 これは、SQL Server または Oracle データベースの Transparent Data Encryption (TDE) などの他の保存されているときの暗号化テクノロジに似ています。 ストレージの暗号化は常にオンになっており、無効にすることはできません。


## <a name="network-security"></a>ネットワークのセキュリティ

Azure Database for PostgreSQL - フレキシブル サーバーを実行するときに、2 つの主要なネットワーク オプション選択できます。 これらのオプションは、プライベート アクセス (VNet 統合) とパブリック アクセス (許可されている IP アドレス) です。 プライベート アクセスを利用することで、フレキシブル サーバーが Azure Virtual Network にデプロイされます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 仮想ネットワーク内のリソースでは、プライベート IP アドレスを通した通信が可能です。
パブリック アクセスの場合、フレキシブル サーバーにはパブリック エンドポイントを介してアクセスされます。 パブリック エンドポイントはパブリックに解決可能な DNS アドレスであり、アクセスすると、既定ですべての接続をブロックするファイアウォールを介してセキュリティで保護されます。 



### <a name="ip-firewall-rules"></a>IP ファイアウォール規則
IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。 詳細については、[ファイアウォール規則の概要](concepts-firewall-rules.md)に関するページを参照してください。


### <a name="private-vnet-access"></a>プライベート VNET アクセス
Azure Virtual Network にフレキシブル サーバーを展開できます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 詳細については、[フレキシブル サーバー](concepts-networking.md)に関するページをご覧ください

### <a name="network-security-groups-nsg"></a>ネットワーク セキュリティ グループ (NSG)
ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。  詳細については、[ネットワーク セキュリティ グループの概要](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)に関するページをご覧ください

## <a name="access-management"></a>アクセス管理

Azure Database for PostgreSQL サーバーを作成するときに、管理者ロールの資格情報を指定します。 この管理者ロールは、追加の [PostgreSQL ロール](https://www.postgresql.org/docs/current/user-manag.html)を作成するために使用できます。

[Azure Active Directory (AAD) 認証](../concepts-aad-authentication.md)を使用してサーバーに接続することもできます。


### <a name="azure-defender-protection"></a>Azure Defender の保護

 現在、Azure Database for PostgreSQL - フレキシブルでは、[Azure Defender の保護](https://docs.microsoft.com/azure/security-center/azure-defender)をサポートしていません。 


[監査ログ](../concepts-audit.md)を使うと、データベースのアクティビティを追跡できます。 


## <a name="next-steps"></a>次のステップ
  - パブリック アクセス ネットワークの [IP](concepts-firewall-rules.md) のファイアウォール規則を有効にする
  - [Azure Database for PostgreSQL - フレキシブル サーバーを使用したプライベート アクセス ネットワーク](concepts-networking.md)について学習する
  - Azure Database for PostgreSQL の [Azure Active Directory 認証](../concepts-aad-authentication.md)について学習する