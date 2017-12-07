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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 2a36df0e45af5bcce5338d04b7e1ba44221ae964
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS アプリケーション

同じ *Wingtip Tickets* アプリケーションが、3 つのサンプルの各々に実装されています。 このアプリは、シアター、クラブなど小規模の場所を対象とする SaaS アプリを一覧表示しチケット発行する、単純なイベントです。それぞれの場所はアプリのテナントで、会場の詳細、イベント一覧、顧客、チケット注文などの独自のデータを持ちます。このアプリは、管理スクリプトとチュートリアルと共に、1 つの完全な SaaS シナリオを示します。 これには、テナントのプロビジョニング、パフォーマンスの監視と管理、スキーマ管理、およびテナント間のレポートと分析が含まれます。

## <a name="three-saas-application-patterns"></a>3 つの SaaS アプリケーション パターン

このアプリには 3 つのバージョンがあります。それぞれが、Azure SQL Database 上で異なるデータベース テナント パターンを探求します。  1 つ目は、独立したシングル テナント データベースを使用するシングル テナント アプリケーションを使用します。 2 つ目は、テナントごとにデータベースを持つマルチテナント アプリを使用します。 3 つ目のサンプルは、シャードされたマルチテナント データベースを持つマルチ テナント アプリを使用します。

![3 つのテナント パターン][image-three-tenancy-patterns]

 各サンプルには、管理スクリプトおよび設計範囲を紹介するチュートリアルと、独自のアプリケーションで使用できる管理パターンが含まれています。  各サンプルは、5 分未満でデプロイします。  3 つすべてを並行してデプロイできるので、設計と管理の違いを比較できます。

## <a name="standalone-application-pattern"></a>スタンドアロン アプリケーション パターン

スタンドアロン アプリケーション パターンは、テナントごとに 1 つのテナント データベースを持つシングル テナント アプリケーションを使用します。 各テナントのアプリは、別々の Azure リソース グループにデプロイされます。 これは、サービス プロバイダーのサブスクリプションまたはテナントのサブスクリプション内で、テナントの代わりにプロバイダーによって管理されることがあります。 このパターンはテナントを最大限に分離させますが、複数のテナント間でリソースを共有する機会がないため、通常は最もコストが高くなります。

[チュートリアル][docs-tutorials-for-wingtip-sa]および GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa] 上のコードを確認してください。

## <a name="database-per-tenant-pattern"></a>テナントごとのデータベース パターン

テナントごとデータベースのパターンが効果的なのは、テナントの分離を考慮し、共有リソースの費用対効果の高い使用ができる一元的なサービスを実行したいサービス プロバイダーです。 場所またはテナントごとにデータベースが作成され、すべてのデータベースが一元的に管理されます。 データベースはエラスティック プールにホストされて、高い費用対効果と簡単なパフォーマンス管理を実現できますが、これはテナントの予期できないワークロード パターンを活用しています。 カタログ データベースには、テナントとそのデータベースの間のマッピングが格納されます。 このマッピングは、[エラスティック データベース クライアント ライブラリ](sql-database-elastic-database-client-library.md)のシャード マップ管理機能を使用して管理されており、アプリケーションへの効率的な接続管理を提供します。

[チュートリアル][docs-tutorials-for-wingtip-dpt]および GitHub  [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt] 上のコードを確認してください。

## <a name="sharded-multi-tenant-database-pattern"></a>シャード マルチテナント データベース パターン

マルチテナント データベースは、テナントあたりのコスト削減を求めていて、テナント分離は減らしてもよい、というサービス プロバイダーに効果的です。 このパターンでは、1 つのデータベースに多数のテナントを包含することができ、テナントごとのコストを削減します。 複数のデータベース間でテナントをシャーディングすることにより、ほぼ無限のスケールが可能です。  カタログ データベースはここでも、テナントをデータベースにマップします。  

このパターンではハイブリッド モデルも可能で、1 つのデータベース内の複数テナントでコストを最適化したり、独自のデータベース内の 1 つのテナントで分離を最適化したりすることもできます。 この選択は、テナントのプロビジョニング時あるいはその後のどちらでも、アプリケーションに影響を与えずに、テナントごとに可能です。

[チュートリアル][docs-tutorials-for-wingtip-mt]および GitHub  [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt] 上のコードを確認してください。

## <a name="next-steps"></a>次のステップ

#### <a name="conceptual-descriptions"></a>概念の説明

- アプリケーションのテナント パターンの詳細な説明については、「[マルチ テナント SaaS データベース テナント パターン][saas-tenancy-app-design-patterns-md]」をご覧ください。

#### <a name="tutorials-and-code"></a>チュートリアルとコード

- スタンドアロン アプリ
    - [スタンドアロン アプリのチュートリアル][docs-tutorials-for-wingtip-sa]。
    - [GitHub 上のスタンドアロン コード][github-code-for-wingtip-sa]。

- テナントごとのデータベース
    - [テナントごとのデータベースのチュートリアル][docs-tutorials-for-wingtip-dpt]。
    - [GitHub 上のテナントごとのデータベースのコード][github-code-for-wingtip-dpt]。

- シャード マルチテナント
    - [シャード マルチテナントのチュートリアル][docs-tutorials-for-wingtip-mt]。
    - [GitHub 上のシャード マルチテナントのコード][github-code-for-wingtip-mt]。



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "3 つのテナント パターン"。

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

