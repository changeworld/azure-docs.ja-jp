---
title: Azure Database for PostgreSQL - フレキシブル サーバーにおけるファイアウォール規則
description: この記事では、ファイアウォール規則を使用して、パブリック ネットワーク デプロイ オプションを備えた Azure Database for PostgreSQL - フレキシブル サーバーに接続する方法について説明します。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866870"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーにおけるファイアウォール規則
Azure Database for PostgreSQL - フレキシブル サーバーを実行しているとき、主なネットワーク オプションとして次の 2 つがあります。 これらのオプションは、プライベート アクセス (仮想ネットワーク統合)とパブリック アクセス (許可されている IP アドレス) です。 

パブリック アクセスを使用すると、Azure Database for PostgreSQL サーバーへのアクセスはパブリック エンドポイント経由となります。 既定では、ファイアウォールによってサーバーへのすべてのアクセスがブロックされます。 サーバーにアクセスできる IP ホストを指定するには、サーバー レベルの *ファイアウォール規則* を作成します。 ファイアウォール規則は、許可されるパブリック IP アドレス範囲を指定します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。

Microsoft Azure portal または Azure CLI コマンドを使用してファイアウォール規則を作成できます。 サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。

サーバー レベルのファイアウォール規則は、同じ Azure Database for PostgreSQL サーバー上のすべてのデータベースに適用されます。 ルールは、Azure portal の Web サイトへのアクセスに影響しません。

以下の図のとおり、インターネットや Azure からの接続試行は、PostgreSQL データベースに到達する前に、ファイアウォールを通過する必要があります。

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="ファイアウォールのしくみの概要を示す図。":::

## <a name="connect-from-the-internet"></a>インターネットからの接続
要求のソース IP アドレスがサーバーレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。 それ以外の場合は拒否されます。 

たとえば、アプリケーションが PostgreSQL の Java Database Connectivity (JDBC) ドライバーで接続している場合、ファイアウォールが接続をブロックしているため、次のエラーが発生する可能性があります。

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException:FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connect-from-azure"></a>Azure から接続する
任意のアプリケーションまたはサービスの発信 IP アドレスを見つけ、これらの個々の IP アドレスまたは範囲へのアクセスを明示的に許可することをお勧めします。 例えば、Azure App Service アプリの発信 IP アドレスを見つけたり、仮想マシンに結びついているパブリック IP アドレスを利用したりすることができます。 

ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、Azure データセンターのすべての IP アドレスからの接続を有効にすることを検討します。

1. Azure portal の **[ネットワーク]** ペインで、 **[Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する]** チェックボックスを選択します。 
1. **[保存]** を選択します。 

> [!IMPORTANT]
> **[Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する]** オプションを選択すると、他の顧客のサブスクリプションからの接続を含め、Azure サービスからのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、サインインおよびユーザーのアクセス許可が制限されていることを確認してください。 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="ポータルで Azure サービスへのアクセスを許可する選択を示すスクリーンショット。":::

## <a name="programmatically-manage-firewall-rules"></a>プログラムによってファイアウォール規則を管理する
ファイアウォール規則は、Microsoft Azure portal の使用に加え、Azure CLI を使用してプログラムで管理することができます。 

Azure CLI から、開始アドレスと終了アドレスが 0.0.0.0 のファイアウォール規則設定では、ポータルの **[Azure 内の任意の Azure サービスからこのサーバーへのパブリック アクセスを許可する]** オプションに相当します。 接続試行がファイアウォール規則によって拒否された場合、そのアプリは Azure Database for PostgreSQL サーバーに到達しません。

## <a name="troubleshoot-firewall-problems"></a>ファイアウォールの問題のトラブルシューティング
Azure Database for PostgreSQL サーバーに期待どおりにアクセスできない場合は、次の可能性を検討してください。

* **許可リストに対する変更がまだ有効にされていない**: Azure Database for PostgreSQL サーバーのファイアウォール構成に対する変更には、最大で 5 分かかる場合があります。

* **サインインが許可されない、または正しくないパスワードが使用された**: Azure Database for PostgreSQL サーバーでは、サインインのアクセス許可がないか、パスワードが正しくない場合、サーバーへの接続は拒否されます。 ファイアウォール設定を作成しても、クライアントはサーバーへの接続を試行できるようになるだけです。 各クライアントは、必要なセキュリティ資格情報を提供する必要があることに変わりはありません。

  たとえば、JDBC クライアントの認証が失敗した場合、次のエラーが表示される場合があります。

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException:FATAL: password authentication failed for user "yourusername"

* **ファイアウォールが動的 IP アドレスを許可しない:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

  * Azure Database for PostgreSQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせます。 その後、ファイアウォール規則としてその IP アドレス範囲を追加します。

  * 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

* **ファイアウォール規則が IPv6 形式で使用できない**: ファイアウォール規則は IPv4 形式である必要があります。 IPv6 形式でファイアウォール規則を指定すると、検証エラーが返されます。


## <a name="next-steps"></a>次のステップ

* [Microsoft Azure portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](how-to-manage-firewall-portal.md)
* [Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](how-to-manage-firewall-cli.md)
