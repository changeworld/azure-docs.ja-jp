---
title: ネットワークの概要 - Azure Database for MySQL フレキシブル サーバー
description: Azure Database for MySQL のフレキシブル サーバー デプロイ オプションの接続およびネットワークのオプションについて説明します
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 2ae111660b2faa6f5bba17e9f94b17caa2b2c642
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438203"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL の接続およびネットワークの概念 - フレキシブル サーバー

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、Azure MySQL フレキシブル サーバーへの接続を制御するための概念について説明します。 Azure でサーバーを安全に作成してアクセスするための、Azure Database for MySQL フレキシブル サーバーのネットワークの概念について詳しく説明します。

Azure Database for MySQL - フレキシブル サーバーでは、サーバーへの接続を構成する 2 種類の方法がサポートされています。
> [!NOTE]
> サーバーを作成した後にネットワーク オプションを変更することはできません。

   * **プライベート アクセス (VNet 統合)** [プライベート アクセス](./concepts-networking-vnet.md) [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) にフレキシブル サーバーを展開できます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 仮想ネットワーク内のリソースでは、プライベート IP アドレスを通した通信が可能です。
   
   * **パブリック アクセス (許可された IP アドレス)** [パブリック アクセス](./concepts-networking-public.md) フレキシブル サーバーには、パブリック エンドポイントを介してアクセスします。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 "許可された IP アドレス" という表現は、サーバーに対するアクセス許可を付与することを選択する、ある範囲の IP を指しています。 これらのアクセス許可は、**ファイアウォール規則** と呼ばれます。

## <a name="choosing-a-networking-option"></a>ネットワーク オプションの選択

以下の機能が必要な場合は、**プライベート アクセス (VNet 統合)** を選択します。
   * 同じ仮想ネットワーク内または[ピアリングされた仮想ネットワーク](../../virtual-network/virtual-network-peering-overview.md)内の Azure リソースから、フレキシブル サーバーに接続する
   * VPN または ExpressRoute を使用して Azure 以外のリソースからフレキシブル サーバーに接続する
   * パブリック エンドポイントがない

以下の機能が必要な場合は、**パブリック アクセス (許可された IP アドレス)** の方法を選択します。
   * 仮想ネットワークをサポートしていない Azure リソースから接続する
   * VPN または ExpressRoute によって接続されていない Azure 外部のリソースから接続する 
   * フレキシブル サーバーにパブリック エンドポイントがある

プライベート アクセス オプションとパブリック アクセス オプションのどちらの使用を選ぶかに応じて以下の特性が当てはまります。
* 許可された IP アドレスからの接続では、有効な資格情報を使用して MySQL サーバーに対する認証を行う必要がある
* ネットワーク トラフィックに対して[接続の暗号化](#tls-and-ssl)を使用できる
* サーバーには完全修飾ドメイン名 (fqdn) があります。 接続文字列の hostname プロパティには、IP アドレスではなく fqdn を使用することをお勧めします。
* どちらのオプションでも、データベース レベルやテーブル レベルではなく、サーバー レベルでアクセスが制御されます。 MySQL のロールのプロパティを使用して、データベース、テーブル、およびその他のオブジェクトのアクセスを制御します。


### <a name="unsupported-virtual-network-scenarios"></a>サポートされない仮想ネットワークのシナリオ

* パブリック エンドポイント (またはパブリック IP あるいは DNS) - 仮想ネットワークにデプロイされたフレキシブル サーバーに、パブリック エンドポイントを設けることはできません
* フレキシブル サーバーを仮想ネットワークおよびサブネットにデプロイした後、それを別の仮想ネットワークまたはサブネットに移動することはできません。 * フレキシブル サーバーをデプロイした後で、フレキシブル サーバーによって使用されている仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。
* サブネットにリソースを配置すると、そのサブネットのサイズ (アドレス空間) を増やすことはできません
* サーバーを作成した後で、パブリック アクセスからプライベート アクセスに変更することはできません。 ポイントインタイム リストアを使用することをお勧めします

[Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) を使用してプライベート アクセス (VNet 統合) を有効にする方法を確認します。

> [!NOTE]
> カスタム DNS サーバーを使用する場合は、Azure Database for MySQL - フレキシブル サーバーの FQDN を解決する DNS フォワーダーを使用する必要があります。 「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。

## <a name="hostname"></a>Hostname (ホスト名)
選択するネットワーク オプションに関係なく、フレキシブル サーバーに接続するときは、接続文字列に完全修飾ドメイン名 (FQDN) `<servername>.mysql.database.azure.com` を使用することをお勧めします。 

## <a name="tls-and-ssl"></a>TLS と SSL
Azure Database for MySQL フレキシブル サーバーでは、Secure Sockets Layer (SSL) とトランスポート層セキュリティ (TLS) の暗号化を使用した MySQL サーバーへのクライアント アプリケーションの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間の暗号化されたネットワーク接続を保証する業界標準のプロトコルであり、ユーザーがコンプライアンス要件に準拠できるようにします。

Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS 1.2) を使用する暗号化された接続が既定でサポートされ、TLS 1.0 と TLS 1.1 を使用する受信接続はすべて既定では拒否されます。 フレキシブル サーバーでの、暗号化された接続の強制または TLS のバージョンの構成は、構成および変更できます。 

次に、フレキシブル サーバーで使用できる SSL と TLS の設定のさまざまな構成を示します。

| シナリオ   | サーバー パラメーターの設定      | 説明                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL を無効にする (暗号化された接続) | require_secure_transport = OFF |レガシ アプリケーションで MySQL サーバーへの暗号化された接続がサポートされていない場合、require_secure_transport=OFF を設定すると、フレキシブル サーバーへの暗号化された接続の強制を無効にできます。|
|バージョン 1.2 より前の TLS で SSL を強制する | require_secure_transport = ON および tls_version = TLSV1 または TLSV1.1| レガシ アプリケーションで暗号化された接続がサポートされているが、バージョン 1.2 より前の TLS が必要な場合は、暗号化された接続を有効にできますが、アプリケーションでサポートされている TLS のバージョン (v1.0 または v1.1) での接続を許可するようにフレキシブル サーバーを構成します|
|TLS バージョン 1.2 で SSL を強制する (既定の構成)|require_secure_transport = ON および tls_version = TLSV1.2| これは、フレキシブル サーバーに推奨される既定の構成です。|
|TLS バージョン 1.3 で SSL を強制する (MySQL v8.0 以降でサポート)| require_secure_transport = ON および tls_version = TLSV1.3| これは、新しいアプリケーションの開発に便利であり、推奨されます|

> [!Note]
> フレキシブル サーバーでの SSL 暗号の変更はサポートされていません。 tls_version が TLS バージョン 1.2 に設定されている場合、FIPS 暗号スイートが既定で強制されます。 バージョン 1.2 以外の TLS の場合、SSL 暗号は、MySQL Community のインストールに付属している既定値に設定されます。

詳細については、[SSL/TLS を使用して接続する](how-to-connect-tls-ssl.md)方法に関するページを参照してください。 


## <a name="next-steps"></a>次のステップ
* [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) を使用してプライベート アクセス (VNet 統合) を有効にする方法を確認します
* [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) を使用してパブリック アクセス (許可される IP アドレス) を有効にする方法を確認します
* [TLS を使用する](how-to-connect-tls-ssl.md)方法を確認します
