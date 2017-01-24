---
title: "SQL Database V12 へのアップグレードを計画する | Microsoft Docs"
description: "Azure SQL Database V12 へのアップグレードに関連する準備作業と制限について説明します。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 8020f904-ad27-40c5-8c59-bdf2e690f77d
ms.service: sql-database
ms.custom: V11
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: genemi
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 237aa26a40c34d8fb511881604cb26bf9380322e


---
# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>SQL Database V12 へのアップグレードの計画と準備をする
このトピックでは、Azure SQL Database をバージョン V11 から V12 にアップグレードするために必要な、計画と準備について説明します。

V12 へのアップグレードをサポートする新しい [Azure ポータル](https://portal.azure.com/) が使用できます。

次の表は、V12 に関する他のヘルプ トピックの一覧です。

| タイトルとリンク | コンテンツの説明 |
|:--- |:--- |
| [Azure SQL Database の機能](sql-database-features.md) |Azure SQL Database と SQL Server を比較した機能のマトリックスを提供します。 |
| [SQL Database V12 でデータベースを作成する](sql-database-get-started.md) |V12 で新しい Azure SQL Database を作成する方法について説明します。 空のデータベースだけでなく、さまざまなオプションも説明します。 |

## <a name="plan-ahead"></a>事前の計画
次のサブセクションでは、Azure SQL Database を V12 にアップグレードする操作を行う前に理解しておく内容と、決定しておく必要がある項目について説明します。

### <a name="version-clarification"></a>バージョンの明確化
このドキュメントで取り扱うのは、Microsoft Azure SQL Database のバージョン V11 から V12 へのアップグレードです。 正式には、バージョン番号は次の 2 つのような値になります。これらは Transact-SQL ステートメント **SELECT @@version;** によって取得できます。

* 12.0.2000.8 *(または少し高い、V12)*
* 11.0.9228.18 *(V11)*
  * V11 は SAWA v2 とも呼ばれました。

### <a name="service-tier-planning"></a>サービス階層の計画
V12 から使用する場合、Azure SQL Database は Basic、Standard、Premium のサービス階層のみをサポートします。 V12 では、Web と Business サービス階層はサポートされません。 そのため、現在 Web または Business サービス階層の Azure SQL Database をアップグレードする場合、新しいサービス階層を設定する必要があります。

Basic、Standard、Premium サービス階層の詳細については、次を参照してください。

* [SQL Database のサービス階層](sql-database-service-tiers.md)
* [SQL Database の Web/Business データベースを新しいサービス階層にアップグレードする](sql-database-upgrade-server-portal.md)

### <a name="review-the-geo-replication-configuration"></a>geo レプリケーションの構成を確認する
Azure SQL Database を Geo レプリケーション用に構成している場合、現在の構成を文書化しておき、アップグレードの準備操作を開始する前に、Geo レプリケーションを停止する必要があります。 アップグレードが完了したら、Geo レプリケーション用にデータベースを再構成する必要があります。

ソースには手を加えずに、データベースのコピーでテストを行うという方針になります。

## <a name="preparation-actions"></a>準備操作
計画が完了したら、次のサブセクションで説明されているアクション手順を実行して、最終的なアップグレード フェーズの準備を行うことができます。

最終的なアップグレード フェーズの詳細な説明は、このヘルプ トピックの上部からリンクされているヘルプ トピックに記載してあります。

### <a name="service-tier-actions"></a>サービス階層のアクション
V12 では、Web と Business サービスの価格レベルはサポートされません。

V11 Azure SQL Database が Web または Business データベースのいずれかである場合は、アップグレード処理の際にサポートされる階層へのデータベースの切り替えが通知されます。 お使いのデータベースのワークロードの履歴に合った階層にアップグレードすることをお勧めします。 ただし、必要に応じて、サポートされる任意の階層を選択できます。

アップグレード前に V11 データベースを Web や Business 階層から切り替えておけば、アップグレード中に必要となる手順を減らすことができます。 この操作は新しい [Azure ポータル](https://portal.azure.com/)から実行できます。

切り替え先のサービス階層がわからない場合は、最初に Standard 階層の S2 レベルを選択しておくと便利です。 下位の階層のリソースは、Web 階層や Business 階層よりも少なくなっています。

### <a name="suspend-geo-replication-during-upgrade"></a>アップグレード時に geo レプリケーションを中断する
データベースで geo レプリケーションが有効になっていると、V12 へのアップグレードを実行できません。 最初にデータベースを再構成して、geo レプリケーションの使用を停止する必要があります。

アップグレードが完了したら、データベースを構成して、geo レプリケーションを再度使用できます。

### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>クライアント接続用に Azure VM でポートを開く
クライアントが Azure 仮想マシン (VM) で実行されるとき、クライアント プログラムが SQL Database V12 に接続する場合、VM で次のポート範囲を開く必要があります。

* 11000 ～ 11999
* 14000 ～ 14999

SQL Database V12 のポートの詳細については、 [こちら](sql-database-develop-direct-route-ports-adonet-v12.md) を参照してください。 これらのポートは、SQL Database V12 のパフォーマンスの強化に必要です。

## <a name="a-idlimitationsalimitations-during-and-after-upgrade-to-v12"></a><a id="limitations"></a>V12 へのアップグレード中とアップグレード後の制限事項
### <a name="portals-for-v12"></a>V12 用ポータル
Azure には 3 つのポータルがありますが、それぞれ SQL Database V12 に関する機能が異なります

* [http://portal.azure.com/](https://portal.azure.com/)<br/>この Azure ポータルは新しいもので、現段階ではプレビュー状態のままです。 このポータルは、現段階では完全に一般公開 (GA) されていません。 このポータルでは、次のことを実行できます。
  
  * V12 サーバーとデータベースを管理できます。
  * V11 データベースを V12 にアップグレードできます。
* [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>この Azure クラシック ポータルは最終的に廃止される予定です。 このポータルでは、次のことを実行できます。
  
  * V12 サーバーとデータベースを管理できます。
  * V11 データベースを V12 にアップグレード *できません* 。
* (http://*yourservername*.database.windows.net)<br/> Azure SQL Database クラシック ポータル:
  
  * V12 サーバーを管理 "できません"** 。

Visual Studio 2015 (VS2015) で Azure SQL Database に接続することをお勧めします。 VS2015 は、次のようなタスクに使用できます。

* Transact-SQL ステートメントの実行。
* スキーマの設計。
* オンラインまたはオフラインでのデータベースの開発。

代わりに、VS2015 のすべての機能を備えたバージョンである [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/) を無償でダウンロードすることもできます。

以前の Azure クラシック ポータルでは、データベース ページで **[Visual Studio で開く]** をクリックしてコンピューター上で VS2015 を起動し、Azure SQL Database に接続できます。

代わりに、 [CU6](http://support.microsoft.com/kb/3031047/) を含む SQL Server Management Studio (SSMS) 2014 を使用して Azure SQL Database に接続することもできます。 詳細については、<br/>[Azure SQL Database のクライアント ツール更新プログラム](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)から実行できます。

> [!IMPORTANT]
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="limitation-during-upgrade-to-v12"></a>V12 へのアップグレード *中* の制限事項
V11 データベースは、V12 へのアップグレード中も、引き続きデータ アクセスに使用できます。 ただし、考慮すべきいくつかの制限があります。

| V12 へのアップグレード | Description |
|:--- |:--- |
| アップグレードの実行時間 |アップグレードの実行時間は、サーバーにあるデータベースのサイズ、エディション、数によって異なります。 特に、次のようなデータベースを持つサーバーの場合は、アップグレードの処理に数時間から数日かかることがあります。<br/><br/>* 50 GB より大きい<br/>* Premium 以外のサービス レベル<br/><br/>アップグレード中にサーバーで新しいデータベースを作成した場合も、アップグレードの実行時間が長くなることがあります。 |
| geo レプリケーションなし |V11 からのアップグレードを実行している最中の V12 サーバーでは geo レプリケーションはサポートされません。 |
| データベースは V12 へのアップグレードの最終フェーズ中に短時間利用できなくなる |V11 サーバーに属しているデータベースは、アップグレード処理中も利用可能です。 ただし、サーバーとデータベースへの接続は、最終フェーズ中に V11 から準備が完了した V12 への切り替えが始まると、短時間利用できなくなります。<br/><br/>この切り替え時間は 40 秒から 5 分に及ぶことがあります。 ほとんどのサーバーでは、切り替えは 90 秒以内に完了すると予期されています。 データベースの数が多い、またはデータベースの書き込みワークロードが重いサーバーでは、書き込み時間は長くなります。 |

### <a name="limitation-after-upgrade-to-v12"></a>V12 へのアップグレード *後* の制限事項
| V12 へのアップグレード | Description |
|:--- |:--- |
| V11 に戻すことはできない |置き換えによるアップグレードの後は、元に戻したり取り消したりすることはできません。 |
| Web または Business 階層 |アップグレードを開始すると、新しい V12 データベースでは Web や Business サービス階層を認識したり、受け付けたりできなくなります。 |

### <a name="export-and-import-after-upgrade-to-v12"></a>V12 へのアップグレード *後* のエクスポートとインポート
[Azure ポータル](https://portal.azure.com/)を使用して V12 データベースをエクスポート、インポートできます。 または、次のいずれかのツールを使用してエクスポートまたはインポートすることもできます。

* SQL Server Management Studio (SSMS)
* Visual Studio 2015
* Data-Tier Application Framework (DacFx)

ただし、これらのツールを使用するには、まずそれぞれの最新の更新プログラムをインストールし、新しい V12 の機能が確実にサポートされるようにする必要があります。

* [SQL Server Management Studio 2014 用の累積的な更新プログラム 6](http://support2.microsoft.com/kb/3031047)
* [Visual Studio 2013 の SQL Server データベース ツール用の 2015年 2 月の更新プログラム](https://msdn.microsoft.com/data/hh297027)
* [Azure SQL Database V12 用の 2015 年 2月の Data-Tier Application Framework (DacFx)](http://www.microsoft.com/download/details.aspx?id=45886)

> [!NOTE]
> 上記のツールへのリンクが、2015 年 3 月 2 日以降に更新されました。 これらのツールの新しい更新プログラムを使用することをお勧めします。
> 
> 

### <a name="restore-to-v12-of-a-deleted-v11-database"></a>削除した V11 データベースの V12 に復元する
次のシナリオでは、削除した V11 Azure SQL Database を V12 Azure SQL Database サーバーに復元します。

1. V11 Azure SQL Database サーバーを利用していることを前提としています。 <br/> そのサーバーでは、廃止された Web サービス階層と Business サービス階層を利用しています。
2. データベースを削除します。
3. サーバーを V12 にアップグレードします。
4. 次に、データベースをサーバーに復元します。 <br/> それによってデータベースは V12 にアップグレードされ、Standard サービス階層の S0 レベルに設定されます。
5. S0 が適していない場合は、任意のサービス階層にデータベースを切り替えることができます。

### <a name="powershell-cmdlets"></a>PowerShell コマンドレット
PowerShell コマンドレットを使用して、V11 を含め、V12 より前のバージョンから V12 への Azure SQL Database のアップグレードを開始、停止、監視できます。

* [PowerShell を使用し、SQL Database V12 にアップグレードします](sql-database-upgrade-server-powershell.md)

これらの PowerShell コマンドレットについては、次のリファレンス ドキュメントを参照してください。

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Stop- コマンドレットは、一時停止ではなく取り消しを意味します。 アップグレードを再開する方法は、最初からやり直す方法以外にありません。 Stop- コマンドレットにより、該当するすべてのリソースがクリーンアップされ、解放されます。

## <a name="failure-resolution"></a>エラーの解決
何らかの理由で、アップグレードでエラーが発生した場合は、V11 データベースがそのまま有効なり、通常と同じように利用できます。

## <a name="related-links"></a>関連リンク
* Microsoft Azure の [プレビュー機能](https://azure.microsoft.com/services/preview/)

<!--Anchors-->
[Subheading 1]: #subheading-1



<!--HONumber=Dec16_HO1-->


