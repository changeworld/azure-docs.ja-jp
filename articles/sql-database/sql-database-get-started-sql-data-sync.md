---
title: Azure SQL データ同期の設定 | Microsoft Docs
description: このチュートリアルでは、Azure SQL データ同期をセットアップする方法を示します
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: 7cb7ce0ea81cf99c2ee14a6f209864894563d237
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111781"
---
# <a name="set-up-sql-data-sync"></a>SQL データ同期の設定
このチュートリアルでは、Azure SQL Database と SQL Server インスタンスの両方を含むハイブリッド同期グループを作成して、Azure SQL データ同期をセットアップする方法について学習します。 新しい同期グループには必要な構成をすべて行います。このため、新しい同期グループは設定したスケジュールで同期されます。

このチュートリアルは、少なくとも SQL Database および SQL Server をいくらか使用したことがある読者を対象にしています。 

SQL データ同期の概要については、[Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](sql-database-sync-data.md)に関する記事を参照してください。

SQL データ同期を構成する方法を示す完全な PowerShell の例については、次の記事を参照してください。
-   [PowerShell を使用して複数の Azure SQL Database 間で同期を行う](scripts/sql-database-sync-data-between-sql-databases.md)
-   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>手順 1 - 同期グループの作成

### <a name="locate-the-data-sync-settings"></a>データ同期設定の検索

1.  ブラウザーで Azure Portal に移動します。

2.  ポータルで、ダッシュボードから、またはツール バーの [SQL データベース] アイコンから、SQL データベースを検索します。

    ![Azure SQL データベースの一覧](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  **[SQL データベース]** ページで、データ同期のハブ データベースとして使用する既存の SQL データベースを選択します。[SQL データベース] ページが開きます。

    ハブ データベースは、同期グループが複数のデータベース エンドポイントを持つ、同期トポロジの中心になるエンドポイントです。 同じ同期グループ内の他のすべてのデータベース エンドポイント (つまり、すべてのメンバー データベース) は、ハブ データベースと同期します。

4.  選択したデータベースの [SQL データベース] ページで、**[別のデータベースに同期]** を選択します。 [データ同期] ページが開きます。

    ![[別のデータベースに同期] オプション](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>新しい同期グループの作成

1.  [データ同期] ページで **[新しい同期グループ]** を選択します。 **[新しい同期グループ]** ページが開きます (手順 1 の **[同期グループの作成]** が強調表示された状態になっています)。 一緒に、**[データ同期グループの作成]** ページが開きます。

2.  **[データ同期グループの作成]** ページで、以下の操作を行います。

    1.  **[同期グループ名]** フィールドで、新しい同期グループの名前を入力します。

    2.  **[メタデータ データベースの同期]** セクションで、新しいデータベースを作成するか (推奨)、既存のデータベースを使用するかを選択します。

        > [!NOTE]
        > Microsoft では、新しい空のデータベースを作成して同期メタデータ データベースとする方法をお勧めしています。 データ同期は、このデータベースにテーブルを作成し、頻繁に発生するワークロードを実行します。 このデータベースは、選択したリージョンのすべての同期グループが使用する同期メタデータ データベースとして自動的に共有されます。 同期メタデータ データベースやその名前は、削除しないと変更できません。

        **[新しいデータベース]** を選択した場合は、**[新しいデータベースの作成]** を選択します。 **[SQL データベース]** ページが開きます。 **[SQL データベース]** ページで、新しいデータベースの名前を指定し、構成します。 **[OK]** をクリックします。

        **[既存のデータベースを使用する]** を選択した場合は、一覧からデータベースを選択します。

    3.  **[自動同期]** セクションで、まず、**[オン]** または **[オフ]** を選択します。

        **[オン]** を選択した場合は、**[同期の頻度]** セクションで数値を入力し、[秒]、[分]、[時間]、または [日] を選択します。

        ![同期の頻度の指定](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  **[競合の解決]** セクションで、[ハブ側に合わせる] または [Member wins]\(メンバー側に合わせる\) を選択します。

        "ハブ側に合わせる" とは、競合が発生した場合、ハブ データベースのデータで、メンバー データベースの競合するデータを上書きすることを意味します。 "メンバー側に合わせる" とは、競合が発生した場合、メンバー データベースのデータで、ハブ データベースの競合するデータを上書きすることを意味します。 

        ![競合の解決方法の指定](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  **[OK]** を選択し、新しい同期グループが作成されてデプロイされるまで待ちます。

## <a name="step-2---add-sync-members"></a>手順 2 - 同期メンバーの追加

新しい同期グループが作成されてデプロイされると、**[新しい同期グループ]** ページで手順 2 の **[同期メンバーの追加]** が強調表示されます。

**[ハブ データベース]** セクションで、ハブ データベースを配置する SQL Database サーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。

![ハブ データベースが同期グループに追加された](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Azure SQL Database の追加

**[メンバー データベース]** セクションで、必要に応じて **[Azure データベースを追加]** を選択して、Azure SQL Database を同期グループに追加します。 **[Azure データベースの構成]** ページが開きます。

**[Azure データベースの構成]** ページで、以下の操作を行います。

1.  **[メンバー名の同期]** フィールドで、新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。

2.  **[サブスクリプション]** フィールドで、課金のために関連付ける Azure サブスクリプションを選択します。

3.  **[Azure SQL Server]** フィールドで、既存の SQL データベース サーバーを選択します。

4.  **[Azure SQL Database]** フィールドで、既存の SQL データベースを選択します。

5.  **[同期方向]** フィールドで、[双方向の同期]、[ハブへ]、または [ハブから] を選択します。

    ![新しい SQL Database 同期メンバーの追加](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  **[ユーザー名]** と **[パスワード]** フィールドに、メンバー データベースがある SQL Database サーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。

7.  **[OK]** を選択し、新しい同期メンバーが作成され、デプロイされるまで待ちます。

    ![新しい SQL Database 同期メンバーが追加された](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> オンプレミス SQL Server データベースの追加

**[メンバー データベース]** セクションで、必要に応じて **[オンプレミス データベースを追加]** を選択して、オンプレミスの SQL Server を同期グループに追加します。 **[オンプレミスの構成]** ページが開きます。

**[オンプレミスの構成]** ページで、以下の操作を行います。

1.  **[同期エージェント ゲートウェイの選択]** を選択します。 **[同期エージェントの選択]** ページが開きます。

    ![同期エージェント ゲートウェイの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  **[同期エージェント ゲートウェイの選択]** ページで、既存のエージェントを使用するか、新しいエージェントを作成するかを選択します。

    **[既存のエージェント]** を選択した場合は、一覧から既存のエージェントを選択します。

    **[新しいエージェントを作成します]** を選択した場合は、以下の操作を行います。

    1.  表示されているリンクからクライアント同期エージェント ソフトウェアをダウンロードして、SQL Server があるコンピューターにインストールします。
 
        > [!IMPORTANT]
        > クライアント エージェントがサーバーと通信できるように、ファイアウォールの送信 TCP ポート 1433 を開く必要があります。


    2.  エージェントの名前を入力します。

    3.  **[キーを作成し生成する]** を選択します。

    4.  エージェント キーをクリップボードにコピーします。
        
        ![新しい同期エージェントの作成](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  **[OK]** を選択して、**[同期エージェントの選択]** ページを閉じます。

    6.  SQL Server コンピューターでクライアント同期エージェント アプリを探して実行します。

        ![データ同期クライアント エージェント アプリ](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  同期エージェント アプリで **[Submit Agent Key]\(エージェント キーの送信\)** を選択します。 **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスが開きます。

    8.  **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスで、Azure Portal からコピーしたエージェント キーを貼り付けます。 また、メタデータ データベースが配置されている Azure SQL Database サーバーの既存の資格情報も指定します  (新しいメタデータ データベースを作成した場合、このデータベースはハブ データベースと同じサーバー上にあります)。**[OK]** を選択し、構成が完了するまで待ちます。

        ![エージェント キーとサーバー資格情報の入力](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   この時点でファイアウォールのエラーが発生する場合は、SQL Server コンピューターからの受信トラフィックを許可するように、Azure 上でファイアウォール規則を作成する必要があります。 ポータルで規則を手動で作成することもできますが、SQL Server Management Studio (SSMS) で作成する方が簡単でしょう。 SSMS で、Azure 上のハブ データベースに接続してみてください。 名前として、「<hub_database_name>.database.windows.net」を入力します。 Azure ファイアウォール規則を構成するには、ダイアログ ボックスに表示される手順に従います。 クライアント同期エージェント アプリに戻ります。

    9.  クライアント同期エージェント アプリで **[登録]** をクリックして、SQL Server データベースをエージェントに登録します。 **[SQL Server の構成]** ダイアログ ボックスが開きます。

        ![SQL Server データベースの追加と構成](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. **[SQL Server の構成]** ダイアログ ボックスで、SQL Server 認証と Windows 認証のどちらを使用して接続するかを選択します。 SQL Server 認証を選択した場合は、既存の資格情報を入力します。 SQL Server 名と、同期するデータベースの名前を指定します。**[接続テスト]** を選択して、設定をテストします。 次に、**[保存]** を選択します。 登録したデータベースが、一覧に表示されます。

        ![SQL Server データベースが登録されました](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. クライアント同期エージェント アプリを閉じることができます。

    12. ポータルの **[オンプレミスの構成]** ページで、**[データベースの選択]** を選択します。 **[データベースの選択]** ページが開きます。

    13. **[データベースの選択]** ページの **[メンバー名の同期]** フィールドで、新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。 一覧からデータベースを選択します。 **[同期方向]** フィールドで、[双方向の同期]、[ハブへ]、または [ハブから] を選択します。

        ![オンプレミス データベースの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. **[OK]** を選択して、**[データベースの選択]** ページを閉じます。 さらに **[OK]** を選択して **[オンプレミスの構成]** ページを閉じ、新しい同期メンバーが作成およびデプロイされるまで待ちます。 最後に、**[OK]** をクリックして **[同期メンバーの選択]** ページを閉じます。

        ![同期グループに追加されたオンプレミス データベース](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  SQL データ同期およびローカル エージェントに接続するには、ロール `DataSync_Executor` にユーザー名を追加します。 データ同期は、このロールを SQL Server インスタンス上に作成します。

## <a name="step-3---configure-sync-group"></a>手順 3 - 同期グループの構成

新しい同期グループ メンバーが作成およびデプロイされると、**[新しい同期グループ]** ページで手順 3 の **[同期グループの構成]** が強調表示されます。

1.  **[テーブル]** ページで、同期グループ メンバーの一覧からデータベースを選択し、**[スキーマの更新]** を選択します。

2.  使用可能なテーブルの一覧から、同期するテーブルを選択します。

    ![同期するテーブルの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  既定では、テーブル内のすべての列が選択されます。 同期の必要がない列がある場合は、その列のチェック ボックスをオフにします。主キー列は、選択されたままにしておいてください。

    ![同期するフィールドの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  最後に、**[保存]** を選択します。

## <a name="faq-about-setup-and-configuration"></a>セットアップと構成についてよくあるご質問

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>データ同期はどのくらいの頻度でデータを同期しますか? 
最小の頻度は 5 分ごとです。

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL データ同期はテーブルを完全に作成してプロビジョニングしますか?

同期スキーマ テーブルが同期先のデータベースにまだ作成されていない場合、SQL データ同期はユーザーが選んだ列でテーブルを作成します。 ただし、以下の理由から、この動作によって完全に忠実なスキーマにはなりません。

-   同期先テーブルにはユーザーが選んだ列だけが作成されます。 同期元テーブルの一部の列が同期グループに含まれていない場合、それらの列は同期先テーブルにプロビジョニングされません。

-   選択した列のインデックスだけが作成されます。 同期元テーブルのインデックスに同期グループに含まれていない列がある場合、それらのインデックスは同期先テーブルにプロビジョニングされません。

-   XML 型の列のインデックスはプロビジョニングされません。

-   CHECK 制約はプロビジョニングされません。

-   同期元テーブルの既存のトリガーはプロビジョニングされません。

-   ビューとストアド プロシージャは同期先データベースに作成されません。

これらの制限のため、次のことをお勧めします。
-   運用環境では、完全に忠実なスキーマを手動でプロビジョニングします。
-   サービスを試す目的であれば、SQL データ同期の自動プロビジョニング機能が問題なく動作します。

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>作成していないテーブルが表示されるのはなぜですか?  
データの同期により、データベース内に変更追跡のためのサイド テーブルが作成されます。 これらを削除しないでください。削除するとデータ同期が動作を停止します。

### <a name="is-my-data-convergent-after-a-sync"></a>同期後にデータは収束しますか?

必ずしもその必要はありません。 ハブと 3 つのスポーク (A、B、C) を持つ同期グループでは、同期は ハブから A、ハブから B、ハブから C に対して行われます。ハブから A への同期の "*後*" でデータベース A が変更された場合、次の同期タスクまで、その変更はデータベース B またはデータベース C に書き込まれません。

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>スキーマの変更を同期グループに反映するにはどうすればよいですか?

すべてのスキーマ変更を手動で行い、反映する必要があります。
1. ハブおよびすべての同期メンバーに対してスキーマ変更を手動でレプリケートします。
2. 同期スキーマを更新します。

**新しいテーブルと列の追加**。 新しいテーブルと列は、現在の同期に影響しません。データ同期は新しいテーブルと列が同期スキーマに追加されるまで、新しいテーブルと列を無視します。 新しいデータベース オブジェクトを追加するときに従うべき最善の手順は次のとおりです。
1. 新しいテーブルまたは列をハブおよびすべての同期メンバーに追加します。
2. 新しいテーブルまたは列を同期スキーマに追加します。
3. 新しいテーブルと列への値の挿入を開始します。

**列のデータ型の変更**。 既存の列のデータ型を変更した場合、データ同期は新しい値が同期スキーマで定義された元のデータ型に一致する限り動作を続行します。 たとえば、ソース データベース内の型を **int** から **bigint** に変更した場合、データ同期は **int** データ型にしては大きすぎる値を挿入するまで動作を続行します。 変更を完了するには、ハブおよびすべての同期メンバーに対してスキーマの変更を手動でレプリケートし、同期スキーマを更新します。

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>データ同期でデータベースをエクスポートおよびインポートするにはどうすればよいですか?
データベースを `.bacpac` ファイルとしてエクスポートし、そのファイルをインポートして新しいデータベースを作成した後、新しいデータベースでデータ同期を使うには、次の 2 つのことを行う必要があります。
1.  [このスクリプト](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql)を使って、**新しいデータベース**でデータ同期オブジェクトとサイド テーブルをクリーンアップします。 このスクリプトは、データベースからすべての必要なデータ同期オブジェクトを削除します。
2.  新しいデータベースで同期グループを再作成します。 古い同期グループが必要ない場合は削除します。

## <a name="faq-about-the-client-agent"></a>クライアント エージェントについてよくあるご質問

### <a name="why-do-i-need-a-client-agent"></a>なぜクライアント エージェントが必要ですか?

SQL データ同期サービスは、クライアント エージェントを使って SQL Server データベースと通信します。 このセキュリティ機能では、ファイアウォールの背後にあるデータベースと直接通信できません。 SQL データ同期サービスは、エージェントと通信するとき、暗号化された接続と、一意のトークンつまり "*エージェント キー*" を使います。 SQL Server データベースは、接続文字列とエージェント キーを使ってエージェントを認証します。 この設計では、高レベルのセキュリティがデータに提供されます。

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>何個のローカル エージェント UI インスタンスを実行できますか?

実行できる UI のインスタンスは 1 つだけです。

### <a name="how-can-i-change-my-service-account"></a>サービス アカウントを変更するにはどうすればよいですか?

クライアント エージェントをインストールした後でサービス アカウントを変更する唯一の方法は、クライアント エージェントをアンインストールし、新しいサービス アカウントで新しいクライアント エージェントをインストールすることです。

### <a name="how-do-i-change-my-agent-key"></a>エージェント キーを変更するにはどうすればよいですか?

エージェント キーは、エージェントによって一度だけ使われます。 エージェントを削除して新しいエージェントを再インストールするときに再利用したり、複数のエージェントで共用したりすることはできません。 既存のエージェントに対して新しいキーを作成する必要がある場合は、クライアント エージェントと SQL データ同期サービスで同じキーが記録されるようにする必要があります。

### <a name="how-do-i-retire-a-client-agent"></a>クライアント エージェントの使用を終了するにはどうすればよいですか?

すぐにエージェントを無効にしたり、使用を終了したりするには、ポータルでエージェント キーの再生成だけを行い、エージェント UI でのキーの送信は行わないでおきます。 キーを再生成すると、対応するエージェントがオンラインかオフラインかにかかわらず、前のキーは無効になります。

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>別のコンピューターにクライアント エージェントを移動するにはどうすればよいですか?

現在とは異なるコンピューターからローカル エージェントを実行する場合は、次のようにします。

1. 目的のコンピューターにエージェントをインストールします。

2. SQL データ同期ポータルにログインし、新しいエージェントのエージェント キーを再生成します。

3. 新しいエージェントの UI を使って、新しいエージェント キーを送信します。

4. 以前に登録されたオンプレミスのデータベースのリストをクライアント エージェントがダウンロードするまで待ちます。

5. 到達不能と表示されるすべてのデータベースに対し、データベースの資格情報を指定します。 これらのデータベースには、エージェントをインストールした新しいコンピューターから到達可能でなければなりません。

## <a name="next-steps"></a>次の手順
おめでとうございます。 SQL Database インスタンスと SQL Server データベースの両方を含む同期グループを作成しました。

SQL データ同期の詳細については、以下を参照してください。

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベースにまたがるデータの同期)](sql-database-sync-data.md)
-   [Azure SQL データ同期のベスト プラクティス](sql-database-best-practices-data-sync.md)
-   [Log Analytics を使用した Azure SQL データ同期の監視](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Azure SQL データ同期に関する問題のトラブルシューティング)](sql-database-troubleshoot-data-sync.md)

-   SQL データ同期を構成する方法を示す完全な PowerShell の例
    -   [PowerShell を使用した複数の Azure SQL データベース間の同期](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL データ同期 REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database の詳細については、以下を参照してください。

-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
