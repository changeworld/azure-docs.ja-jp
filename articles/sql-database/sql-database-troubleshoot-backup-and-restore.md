<properties
	pageTitle="Azure SQL Database によるバックアップのトラブルシューティングと復元"
	description="Azure SQL Database のバックアップおよびレプリカを使用した、クラウド データベースのエラーおよび障害からの回復方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="daleche"/>

# 過去のある時点へのデータベースの復元、削除したデータベースの復元、またはデータ センターの障害からの回復

SQL データベースは、障害やユーザー エラーから回復できるように、データベースのレプリカを保持します。使用可能なオプションは、データベースのサービス層と選択したオプションによって異なります。詳細および設計に関する考慮事項については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。

##過去のある時点にデータベースを復元するには
1.	[Azure ポータル](https://azure.microsoft.com/)で **[SQL データベース]** をクリックします。
2.	一覧からデータベースを選択して、**[復元]** をクリックします。
3.	データベースの新しい名前を入力し、復元する日付および時刻を選択してから、**[作成]** をクリックします。
4.	新しいデータベースを参照する場合、必要に応じてアプリの調整を行います。「[ユーザー エラーからのデータベースの回復](sql-database-user-error-recovery.md)」を参照してください。

##誤って削除したデータベースを復元するには
1.	[Azure ポータル](https://azure.microsoft.com/)で **[SQL Server]** をクリックします。
2.	一覧からデータベースがホストされているサーバーを選択します。
3.	[サーバー] ブレードで下にスクロールし、**[削除済みデータベース]** をクリックします。
4.	復元するデータベースを選択して、**[作成]** をクリックします。
5.	新しいデータベースを参照する場合、必要に応じてアプリの調整を行います。「[ユーザー エラーからのデータベースの回復](sql-database-user-error-recovery.md)」を参照してください。

##地域のデータ センターの障害から回復するには
Standard および Premium のデータベースで geo レプリケートされたセカンダリを設定している場合は、そのセカンダリを使用して回復することができます。これにより、データが失われる可能性の少ないデータベースの復元が可能になります。詳細については、「[Azure SQL データベースの障害からの回復](sql-database-disaster-recovery.md)」を参照してください。

Azure では、別のリージョンでのすべてのデータベースのバックアップ (地理冗長バックアップ) も提供しています。geo リストアと呼ばれるこれらのバックアップから新しいデータベースを作成できますが、この方法のみに頼ると、データが失われる可能性があります。

**geo リストアを使用してデータベースを回復するには：**

- [Azure ポータル](https://azure.microsoft.com/)で**[新規]** をクリックし、**[データ + ストレージ]** をクリックしてから、**[SQL Database]** をクリックし、データベースのソースとして **[Backup]** を選択します。詳細については、「[Azure SQL データベースの障害からの回復](sql-database-disaster-recovery.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->