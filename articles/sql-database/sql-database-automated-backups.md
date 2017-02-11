---
title: "SQL Database のバックアップ - 自動、geo 冗長 | Microsoft Docs"
description: "SQL Database は数分ごとにローカル データベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: f6bb6e1c81cafe5f0e5c43c99ab15a0483742868


---
# <a name="learn-about-sql-database-backups"></a>SQL Database バックアップについての詳細情報
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    � Definition of the feature terminology.  i.e., What is a database backup?
    � Characteristics and capabilities of the feature. (How the feature works)
    � Common uses with links to overview topics that recommend when to use the feature.
    � Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    � Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    � How to steps for using the feature (Tasks)
    � How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example:    "Learn about elastic pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL Database はデータベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージ (RA-GRS) を利用して地理的冗長性を提供します。 バックアップは自動的に作成され、追加料金は発生しません。 ユーザー側の操作は必要ありません。 データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 独自のストレージ コンテナーにバックアップを保持したい場合は、長期的なバックアップの保持ポリシーを構成できます。 詳細については、[長期保存](sql-database-long-term-retention.md)に関する記事をご覧ください。

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは何か。
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL Database は SQL Server 技術を利用し、[完全](https://msdn.microsoft.com/library/ms186289.aspx)バックアップ、[差分](https://msdn.microsoft.com/library/ms175526.aspx)バックアップ、[トランザクション ログ](https://msdn.microsoft.com/library/ms191429.aspx) バックアップを作成します。 トランザクション ログ バックアップはパフォーマンス レベルとデータベース活動の量に基づいて、一般的には 5 - 10 分ごとに発生します。 完全バックアップと差分バックアップによるトランザクション ログ バックアップにより、データベースをホストする同じサーバーに、データベースを特定の時点に復元できます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

これらのバックアップを使用して、以下を行うことができます。

* リテンション期間内の特定の時点にデータベースを復元します。 この操作により、元のデータベースと同じサーバーに新しいデータベースが作成されます。
* 削除したデータベースを、削除された時点または保有期間内の任意の時点に復元します。 削除されたデータベースは、元のデータベースが作成されたサーバーと同じサーバーにのみ復元できます。
* 別の地理的リージョンにデータベースを復元します。 これにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。 
* Azure Recovery Services コンテナーに格納されている特定のバックアップからデータベースを復元します。 これにより、データベースの古いバージョンに復元でき、コンプライアンスの要求を満たし、またはアプリケーションの古いバージョンを実行できます。 [長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
* 復元を実行するには、[バックアップからのデータベースの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」を参照してください。
>

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の " *データベース レプリケーション* " は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>無償のバックアップ ストレージはどのくらい含まれますか。
SQL Database は、バックアップ ストレージとして、プロビジョニングされている最大のデータベース ストレージの 200% までを追加のコストなしで提供します。 たとえば、プロビジョニングされたデータベース サイズが 250 GB の Standard データベース インスタンスの場合、500 GB のバックアップ ストレージを追加のコストなしで利用できます。 データベースのサイズがプロビジョニングされたバックアップ ストレージを超える場合は、Azure サポートに連絡してリテンション期間を短縮できます。 もう 1 つのオプションとして、標準の読み取りアクセス geo 冗長ストレージ (RA-GRS) の料金で課金される追加のバックアップ ストレージに対して料金を支払うこともできます。 

## <a name="how-often-do-backups-happen"></a>バックアップはどのくらいの頻度で行われますか。
完全データベース バックアップは毎週作成され、差分データベース バックアップは通常、数時間に 1 回作成されます。また、トランザクション ログのバックアップは通常 5 - 10 分間隔で実行されます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら SQL Database サービスによって決定されます。 

バックアップ ストレージの geo レプリケーションは、Azure Storage のレプリケーション スケジュールに基づいて発生します。

## <a name="how-long-do-you-keep-my-backups"></a>バックアップはどのくらいの期間保存されますか。
各 SQL Database バックアップには、データベースの[サービス レベル](sql-database-service-tiers.md)に基づくリテンション期間が指定されます。 データベースのリテンション期間:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* Basic サービスレベルの場合、7 日間です。
* Standard サービス レベルの場合、35 日間です。
* Premium サービス レベルの場合、35 日間です。

Standard または Premium サービス レベルから Basic にデータベースをダウングレードした場合、バックアップは 7 日間保存されます。 7 日間を過ぎた場合、既存のバックアップはすべて利用できなくなります。 

Basic サービス レベルから Standard または Premium にデータベースをアップグレードした場合、SQL Database は 35 日間が経過するまで既存のバックアップを保持します。 新しいバックアップは 35 日間保持されます。

データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、リテンション期間が 7 日間の Basic データベースを削除するとします。 3 日経過したバックアップはあと 4 日間保存されます。

> [!IMPORTANT]
> SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>バックアップの保有期間を延長するにはどうすればよいですか。
アプリケーションで、バックアップがより長期間使用可能である必要がある場合、個別のデータベースの長期的バックアップ保持ポリシー (LTR ポリシー) を構成することによって、組み込みの保有期間を延長できます。 これにより、組み込みの保有期間を 35 日から 10 年に延長できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

Azure Portal または API を使用して LTR ポリシーをデータベースに追加すると、週単位のデータベースの完全バックアップが自動的にユーザー独自の Azure Backup サービス コンテナーにコピーされます。 データベースが TDE で暗号化されている場合、バックアップは保存中に自動的に暗号化されます。  期限切れのバックアップは、そのタイムスタンプおよび LTR ポリシーに基づいて、サービス コンテナーによって自動的に削除されます。  そのため、バックアップのスケジュールを管理したり、古いファイルのクリーンアップについて心配したりする必要はありません。 復元の API は、資格情報コンテナーが SQL Database と同じサブスクリプションにある限り、資格情報コンテナーに格納されているバックアップをサポートします。 Azure Portal または PowerShell を使用してこれらのバックアップにアクセスできます。

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」を参照してください。
>

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>次のステップ

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- 特定の時点に復元するには、[データベースの特定の時点への復元](sql-database-point-in-time-restore.md)に関する記事を参照してください。
- サービスによって生成されたデータベース バックアップから最も古い復元ポイントを確認するには、[最も古い復元ポイントの表示](sql-database-view-oldest-restore-point.md)に関する記事を参照してください。
- Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成するには、[長期的なバックアップ保有期間の構成](sql-database-configure-long-term-retention.md)に関する記事を参照してください。



<!--HONumber=Dec16_HO2-->


