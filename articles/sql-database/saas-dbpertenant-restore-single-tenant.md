---
title: マルチテナント SaaS アプリで Azure SQL Database を復元する | Microsoft Docs
description: データを誤って削除した場合にシングル テナントの SQL データベースを復元する方法について説明します
keywords: SQL データベース チュートリアル
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/10/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 7ae8bcb6172d9f9d56c531e149635434057fc2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>テナント SaaS アプリケーションごとの 1 つのデータベースで、単一のテナントを復元します。

テナントごとデータベース モデルは、他のテナントに影響を与えずに単一のテナントを以前の特定の時点まで簡単に復元することを可能にします。

このチュートリアルでは、2 つのデータ回復パターンについて説明します。

> [!div class="checklist"]

> * データベースを並列データベースに復元する
> * 所定の場所にデータベースを復元し、既存のデータベースと置換する


|||
|:--|:--|
| **並列データベースに復元する** | このパターンは、コンプライアンスの監査などのレビュー作業に使用され、テナントで以前の時点からのデータを調査できるようにします。  テナントの現在のデータベースはオンラインのままで、変化しません。 |
| **所定の場所に復元する** | このパターンは通常、テナントのデータを誤って削除または破損した後で、以前の特定の時点にテナントを回復するために使用されます。 元のデータベースはオフラインになり、復元されたデータベースに置き換えられます。 |
|||

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](saas-dbpertenant-get-started-deploy.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>SaaS テナントの復元パターンの概要

個々のテナントのデータを復元するためのシンプルなパターンが 2 つあります。 テナント データベースは相互に切り離されているため、一方のテナントを復元する際に、もう一方のテナントのデータに影響を及ぼすことはありません。  SQL Database のポイント イン タイム リストア (PITR) 機能は、両方のパターンで使用されます。  PITR は常に、新しいデータベースを作成します。   

最初のパターンである**並列での復元**では、テナントの現在のデータベースの隣に、新しい並列データベースが作成されます。 その後、テナントには、復元されたデータベースに対する読み取り専用アクセスが付与されます。 復元されたデータはレビューされ、現在のデータ値を上書きするために潜在的に使用されます。 テナントによる復元されたデータベースへのアクセス方法や提供される復元用オプションを決定するのは、アプリ デザイナーの担当です。 一部のシナリオでは、テナントで以前の時点のデータを簡単に確認可能であることが、唯一の要件になる場合もあります。 

2 つ目のパターンである**特定の場所への復元**は、データを損失または破損し、テナントで以前の時点まで戻したい場合に役立ちます。  データベースの復元中は、テナントがオフラインになります。 元のデータベースが削除され、復元されたデータベースの名前が変更されます。 元のデータベースのバックアップ チェーンは、削除後もアクセス可能なままになり、必要に応じて、以前の時点までデータベースを復元することができます。

データベースで [geo レプリケーション](sql-database-geo-replication-overview.md)が使用され並行して復元される場合は、復元されたコピーから元のデータベースに必要なデータをコピーすることをお勧めします。 元のデータベースを復元したデータベースに置き換える場合は、geo レプリケーションを再構成して再同期する必要があります。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant アプリケーション スクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリで入手できます。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="before-you-start"></a>開始する前に

データベースが作成されると、最初の完全バックアップから復元可能になるまでに、10 ～ 15 分かかる場合があります。  アプリケーションをインストールしたばかりの場合は、このシナリオを進める前に数分間待機する必要が生じる可能性があります。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>データを誤って削除した状態のテナントをシミュレートする

前述の回復シナリオを示すには、最初にいずれかのテナント データベースでイベントを "*誤って*" 削除します。 

### <a name="open-the-events-app-to-review-the-current-events"></a>Events アプリを開いて現在のイベントを確認する

1. *Events Hub* (http://events.wtp.&lt;user&gt;.trafficmanager.net) を開き、**[Contoso Concert Hall]** をクリックします。

   ![Events Hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. イベントの一覧をスクロールして、一覧の最後のイベントをメモします。

   ![最後のイベント](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>"誤って" 最後のイベントを削除する

1. *PowerShell ISE* で ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = **1** ((チケットの販売がない) 最後のイベントを削除します)。
1. **F5** キーを押してスクリプトを実行し、最後のイベントを削除します。 以下の確認メッセージが表示されます。

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso のイベント ページが開きます。 下へスクロールし、イベントが表示されていないことを確認します。 イベントが一覧に残っている場合は、[最新の情報に更新] をクリックして、イベントが表示されていないことを確認します。

   ![最後のイベント](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>運用データベースと並行してテナント データベースを復元する

ここでは、Contoso Concert Hall データベースをイベントが削除される前の時点に復元します。 このシナリオでは、並列データベースで削除されたデータを確認することのみが目的であると仮定しています。

 *Restore-TenantInParallel.ps1* スクリプトは、並列カタログ エントリを持つ *ContosoConcertHall\_old* という名前の並列テナント データベースを作成します。 この復元パターンは、小規模なデータ損失からの回復や、コンプライアンスや監査の目的に応じてデータを確認する必要がある場合に最適です。 また、[geo レプリケーション](sql-database-geo-replication-overview.md)の使用時にも推奨されるアプローチです。

1. 「[データを誤って削除した状態のテナントをシミュレートする](#simulate-a-tenant-accidentally-deleting-data)」の手順を完了します。
1. *PowerShell ISE* で、...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_ を開きます。
1. **$DemoScenario** = **2** と設定します *(並列でテナントを復元します)*。
1. **F5** キーを押して、スクリプトを実行します。

このスクリプトは、イベントが削除される前の特定の時点にテナント データベースを復元します。 データベースは、_ContosoConcertHall\_old_ という名前の新しいデータベースに復元されます。 この復元されたデータベースに存在するカタログ メタデータが削除されて、データベースは、*ContosoConcertHall\_old* という名前から構築されたキーを使ってカタログに追加されます。

デモ スクリプトは、ブラウザーでこの新しいテナント データベースのイベント ページを開きます。 URL に関するメモ: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```。このページは、復元されたデータベースからのデータを示しており、*_old* が名前に追加されています。

ブラウザーに表示されるイベントをスクロールして、前述のセクションで削除したイベントが復元されていることを確認します。

なお、独自のイベント アプリを使用して、復元されたテナントを追加のテナントとして公開すると、復元されたデータに対するテナント アクセスが提供される可能性は低いですが、復元パターンを示すことができます。 実際には、古いデータへの読み取りアクセス許可を付与し、復元されたこのデータベースを一定の期間のみ保持することになるはずです。 サンプルでは、_復元されたテナントの削除_ シナリオの実行を完了することで、復元されたテナント エントリを削除できます。

1. **$DemoScenario** = **4** を設定します *(復元されたテナントを削除します)*。
1. **F5** **を使用して****実行します**。
1. これで、*ContosoConcertHall\_old* エントリがカタログから削除されました。 ブラウザーで先に進み、このテナントのイベント ページを閉じます。


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>テナントを所定の時点に復元し、既存のテナント データベースを置き換える

ここでは、Contoso Concert Hall テナントをイベントが削除される前の時点に復元します。 *Restore-TenantInPlace* スクリプトは、テナント データベースを新しいデータベースに復元して、元のデータベースを削除します。  この復元パターンは、重大なデータ破損からの回復に最適です (テナントで対応が必要となる重大なデータ損失が発生する可能性があるため)。

1. PowerShell ISE で **Demo-RestoreTenant.ps1** ファイルを開きます。
1. **$DemoScenario** = **5** を設定します *(テナントを所定の場所に復元します)*。
1. **F5** を使用して実行します。

このスクリプトは、イベントが削除される前の時点にテナント データベースを復元します。 それ以上更新されないように、最初に Contoso Concert Hall テナントをオフラインにします。 その後、復元ポイントから復元することによって、並列データベースが作成されます。  データベース名が既存のテナント データベース名と競合しないように、タイムスタンプを使用して、復元されたデータベースの名前が指定されます。 続いて、古いテナント データベースが削除され、復元されたデータベースの名前が元のデータベース名に変更されます。 最後に、Contoso Concert Hall がオンラインに戻り、復元されたデータベースへのアプリからのアクセスが許可されます。

これで、イベントが削除される前の時点にデータベースが正常に復元されました。 イベント ページを開き、最後のイベントが復元されていることを確認してください。

データベースを復元したら、再度復元するために最初の完全バックアップが利用可能になるまでに、10 ～ 15 分かかることに注意してください。 

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * データベースを並列データベースに復元する
> * データベースを所定の時点に復元する

[テナント データベースのスキーマの管理に関するページ](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database バックアップについての詳細情報](sql-database-automated-backups.md)
