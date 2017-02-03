---
title: "Azure Portal を使用して Azure SQL Database の geo レプリケーションを構成する | Microsoft Docs"
description: "Azure Portal を使用して Azure SQL Database の geo レプリケーションを構成する"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 9faff06ee1856ca6490add759acd3c5f36719853


---
# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Azure Portal を使用して Azure SQL Database の geo レプリケーションを構成する
> [!div class="op_single_selector"]
> * [概要](sql-database-geo-replication-overview.md)
> * [Azure ポータル](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

この記事では、[Azure Portal](http://portal.azure.com) を使用して SQL Database のアクティブ geo レプリケーションを構成する方法について説明します。

Azure Portal を使用してフェールオーバーを開始するには、「 [Azure Portal を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md)」を参照してください。

> [!NOTE]
> すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。 2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。
> 
> 

Azure Portal を使用して geo レプリケーションを構成するには、次のリソースが必要です。

* Azure SQL Database データベース: 別の地理的リージョンにレプリケートするプライマリ データベースです。

> [!Note]
アクティブ geo レプリケーションは、同じサブスクリプション内のデータベース間である必要があります。

## <a name="add-a-secondary-database"></a>セカンダリ データベースの追加
次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。  

セカンダリ データベースを追加するには、サブスクリプションの所有者または共同所有者でなければなりません。

セカンダリ データベースは、プライマリ データベースと同じ名前となります。また、既定でのサービス レベルはプライマリ データベースと同じになります。 セカンダリ データベースは、スタンドアロン データベースまたはエラスティック プールのデータベースにできます。 詳細については、[サービス レベル](sql-database-service-tiers.md)に関する記事をご覧ください。
セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。

> [!NOTE]
> パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。
> 
> 

### <a name="add-secondary"></a>セカンダリ データベースの追加
1. [Azure Portal](http://portal.azure.com) で、geo レプリケーションについてセットアップするデータベースを参照します。
2. SQL データベース ページで、**[geo レプリケーション]** を選択し、セカンダリ データベースを作成するリージョンを選択します。 プライマリ データベースをホストしているリージョンでなければどのリージョンを選択してもかまいませんが、[ペア リージョン](../best-practices-availability-paired-regions.md)を選択することをお勧めします。
   
    ![geo レプリケーションの構成](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. サーバーと、セカンダリ データベースの価格レベルを選択または構成します。
   
    ![セカンダリ データベースの構成](./media/sql-database-geo-replication-portal/create-secondary.png)
4. 必要に応じて、以下のようにセカンダリ データベースをエラスティック プールに追加できます。 プールにセカンダリ データベースを作成するには、**[エラスティック プール]** をクリックし、ターゲット サーバー上でプールを選択します。 プールは、ターゲット サーバーに既に存在する必要があります。 このワークフローでは、プールを作成できません。
5. **[作成]** をクリックして、セカンダリ データベースを追加します。
6. セカンダリ データベースが作成され、シード処理が始まります。
   
    ![セカンダリ データベースの構成](./media/sql-database-geo-replication-portal/seeding0.png)
7. シード処理が完了すると、セカンダリ データベースの状態が表示されます。
   
    ![シード処理の完了](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する
この操作では、セカンダリ データベースへのレプリケーションを完全に終了し、セカンダリ データベースのロールを通常の読み取り/書き込みデータベースに変更します。 セカンダリ データベースへの接続が切断された場合、コマンドは成功しますが、接続が復元するまでセカンダリ データベースは読み取り/書き込み状態になりません。  

1. [Azure Portal](http://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. SQL Database ページで、**[geo レプリケーション]** を選択します。
3. **[セカンダリ]** ボックスの一覧で、geo レプリケーション パートナーシップから削除するデータベースを選択します。
4. **[レプリケーションを停止する]**をクリックします。
   
    ![セカンダリ データベースの削除](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. 確認ウィンドウが開きます。 **[はい]** をクリックして geo レプリケーション パートナーシップからデータベースを削除します。 (データベースを読み取り/書き込みデータベースに設定することは、レプリケーションの一部ではありません)。

## <a name="next-steps"></a>次のステップ
* アクティブ geo レプリケーションの詳細については、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事をご覧ください。
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。




<!--HONumber=Dec16_HO2-->


