---
title: "Wingtips アプリへようこそ - Azure SQL Database | Microsoft Docs"
description: "クラウド環境での Azure SQL Database 向けのデータベース テナント モデル、およびサンプル Wingtips SaaS アプリケーションについて説明します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Wingtip Tickets サンプル SaaS Azure SQL Database のテナント アプリへようこそ

Wingtip Tickets サンプル SaaS Azure SQL Database のテナント アプリとそのチュートリアルへようこそ。 データベース テナントとは、御社のアプリケーションで有料でホストされている顧客に対して、アプリが提供するデータ分離モードのことをいいます。 今は分かりやすくするために、顧客はそれぞれ自分自身で全データベースを保有しているか、または 1 つのデータベースを他の顧客と共有しているかのどちらかとします。

## <a name="wingtip-tickets-app"></a>Wingtip Tickets アプリ

この Wingtip Tickets サンプル アプリケーションは、異なるデータベース テナント モデルによる、マルチテナント SaaS アプリケーションの設計と管理に関する効果を示します。 付随するチュートリアルでは、これらの同じ効果について直接説明します。 Wingtip Tickets は、Azure SQL Database でビルドされています。

Wingtip Tickets は、実際の SaaS の顧客によって使用されているさまざまな設計と管理のシナリオに対応するよう設計されています。 作成した使用パターンは、Wingtip Tickets で説明されています。

お持ちの Azure サブスクリプションで、この Wingtip Tickets アプリを 5 分でインストールできます。 インストールでは、複数テナント用のサンプル データの挿入も行います。 インストール間でやり取りしたり干渉したりすることはないため、すべてのモデルのアプリケーションと管理スクリプトを安全にインストールできます。

#### <a name="code-in-github"></a>Github のコード

アプリケーション コードと管理スクリプトは、すべて GitHub で入手できます。

- **スタンドアロン アプリ** モデル: [WingtipTicketsSaaS-StandaloneApp リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- **テナントごとのデータベース** モデル: [WingtipTicketsSaaS-DbPerTenant リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- **シャード マルチテナント** モデル: [WingtipTicketsSaaS-MultiTenantDB リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

Wingtip Tickets アプリの 1 つの同じコード ベースが上記のすべてのモデルで再利用されます。 Github から入手したコードを使用して、独自の SaaS プロジェクトを開始できます。



## <a name="major-database-tenancy-models"></a>主なデータベース テナント モデル

Wingtip Tickets は、イベントの一覧表示と発券の SaaS アプリケーションです。 Wingtip は、会場で必要なサービスを提供します。 次のすべての項目が各会場に適用されます。

- 自社のアプリケーションでホストされている分の支払いを受ける。
- Wingtip 内の*テナント*である。
- イベントをホストする。 次のイベントが含まれます。
    - チケットの価格。
    - チケットの販売。
    - チケットを購入する顧客。

このアプリでは、管理スクリプトとチュートリアルと共に、1 つの完全な SaaS シナリオを示します。 このシナリオには、次のアクティビティが含まれています。

- テナントのプロビジョニング。
- パフォーマンスの監視と管理。
- スキーマ管理。
- テナント間のレポートと分析。

これらすべてのアクティビティが、必要なスケールで提供されます。



## <a name="code-samples-for-each-tenancy-model"></a>各テナント モデルのコード サンプル

1 つのアプリケーション モデル セットが強調表示されます。 ただし、実装によっては、2 つ以上のモデルの要素が混在する場合もあります。

#### <a name="standalone-app-model"></a>スタンドアロン アプリ モデル

![スタンドアロン アプリ モデル][standalone-app-model-62s]

このモデルでは、シングルテナント アプリケーションを使用します。 そのため、このモデルで必要なデータベースは 1 つのみで、そこに 1 つのテナントのみのデータが保管されます。 テナントは、データベース内で他のテナントから完全に分離されます。

御社のアプリのインスタンスを多数の異なる顧客に販売し、顧客がそれぞれ独自に稼働させる場合にこのモデルを使用します。 その場合、その顧客は唯一のテナントとなります。 データベースに 1 社の顧客のみのデータを保管しながら、その顧客の多数の顧客データを保管することになります。

#### <a name="database-per-tenant"></a>テナントごとのデータベース

![テナントごとのデータベース モデル][database-per-tenant-model-35d]

このモデルでは、アプリケーションのインスタンスに複数のテナントが含まれます。 ただし、新しいテナントそれぞれには、その新しいテナントだけが使用する別のデータベースが割り当てられます。

このモデルでは、各テナントにデータベースの完全分離が提供されます。 Azure SQL Database サービスは、このモデルの信頼性を高める高度な技術を備えています。

- [SQL Database のマルチテナント SaaS アプリの例の概要][saas-dbpertenant-wingtip-app-overview-15d]に関する記事で、このモデルに関する詳細情報をご覧いただけます。

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>シャード マルチテナント データベース、ハイブリッド

![シャード マルチテナント データベース モデル、ハイブリッド][sharded-multitenantdb-model-hybrid-79m]

このモデルでは、アプリケーションのインスタンスに複数のテナントが含まれます。 また、そのデータベースの一部またはすべてにも複数のテナントが含まれます。 このモデルは異なるサービス層の提供に適しており、顧客はデータベースの完全分離が重要な場合に追加料金を払うことがあります。

すべてのデータベースのスキーマには、テナント識別子が含まれています。 テナント識別子は、1 つのテナントのみを格納するこれらのデータベースで同一となります。

- [SQL Database のマルチテナント SaaS アプリの例の概要][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>各テナント モデルのチュートリアル

各テナント モデルは、以下で説明されています。

- 一連のチュートリアル記事。
- モデル専用の Github リポジトリに格納されている次のソース コード。
    - Wingtip Tickets アプリケーションのコード。
    - 管理シナリオのスクリプト コード。

#### <a name="tutorials-for-management-scenarios"></a>管理シナリオのチュートリアル

各モデルのチュートリアル記事では、次の管理シナリオを説明します。

- テナントのプロビジョニング。
- パフォーマンスの監視と管理。
- スキーマ管理。
- テナント間のレポートと分析。
- 1 つのテナントの以前の時点への復元。
- ディザスター リカバリー。



## <a name="next-steps"></a>次のステップ

- [SQL Database のマルチテナント SaaS アプリの例の概要][saas-dbpertenant-wingtip-app-overview-15d]に関する記事で、このモデルに関する詳細情報をご覧いただけます。

- [マルチテナント SaaS データベース テナント パターン][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "スタンドアロン アプリ モデル"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "テナントごとのデータベース モデル"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "シャード マルチテナント データベース モデル、ハイブリッド"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


