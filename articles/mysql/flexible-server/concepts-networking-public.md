---
title: 公衆ネットワーク アクセスの概要 - Azure Database for MySQL フレキシブル サーバー
description: Azure Database for MySQL のフレキシブル サーバー デプロイ オプションのパブリック アクセス ネットワークのオプションについて説明します
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: a8d2e1e6ef3f1aa2dcb736336232b1b149eb7b89
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438204"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL の公衆ネットワーク アクセス - フレキシブル サーバー

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、サーバーのパブリック接続オプションについて説明します。 また、インターネット経由で安全にアクセスできる Azure Database for MySQL フレキシブル サーバーを作成するための概念についても詳しく説明します。

## <a name="public-access-allowed-ip-addresses"></a>パブリック アクセス (許可された IP アドレス)

お使いのフレキシブル サーバーでパブリック アクセスを構成すると、インターネット経由でアクセスできるパブリック エンドポイントを介してこのサーバーにアクセスできるようになります。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 "許可された IP アドレス" という表現は、サーバーに対するアクセス許可を付与することを選択する、ある範囲の IP を指しています。 これらのアクセス許可は、ファイアウォール規則と呼ばれます。

パブリック アクセスの方法には、以下のような特性があります。

* 許可する IP アドレスだけが、MySQL フレキシブル サーバーにアクセスするためのアクセス許可を持ちます。 既定では、許可される IP アドレスはありません。 IP アドレスは、サーバーの作成時に追加すること、または後から追加することができます。
* MySQL サーバーにはパブリックに解決できる DNS 名がある
* どの Azure 仮想ネットワークにもフレキシブル サーバーが存在しない
* サーバーとの間のネットワーク トラフィックは、プライベート ネットワークを経由しません。 このトラフィックでは、一般的なインターネット経路が使用されます。

### <a name="firewall-rules"></a>ファイアウォール規則

IP アドレスへのアクセス許可を付与することは、ファイアウォール規則と呼ばれます。 許可されていない IP アドレスから接続が試みられた場合、発信元のクライアントにエラーが表示されます。

### <a name="allowing-all-azure-ip-addresses"></a>Azure のすべての IP アドレスの許可

ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、すべての Azure データセンターの IP アドレスからの接続を有効にすることを検討できます。

> [!IMPORTANT]
> **[Azure サービスおよび Azure 内のリソースからのパブリック アクセスを許可する]** オプションを選択すると、他の顧客のサブスクリプションからの接続を含め、Azure サービスからのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

[Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) を使用して、パブリック アクセス (許可される IP アドレス) を有効にし、管理する方法を確認します。

### <a name="troubleshooting-public-access-issues"></a>パブリック アクセスに関する問題をトラブルシューティングする

Microsoft Azure Database for MySQL サーバー サービスに対するアクセスが予期したように動作しない場合は、以下の点について考えてください。

* **許可リストへの変更がまだ反映されていない:** Azure Database for MySQL サーバー ファイアウォールの構成に対する変更が反映されるまで、最大 5 分の遅延が発生する場合があります。

* **認証に失敗した:** その Azure Database for MySQL サーバーで、ユーザーがアクセス許可を持っていないか、使用したパスワードが正しくない場合、Azure Database for MySQL サーバーへの接続は拒否されます。 ファイアウォール設定の作成によってクライアントに提供されるのは、サーバーへの接続を試行する機会のみです。 各クライアントは、必要なセキュリティ資格情報を提供する必要があることに変わりはありません。

* **動的なクライアント IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

  * Azure Database for MySQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲については、お使いのインターネット サービス プロバイダー (ISP) に問い合わせてください。その後、その IP アドレス範囲をファイアウォール規則として追加します。
  * 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

* **ファイアウォール規則が IPv6 形式で使用できない:** ファイアウォール規則は IPv4 形式である必要があります。 IPv6 形式でファイアウォール規則を指定すると、検証エラーが表示されます。

## <a name="next-steps"></a>次のステップ

* [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) を使用してパブリック アクセス (許可される IP アドレス) を有効にする方法を確認します
* [TLS を使用する](how-to-connect-tls-ssl.md)方法を確認します
