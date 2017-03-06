---
title: "クイック スタート: 初めての Azure SQL Database | Microsoft Docs"
description: "Azure Portal で SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、データベースを作成する方法について説明します。 また、Azure SQL Database での SQL Server Management Studio の使用についても説明します。"
keywords: "SQL データベース チュートリアル, SQL データベースの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3
ms.lasthandoff: 02/18/2017


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Azure Portal で SSMS を使用して、初めての Azure SQL データベースを作成、接続、照会する

このチュートリアルでは、Azure Portal で SQL Server Management Studio を使用して、Azure SQL データベースを作成、接続、照会する方法について説明します。 このチュートリアルでは、次のことを行います。

* 論理サーバー、サーバーレベルのファイアウォール規則、2 つのデータベースを含むリソース グループを作成する。
* Azure Portal で SQL Server Management Studio を使用して、サーバー プロパティとデータベース プロパティを確認する。
* Azure Portal で SQL Server Management Studio を使用して、データベースに対するクエリを実行する。

**推定所要時間**: このチュートリアルには約 30 分かかります (既に前提条件を満たしていることが前提です)。

> [!TIP]
> [PowerShell](sql-database-get-started-powershell.md) または [C#](sql-database-get-started-csharp.md) を使用して、Azure SQL データベースを作成、接続、照会する方法も学習することができます。
>

> [!NOTE]
> このチュートリアルでは、[SQL Database サーバーの概要](sql-database-server-overview.md)、[SQL データベースの概要](sql-database-overview.md)、および [Azure SQL Database のファイアウォール規則の概要](sql-database-firewall-configure.md)という学習トピックの内容を理解する際に役立ちます。 SQL Database サービスの概要については、[SQL Database の概要](sql-database-technical-overview.md)に関するページを参照してください。
>  

## <a name="prerequisites"></a>前提条件

* **Azure アカウント**。 [無料の Azure アカウントを作成する](https://azure.microsoft.com/free/)か、[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)ことができます。 

* **Azure の作成のアクセス許可**。 サブスクリプションの所有者または共同作成者ロールのメンバーであるアカウントを使用して Azure Portal に接続できることが必要です。 ロールベースのアクセス制御 (RBAC) の詳細については、「[Azure Portal でのアクセス管理の概要](../active-directory/role-based-access-control-what-is.md)」を参照してください。

* **SQL Server Management Studio**。 最新バージョンの SQL Server Management Studio (SSMS) は、「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」からダウンロードしてインストールすることができます。 新機能が継続的にリリースされているため、Azure SQL Database に接続する場合は、常に最新バージョンの SSMS を使用してください。

### <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

この手順では、[Azure アカウント](https://account.windowsazure.com/Home/Index)を使用して、Azure Portal に接続する方法を示します。

1. 任意のブラウザーを開き、 [Azure ポータル](https://portal.azure.com/)に接続します。
2. [Azure ポータル](https://portal.azure.com/)にサインインします。
3. **[サインイン]** ページが表示されたら、サブスクリプションの資格情報を入力します。
   
   ![[サインイン]](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>新しい論理 SQL サーバーを作成する

この手順では、Azure Portal で任意のリージョンに論理サーバーを作成する方法を示します。 論理サーバーは、SQL データベースを作成する場所となるオブジェクトであり、ユーザーに Azure SQL Database ファイアウォールを通じた接続を許可するファイアウォール規則を作成する場所となるオブジェクトです。 

1. **[新規]** をクリックし、「**SQL サーバー**」と入力して、**Enter** キーを押します。

    ![論理 SQL サーバー](./media/sql-database-get-started/logical-sql-server.png)
2. **[SQL サーバー (論理サーバー)]** をクリックします。
   
    ![論理 SQL サーバーの作成](./media/sql-database-get-started/create-logical-sql-server.png)
3. **[作成]** をクリックして、新しい [SQL Server (論理サーバーのみ)] ブレードを開きます。

    ![新しい論理 SQL サーバー](./media/sql-database-get-started/new-logical-sql-server.png)
3. **[サーバー名]** ボックスに、新しい論理サーバーの有効な名前を指定します。 緑色のチェック マークは、入力した名前が有効であることを示しています。
    
    ![新しいサーバー名](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > 新しいサーバーの完全修飾名は、グローバルで一意である必要があり、**<サーバー名>.database.windows.net** の形式になります。 このチュートリアルの後の方で、この完全修飾サーバー名を使用して、サーバーとデータベースに接続します。
    >
    
4. **[サーバー管理者ログイン]** ボックスに、このサーバーの SQL 認証ログイン用のユーザー名を指定します。 このログインは、サーバー プリンシパル ログインとも呼ばれます。 緑色のチェック マークは、入力した名前が有効であることを示しています。
    
    ![SQL の管理者ログイン](./media/sql-database-get-started/sql-admin-login.png)
5. **[パスワード]** ボックスと **[パスワードの確認]** ボックスに、サーバー プリンシパル ログイン アカウントのパスワードを指定します。 緑色のチェック マークは、入力したパスワードが有効であることを示しています。
    
    ![SQL の管理者パスワード](./media/sql-database-get-started/sql-admin-password.png)
6. **[サブスクリプション]** ドロップダウン ボックスで、オブジェクトを作成するアクセス許可が付与されたサブスクリプションを選択します。

    ![サブスクリプション](./media/sql-database-get-started/subscription.png)
7. **[リソース グループ]** ボックスで **[新規作成]** を選択し、新しいリソース グループのための有効な名前を指定します。 緑色のチェック マークは、入力した名前が有効であることを示しています。

    ![新しいリソース グループ](./media/sql-database-get-started/new-resource-group.png)

8. **[場所]** ボックスで、論理サーバーを作成するデータ センターを選択します。
    
    ![サーバーの場所](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > このブレードの **[Azure サービスにサーバーへのアクセスを許可する]** チェック ボックスは変更できません。 この設定は、サーバー ファイアウォールのブレードで変更できます。 詳細については、[セキュリティの概要](sql-database-control-access-sql-authentication-get-started.md)に関する記事を参照してください。
    >
    
9. **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

10. **[作成]** をクリックして、論理サーバーを作成するためのこのスクリプトを Azure にデプロイします。

    ![[作成] ボタン](./media/sql-database-get-started/create.png)

11. サーバーが作成されたら、サーバーの既定で表示されるプロパティを確認します。 

    ![[SQL Server] ブレード](./media/sql-database-get-started/sql-server-blade.png)
12. **[プロパティ]** をクリックし、論理 SQL サーバーの追加のプロパティを表示します。

    ![SQL Server のプロパティ](./media/sql-database-get-started/sql-server-properties.png)
13. 完全修飾サーバー名を、このチュートリアルで後から使用するためにクリップボードにコピーします。

    ![SQL Server の完全な名前](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

この手順では、Azure Portal でサーバーレベルのファイアウォール規則を作成する方法を示します。 既定では、Azure SQL Database ファイアウォールは、論理サーバーとそのデータベースへの外部接続を防ぎます。 サーバーに接続できるようにするには、次の手順で、接続元となるコンピューターの IP アドレスに対するファイアウォール規則を作成する必要があります。 詳細については、「[Azure SQL Database ファイアウォール規則の概要](sql-database-firewall-configure.md)」を参照してください。

1. SQL サーバーのブレードで **[ファイアウォール]** をクリックし、サーバーのファイアウォール ブレードを開きます。 クライアント コンピューターの IP アドレスが表示されることに注意してください。

    ![SQL Server のファイアウォール](./media/sql-database-get-started/sql-server-firewall.png)

2. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスに対するファイアウォール規則を作成します。

    ![クライアント IP の追加](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > 単一の IP アドレスまたはアドレスの範囲全体に対するファイアウォール規則を作成することができます。 ファイアウォールを開くと、SQL の管理者とユーザーは、有効な資格情報を持っているサーバー上の任意のデータベースにログインできます。
    >

4. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存し、**[OK]** をクリックして成功のダイアログ ボックスを閉じます。

    ![成功](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>SSMS を使用してサーバーに接続する

この手順では、SQL Server Management Studio を使用して SQL 論理サーバーに接続する方法を示します。 SSMS は、DBA が SQL サーバーとデータベースを管理するために使用する主要ツールです。

1. SQL Server Management Studio を開きます (Windows 検索ボックスに「**Microsoft SQL Server Management Studio**」と入力し、**Enter** キーを押して SSMS を開きます)。

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. **[サーバーに接続]** ダイアログ ボックスで、前の手順の完全修飾サーバー名を入力し、SQL Server 認証を選択してから、サーバーのプロビジョニング中に指定したログインとパスワードを指定します。

    ![[サーバーへの接続]](./media/sql-database-get-started/connect-to-server.png)
4. **[接続]** をクリックして接続を開始し、SSMS でオブジェクト エクスプローラーを開きます。

    ![サーバーに接続されました](./media/sql-database-get-started/connected-to-server.png)
5. オブジェクト エクスプローラーで、**[データベース]**、**[システム データベース]**、**[master]** の順に展開して、master データベース内のオブジェクトを表示します。

    ![master データベース](./media/sql-database-get-started/master-database.png)
6. **[master]** を右クリックし、**[新しいクエリ]** をクリックします。

    ![master データベースに対するクエリ実行](./media/sql-database-get-started/query-master-database.png)

8. クエリ ウィンドウで、次のクエリを入力します。

   ```select * from sys.objects```

9.  ツール バーの **[実行]** をクリックすると、master データベース内のすべてのシステム オブジェクトの一覧が返されます。

    ![master データベースのシステム オブジェクトに対するクエリ実行](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > SQL のセキュリティの概要については、[SQL 認証の概要](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
    >

## <a name="create-a-database-with-sample-data"></a>サンプル データを使用してデータベースを作成する

この手順では、前に作成した論理サーバーに関連付けられている Azure Portal のサンプル データでデータベースを作成する方法を示します。 

1. Azure Portal の既定のブレードで、**[SQL データベース]** をクリックします。

    ![SQL データベース](./media/sql-database-get-started/new-sql-database.png)
2. [SQL データベース] ブレードで、**[追加]** をクリックします。 

    ![SQL データベースの追加](./media/sql-database-get-started/add-sql-database.png)

    ![[SQL データベース] ブレード](./media/sql-database-get-started/sql-database-blade.png)
3. **[データベース名]** ボックスに、有効なデータベース名を指定します。

    ![SQL データベース名](./media/sql-database-get-started/sql-database-name.png)
4. **[ソースの選択]** で **[Sample (AdventureWorksLT) (サンプル (AdventureWorksLT))]** を選択します。
   
    ![Adventure Works LT](./media/sql-database-get-started/adventureworkslt.png)
5. **[サーバー]** で、自分のサーバーが選択されていることを確認します。 データベースは、サーバーに追加するときに、単一データベースとして追加すること (既定) も、エラスティック プールに追加することもできることに注意してください。 エラスティック プールの詳細については、[エラスティック プール](sql-database-elastic-pool.md)に関する記事を参照してください。

6. **[価格レベル]** で価格レベルを **[Basic]** に変更し、**[選択]** をクリックします。 価格レベルは必要に応じて後から引き上げることができますが、学習目的の場合は最もコストの低いレベルを使用することをお勧めします。

    ![[価格レベル]](./media/sql-database-get-started/pricing-tier.png)
7. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。

    ![[作成] ボタン](./media/sql-database-get-started/create.png)

8. データベースが作成されたら、Azure Portal でそのプロパティを確認します。 以降のチュートリアルは、このブレードで使用できるオプションをよく知るのに役立ちます。 

    ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Azure Portal でデータベースに対してクエリを実行する

この手順では、Azure Portal でデータベースに対してクエリを直接実行する方法を示します。 

1. [SQL データベース] ブレードで、ツール バーの **[ツール]** をクリックします。

    ![ツール](./media/sql-database-get-started/tools.png)
2. [ツール] ブレードで、**[クエリ エディター (プレビュー)]** をクリックします。

    ![クエリ エディター](./media/sql-database-get-started/query-editor.png)
3. クエリ エディターがプレビュー機能であることを示すチェック ボックスをクリックし、**[OK]** をクリックします。
4. **クエリ エディター** ブレードで、**[ログイン]** をクリックします。

    ![クエリ エディター ブレード](./media/sql-database-get-started/query-editor-blade.png)
5. [承認の種類] と [ログイン] を確認し、このログインのパスワードを指定します。 

    ![クエリ エディターの [ログイン]](./media/sql-database-get-started/query-editor-login.png)
6. **[OK]** をクリックして、ログインを試みます。
7. 認証されたら、クエリ ウィンドウに次のクエリを入力し、**[実行]** をクリックします。

   ```select * from sys.objects```

    ![クエリ エディターのクエリ](./media/sql-database-get-started/query-editor-query.png)

8. **[結果]** ウィンドウでクエリの結果を確認します。

    ![クエリ エディターの結果](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>SSMS を使用してデータベースに対するクエリを実行する

この手順では、SQL Server Management Studio を使用してデータベースに接続し、サンプル データに対してクエリを実行してデータベース内のオブジェクトを表示する方法を示します。

1. SQL Server Management Studio に切り替え、オブジェクト エクスプローラーで、**[データベース]** をクリックし、ツール バーの **[更新]** をクリックしてサンプル データベースを表示します。

    ![新しいサンプル データベース (SSMS)](./media/sql-database-get-started/new-sample-db-ssms.png)
2. オブジェクト エクスプローラーで、新しいデータベースを展開してそのオブジェクトを表示します。

    ![新しいサンプル データベースのオブジェクト (SSMS)](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. サンプル データベースを右クリックし、**[新しいクエリ]** をクリックします。

    ![新しいサンプル データベースのクエリ (SSMS)](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. クエリ ウィンドウで、次のクエリを入力します。

   ```select * from sys.objects```
   
9.  ツール バーの **[実行]** をクリックすると、サンプル データベース内のすべてのシステム オブジェクトの一覧が返されます。

    ![新しいサンプル データベースのシステム オブジェクトに対するクエリ実行 (SSMS)](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>SSMS を使用して空のデータベースを作成する

この手順では、SQL Server Management Studio を使用して新しいデータベースを作成する方法を示します。

1. オブジェクト エクスプローラーで、**[データベース]** を右クリックし、**[新しいデータベース]** をクリックします。

    ![新しい空のデータベース (SSMS)](./media/sql-database-get-started/new-blank-database-ssms.png)

2. **[新しいデータベース]** ダイアログ ボックスの [データベース名] ボックスにデータベース名を指定します。 

    ![新しい空のデータベース名 (SSMS)](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. [新しいデータベース] ダイアログ ボックスで、**[オプション]** をクリックし、[エディション] を **[Basic]** に変更します。

    ![新しい空のデータベース オプション (SSMS)](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > このダイアログ ボックスで、Azure SQL データベース用に変更できるその他のオプションを確認してください。 このようなオプションの詳細については、[データベースの作成](https://msdn.microsoft.com/library/dn268335.aspx)に関するページを参照してください。
    >

4. **[OK]** をクリックして空のデータベースを作成します。
5. 完了したら、オブジェクト エクスプローラーの [データベース] ノードを最新の情報に更新して、新しく作成された空のデータベースを表示します。 

    ![オブジェクト エクスプローラーの新しい空のデータベース](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>接続のトラブルシューティング

Azure SQL Database への接続に失敗すると、エラー メッセージが表示されます。 接続の問題は、SQL Azure データベースの再構成、ファイアウォールの設定、接続タイムアウト、または間違ったログイン情報によって発生する場合があります。 接続のトラブルシューティング ツールについては、「[Troubleshooting connectivity issues with Microsoft Azure SQL Database (Microsoft Azure SQL Database の接続に関する問題のトラブルシューティング)](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)」を参照してください。

## <a name="delete-a-single-database-in-the-azure-portal"></a>Azure Portal で単一のデータベースを削除する

この手順では、Azure Portal で単一のデータベースを削除する方法を示します。

1. Azure Portal の [SQL データベース] ブレードで、削除するデータベースをクリックします。 
2.  該当 SQL データベースに対して、**[削除]** をクリックします。

    ![データベースの削除](./media/sql-database-get-started/delete-database.png)
2. 確認画面で **[はい]** をクリックして、このデータベースを削除します。

    ![データベースの削除 - [はい]](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> データベースのリテンション期間中は、サービス主導の自動バックアップからデータベースを復元することができます (サーバー自体を削除しない場合)。 Basic エディションのデータベースの場合、7 日以内であればデータベースを復元できます。 他のすべてのエディションの場合、35 日以内であればデータベースを復元できます。 サーバー自体を削除した場合、サーバーも、そのサーバーの削除されたデータベースも復旧できません。 データベースのバックアップの詳細については、「[SQL Database バックアップについての詳細情報](sql-database-automated-backups.md)」を参照してください。バックアップからデータベースを復元する方法の詳細については、[データベースの復元](sql-database-recovery-using-backups.md)に関するページを参照してください。 削除されたデータベースを復元する方法については、[Azure Portal を使用した、削除済み Azure SQL Database の復元](sql-database-restore-deleted-database-portal.md)に関するページをご覧ください。
>


## <a name="next-steps"></a>次のステップ
これで、このチュートリアルは完了です。このチュートリアルで学習した内容に基づいたチュートリアルが多数あるため、探してみてください。 

- SQL Server 認証のチュートリアルを開始するには、[SQL の認証と承認](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- Azure Active Directory 認証のチュートリアルを開始するには、[AAD の認証と承認](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。
* Azure Portal でサンプル データベースに対してクエリを実行する場合は、「[Public preview: Interactive query experience for SQL databases (パブリック プレビュー: SQL データベース用の対話型クエリ機能)](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/)」を参照してください。
* Excel に詳しい場合は、 [Excel を使用した Azure SQL データベースへの接続](sql-database-connect-excel.md)方法を参照してください。
* コーディングを開始する準備ができている場合、「 [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」でプログラミング言語を選択します。
* オンプレミスの SQL Server データベースを Azure に移行する場合は、[SQL Database へのデータベースの移行](sql-database-cloud-migrate.md)に関する記事を参照してください。
* BCP コマンド ライン ツールを使用して CSV ファイルから新しいテーブルにデータを読み込む場合は、[BCP を使用した CSV ファイルから SQL Database へのデータの読み込み](sql-database-load-from-csv-with-bcp.md)に関するページを参照してください。
* テーブルやその他のオブジェクトの作成を開始する場合は、「[テーブルの作成](https://msdn.microsoft.com/library/ms365315.aspx)」の「テーブルを作成するには」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- 技術の概要については、[SQL Database の概要](sql-database-technical-overview.md)に関するページを参照してください。
- 料金情報については、[Azure SQL Database の料金](https://azure.microsoft.com/pricing/details/sql-database/)に関するページを参照してください。


