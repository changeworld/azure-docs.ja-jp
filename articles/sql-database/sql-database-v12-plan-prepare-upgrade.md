<properties
	pageTitle="SQL Database V12 へのアップグレードを計画する | Microsoft Azure"
	description="Azure SQL Database V12 へのアップグレードに関連する準備作業と制限について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2015"
	ms.author="genemi"/>


# SQL Database V12 へのアップグレードの計画と準備をする


このトピックでは、Azure SQL Database をバージョン V11 から V12 にアップグレードするために必要な、計画と準備について説明します。


新しい [Azure プレビュー ポータル](http://portal.azure.com/)を使用して、V12 へのアップグレードをサポートできます。


次の表は、V12 に関する他のヘルプ トピックの一覧です。


| タイトルとリンク | コンテンツの説明 |
| :--- | :--- |
| [SQL Database V12 の新機能](sql-database-v12-whats-new.md) | V12 によって、Azure SQL Database が機能的にどれくらい Microsoft SQL Server に近づいたかについて説明します。 |
| [チュートリアル: 最新の SQL Database 更新プログラム V12 にサインアップする](sql-database-v12-sign-up.md) | Azure SQL Database を V12 にアップグレードする場合に実行する必要がある手順について説明します。 |
| [SQL Database の更新プログラム V12 でデータベースを作成する](sql-database-create.md) | V12 で新しい Azure SQL Database を作成する方法について説明します。空のデータベースだけでなく、さまざまなオプションも説明します。 |


## 事前の計画


次のサブセクションでは、Azure SQL Database を V12 にアップグレードする操作を行う前に理解しておく内容と、決定しておく必要がある項目について説明します。

### バージョンの明確化


このドキュメントで取り扱うのは、Microsoft Azure SQL Database のバージョン V11 から V12 へのアップグレードです。正式には、バージョン番号は次の 2 つのような値になります。これらは Transact-SQL ステートメント **SELECT @@version;** によって取得できます。


- 12\.0.2000.8 *(または少し高い、V12)*
- 11\.0.9228.18 *(V11)*
 - V11 は SAWA v2 とも呼ばれました。

### サービス階層の計画


V12 から使用する場合、Azure SQL Database は Basic、Standard、Premium のサービス階層のみをサポートします。V12 では、Web と Business サービス階層はサポートされません。そのため、現在 Web または Business サービス階層の Azure SQL Database をアップグレードする場合、新しいサービス階層を設定する必要があります。


Basic、Standard、Premium サービス階層の詳細については、次を参照してください。


- [SQL Database の Web/Business データベースを新しいサービス階層にアップグレードする](sql-database-upgrade-new-service-tiers.md)
- [Azure SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)


### geo レプリケーションの構成を確認する


Azure SQL Database を Geo レプリケーション用に構成している場合、現在の構成を文書化しておき、アップグレードの準備捜査を開始する前に、Geo レプリケーションを停止する必要があります。アップグレードが完了したら、Geo レプリケーション用にデータベースを再構成する必要があります。


ソースには手を加えずに、データベースのコピーでテストを行うという方針になります。


## 準備操作


計画が完了したら、次のサブセクションで説明されているアクション手順を実行して、最終的なアップグレード フェーズの準備を行うことができます。


最終的なアップグレード フェーズの詳細な説明は、このヘルプ トピックの上部からリンクされているヘルプ トピックに記載してあります。


### サービス階層のアクション


V12 では、Web と Business サービスの価格レベルはサポートされません。


V11 Azure SQL Database が Web または Business データベースのいずれかである場合は、アップグレード処理の際にサポートされる階層へのデータベースの切り替えが通知されます。お使いのデータベースのワークロードの履歴に合った階層にアップグレードすることをお勧めします。ただし、必要に応じて、サポートされる任意の階層を選択できます。


アップグレード前に V11 データベースを Web や Business 階層から切り替えておけば、アップグレード中に必要となる手順を減らすことができます。この操作は新しい [Azure プレビュー ポータル](http://portal.azure.com/)から実行できます。


切り替え先のサービス階層がわからない場合は、最初に Standard 階層の S2 レベルを選択しておくと便利です。下位の階層のリソースは、Web 階層や Business 階層よりも少なくなっています。


### アップグレード時に geo レプリケーションを中断する


データベースで Geo レプリケーションが有効になっていると、V12 へのアップグレードを実行できません。最初にデータベースを再構成して、Geo レプリケーションの使用を停止する必要があります。


アップグレードが完了したら、データベースを構成して、Geo レプリケーションを再度使用できます。


### Azure VM 上のクライアント


クライアントが Azure 仮想マシン (VM) で実行されるとき、クライアント プログラムが SQL Database V12 に接続する場合、VM で次のポート範囲を開く必要があります。

- 11000 ～ 11999
- 14000 ～ 14999


SQL Database V12 のポートの詳細については、[こちら](sql-database-develop-direct-route-ports-adonet-v12.md)を参照してください。これらのポートは、SQL Database V12 のパフォーマンスの強化に必要です。


##<a id="limitations"></a>V12 へのアップグレード中とアップグレード後の制限事項


### V12 用ポータル


Azure には 3 つのポータルがありますが、それぞれ SQL Database V12 に関する機能が異なります


- [http://portal.azure.com/](http://portal.azure.com/)<br/>この Azure プレビュー ポータルは新しいもので、現段階ではプレビュー状態のままです。このポータルは、現段階では完全に一般公開 (GA) されていません。このポータルでは、次のことを実行できます。
 - V12 サーバーとデータベースを管理できます。
 - V11 データベースを V12 にアップグレードできます。


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>この Azure ポータルは最終的に廃止される予定です。このポータルでは、次のことを実行できます。
 - V12 サーバーとデータベースを管理できます。
 - V11 データベースを V12 にアップグレード*できません*。


- (http://*yourservername*.database.windows.net)<br/>Azure SQL Database の管理ポータル:
 - V12 サーバーを管理*できません*。


Visual Studio 2013 (VS2013) で Azure SQL Database に接続することをお勧めします。VS2013 は、次のようなタスクに使用できます。


- Transact-SQL ステートメントの実行。
- スキーマの設計。
- オンラインまたはオフラインでのデータベースの開発。


代わりに、VS2013 の無償ですべての機能を備えたバージョンである [Visual Studio Community 2013](https://www.visualstudio.com/ja-JP/news/vs2013-community-vs.aspx/) で接続することもできます。


以前の Azure ポータルでは、データベース ページで **[Visual Studio で開く]** をクリックしてコンピューター上で VS2013 を起動し、Azure SQL Database に接続できます。


代わりに、[CU6](http://support.microsoft.com/kb/3031047/) を含む SQL Server Management Studio (SSMS) 2014 を使用して Azure SQL Database に接続することもできます。詳細については、<br/>[Azure SQL Database のクライアント ツール更新プログラム](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)に関するブログを参照してください。


### V12 へのアップグレード*中*の制限事項


V11 データベースは、V12 へのアップグレード中も、引き続きデータ アクセスに使用できます。ただし、考慮すべきいくつかの制限があります。


| 制限事項 | 説明 |
| :--- | :--- |
| アップグレードの実行時間 | アップグレードの実行時間は、サーバーにあるデータベースのサイズ、エディション、数によって異なります。特に、次のようなデータベースを持つサーバーの場合は、アップグレード プロセスの実行に数時間から数日かかる場合があります。<br/><br/>* 50 GB を超える<br/>* Premium 以外のサービス階層<br/><br/>アップグレード中にサーバーで新しいデータベースを作成する場合も、アップグレードの実行時間が長くなる場合があります。 |
| geo レプリケーションなし | V11 からのアップグレードを実行している最中の V12 サーバーでは geo レプリケーションはサポートされません。 |
| データベースは V12 へのアップグレードの最終フェーズ中に短時間利用できなくなる | V11 サーバーに属しているデータベースは、アップグレード処理中も利用可能です。ただし、サーバーとデータベースへの接続は、最終フェーズ中に V11 から準備が完了した V12 への切り替えが始まると、短時間利用できなくなります。<br/><br/>この切り替え時間は 40 秒から 5 分に及ぶことがあります。ほとんどのサーバーでは、切り替えは 90 秒以内に完了すると予期されています。データベースの数が多い、またはデータベースの書き込みワークロードが重いサーバーでは、書き込み時間は長くなります。 |


### V12 へのアップグレード*後*の制限事項


| 制限事項 | 説明 |
| :--- | :--- |
| V11 に戻すことはできない | 置き換えによるアップグレードの後は、元に戻したり取り消したりすることはできません。 |
| Web または Business 階層 | アップグレードを開始すると、新しい V12 データベースでは Web や Business サービス階層を認識したり、受け付けたりできなくなります。 |



### V12 へのアップグレード*後*のエクスポートとインポート


[Azure プレビュー ポータル](http://portal.azure.com/)を使用して V12 データベースをエクスポートまたはインポートできます。または、次のいずれかのツールを使用してエクスポートまたはインポートすることもできます。


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


ただし、これらのツールを使用するには、まずそれぞれの最新の更新プログラムをインストールし、新しい V12 の機能が確実にサポートされるようにする必要があります。


- [SQL Server Management Studio 2014 用の累積的な更新プログラム 6](http://support2.microsoft.com/kb/3031047)
- [Visual Studio 2013 の SQL Server データベース ツール用の 2015年 2 月の更新プログラム](https://msdn.microsoft.com/data/hh297027)
- [Azure SQL Database V12 用の 2015 年 2月の Data-Tier Application Framework (DacFx)](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE]上記のツールへのリンクが、2015 年 3 月 2 日以降に更新されました。これらのツールの新しい更新プログラムを使用することをお勧めします。


#### 自動エクスポート


自動エクスポートは引き続きプレビューとして使用できます。自動エクスポートを使用する場合、クライアント ツールの更新プログラムは必要ありません。生成されたファイルを処理してオンプレミス サーバーに正常にインポートするには、前に示したツールの更新プログラムが必要です。


### 削除した V11 データベースの V12 に復元する

次のシナリオでは、削除した V11 Azure SQL Database を V12 Azure SQL Database サーバーに復元します。

1. V11 Azure SQL Database サーバーを利用していることを前提としています。<br/>そのサーバーでは、廃止された Web サービス階層と Business サービス階層を利用しています。
2. データベースを削除します。
3. サーバーを V12 にアップグレードします。
4. 次に、データベースをサーバーに復元します。<br/>それによってデータベースは V12 にアップグレードされ、Standard サービス階層の S0 レベルに設定されます。
5. S0 が適していない場合は、任意のサービス階層にデータベースを切り替えることができます。


### PowerShell コマンドレット


PowerShell コマンドレットを使用して、V11 を含め、V12 より前のバージョンから V12 への Azure SQL Database のアップグレードを開始、停止、監視できます。


これらの PowerShell コマンドレットに関する次のリファレンス ドキュメントを参照してください。


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Stop- コマンドレットは、一時停止ではなく取り消しを意味します。アップグレードを再開する方法は、最初からやり直す方法以外にありません。Stop- コマンドレットにより、該当するすべてのリソースがクリーンアップされ、解放されます。


## エラーの解決


何らかの理由で、アップグレードでエラーが発生した場合は、V11 データベースがそのまま有効なり、通常と同じように利用できます。


## 関連リンク


- Microsoft Azure の[プレビュー機能](http://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
 

<!---HONumber=Oct15_HO1-->