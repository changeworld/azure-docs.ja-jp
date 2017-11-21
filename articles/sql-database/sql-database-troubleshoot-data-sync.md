---
title: "Azure SQL データ同期のトラブルシューティング | Microsoft Docs"
description: "Azure SQL データ同期における一般的な問題のトラブルシューティングについて説明します"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Azure SQL データ同期 (プレビュー) に関する問題のトラブルシューティング

この記事では、SQL データ同期 (プレビュー) チームで現在把握している問題のトラブルシューティングを行う方法について説明します。 問題に対して対処法がある場合は、ここにその対処法を示します。

SQL データ同期の概要については、[Azure SQL データ同期 (プレビュー) を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-database-sync-data.md)方法に関するページを参照してください。
                                                           
## <a name="my-client-agent-doesnt-work"></a>クライアント エージェントが動作しない

### <a name="description-and-symptoms"></a>説明および現象

クライアント エージェントを使用しようとすると、次のエラー メッセージが表示されます。

"Sync failed with exception (例外で同期に失敗しました) There was an error while trying to deserialize parameter www.microsoft.com/.../05:GetBatchInfoResult. (パラメーター www.microsoft.com/.../05:GetBatchInfoResult を逆シリアル化しようとしているときにエラーが発生しました。) See InnerException for more details. (詳細については、InnerException を参照してください。)

"Inner exception message: Type 'Microsoft.Synchronization.ChangeBatch' is an invalid collection type since it does not have a default constructor. (内部例外メッセージ: タイプ 'Microsoft.Synchronization.ChangeBatch' は、既定のコンストラクターがないため無効なコレクション型です。)"


### <a name="cause"></a>原因

このエラーは、SQL データ同期 (プレビュー) の問題です。

この問題の最も一般的な原因は次のとおりです。

-   Windows 8 Developer Preview を実行している、または

-   .NET 4.5 がインストールされている。

### <a name="solution-or-workaround"></a>解決策または対処法

Windows 8 Developer Preview が実行されておらず、かつ .NET Framework 4.5 がインストールされていないコンピューターにクライアント エージェントをインストールしてください。

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>アンインストールを取り消した後、クライアント エージェントが動作しない

### <a name="description-and-symptoms"></a>説明および現象

クライアント エージェントのアンインストールを取り消したにもかかわらず、クライアント エージェントが動作しません。

### <a name="cause"></a>原因

この問題は、SQL データ同期 (プレビュー) クライアント エージェントに資格情報が格納されないために発生します。

### <a name="solution-or-workaround"></a>解決策または対処法

以下に示す 2 つの解決策を試してみてください。

-   1 つ目の方法として、services.msc を使用して、クライアント エージェントの資格情報を再入力します。

-   2 つ目の方法として、今のクライアント エージェントをアンインストールした後、新規にクライアント エージェントをインストールします。 [ダウンロード センター](http://go.microsoft.com/fwlink/?linkid=221479)から最新のクライアント エージェントをダウンロードしてインストールします。

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>データベースがエージェントのドロップダウンに表示されない

### <a name="description-and-symptoms"></a>説明および現象

既存の SQL Server データベースを同期グループに追加しようとしたときに、対象のデータベースがドロップダウン リストに表示されません。

### <a name="cause"></a>原因

この問題には、以下のような複数の原因が考えられます。

-   クライアント エージェントと同期グループが異なるデータ センターにある。

-   クライアント エージェントのデータベースの一覧が最新ではない。

### <a name="solution"></a>解決策

解決策は、原因に応じて異なります。

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>クライアント エージェントと同期グループが異なるデータ センターにある

クライアント エージェントと同期グループの両方が同じデータ センターに配置されている必要があります。 次のいずれかの手順を実行して、この構成を設定できます。

-   同期グループと同じデータ センターに新しいエージェントを作成します。 次に、そのエージェントにデータベースを登録します。 詳細については、[SQL データ同期 (プレビュー) クライアント エージェントのインストール方法](#install-a-sql-data-sync-client-agent)に関するセクションを参照してください。

-   現在の同期グループを削除します。 次に、エージェントと同じデータ センターに同期グループを再作成します。

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>クライアント エージェントのデータベースの一覧が最新ではない

クライアント エージェント サービスをいったん停止した後、再び開始します。
ローカル エージェントは、エージェント キーの最初の送信時にのみ、関連付けられているデータベースの一覧をダウンロードします。それ以降のエージェント キーの送信時にはダウンロードしません。 したがって、エージェントの移動中に登録されたデータベースは、元のエージェント インスタンスに表示されません。

## <a name="client-agent-doesnt-start-error-1069"></a>クライアント エージェントが起動しない (エラー 1069)

### <a name="description-and-symptoms"></a>説明および現象

SQL Server をホストしているコンピューターでエージェントが実行されていないことに気が付きました。 そこで、エージェントを手動で起動しようとすると、"エラー 1069: ログオンに失敗したため、サービスを開始できませんでした。" というエラー メッセージを示すエラー ダイアログが表示されます。

![データ同期エラー 1069 のダイアログ ボックス](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>原因

このエラーの原因としては、エージェントを作成してログイン パスワードを指定した後でローカル サーバー上のパスワードが変更されたことが考えられます。

### <a name="solution-or-workaround"></a>解決策または対処法

エージェントのパスワードを現在のサーバー パスワードに更新します。

1. [SQL Data Sync (Preview) client agent Preview]\(SQL データ同期 (プレビュー) クライアント エージェント プレビュー\) サービスを見つけます。

    a. **[開始]**をクリックします。

    b. 検索ボックスに「services.msc」と入力します。

    c. 検索結果で、[サービス] をクリックします。

    d. **[サービス]** ウィンドウで、**[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** のエントリまでスクロールします。

2. エントリを右クリックし、**[停止]** を選択します。

3. エントリを右クリックし、**[プロパティ]** をクリックします。

4. **[SQL Data Sync (Preview) Agent Preview Properties]\(SQL データ同期 (プレビュー) エージェント プレビューのプロパティ\)** ウィンドウで、**[ログイン]** タブをクリックします。

5. [パスワード] ボックスにパスワードを入力します。

6. [パスワードの確認] ボックスに、同じパスワードを入力します。

7. **[適用]** をクリックし、**[OK]** をクリックします。

8. **[サービス]** ウィンドウで、**[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** サービスを右クリックし、**[開始]** をクリックします。

9. **[サービス]** ウィンドウを閉じます。

## <a name="i-get-a-disk-out-of-space-message"></a>"disk out of space (ディスク領域不足)" メッセージが表示される

### <a name="cause"></a>原因

削除する必要があるファイルが削除されずに残っている場合、"disk out of space (ディスク領域不足)" メッセージが表示されることがあります。 この状況は、ウイルス対策ソフトウェアが原因で発生したり、削除操作が試行されたときにファイルが開かれていたために発生したりすることがあります。

### <a name="solution"></a>解決策

解決策として、`%temp%` の下の同期ファイルを手動で削除 (`del \*sync\* /s`) した後、サブディレクトリも削除します。

> [!IMPORTANT]
> 同期が完了するのを待ってからファイルを削除します。

## <a name="i-cannot-delete-my-sync-group"></a>同期グループを削除できない

### <a name="description-and-symptoms"></a>説明および現象

同期グループを削除しようとしても失敗します。

### <a name="causes"></a>原因

以下のいずれかに該当すると、同期グループの削除に失敗する可能性があります。

-   クライアント エージェントがオフラインになっている。

-   クライアント エージェントがアンインストールされているか、見つからない。 

-   データベースがオフラインになっている。 

-   同期グループのプロビジョニング中または同期中である。 

### <a name="solutions"></a>解決方法

同期グループを削除できない問題を解決するには、次のことを確認します。

-   クライアント エージェントがオンラインになっていることを確認してから、再試行します。

-   クライアント エージェントがアンインストールされているか、見つからない場合:

    a. SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合は、そのファイルを削除します。

    b. 同じオンプレミス コンピューターか、別のオンプレミス コンピューターにエージェントをインストールし、ポータルから、オフラインと表示されているエージェント用に生成されたエージェント キーを送信します。

-   **SQL データ同期 (プレビュー) サービスが停止している。**

    a. **[スタート]** メニューで、"サービス" を検索します。

    b. 検索結果で、[サービス] をクリックします。

    c. **[SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\)** サービスを見つけます。

    d. サービスの状態が **[停止]** の場合は、サービス名を右クリックし、ドロップダウン メニューから **[開始]** を選択します。

-   SQL Database と SQL Server データベースがすべてオンラインになっていることを確認します。

-   プロビジョニングまたは同期プロセスが終了するまで待ちます。 次に、同期グループの削除を再試行します。

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>ポータル UI で、クライアント エージェントに関連付けられたオンプレミス データベースの同期に失敗する

### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) のポータル UI で、エージェントに関連付けられたオンプレミス データベースの同期に失敗します。 エージェントを実行しているローカル コンピューターのイベント ログに、ディスクに十分な空き領域がないことを示す System.IO.IOException エラーが記録されます。

### <a name="solution-or-workaround"></a>解決策または対処法

%TEMP% ディレクトリがあるドライブ上により多くの空き領域を確保します。

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>オンプレミスの SQL Server データベースの登録を解除できない

### <a name="cause"></a>原因

おそらく、既に削除されているデータベースの登録を解除しようとしています。

### <a name="solution-or-workaround"></a>解決策または対処法

オンプレミスの SQL Server データベースの登録を解除するには、データベースを選択し、**[Force Delete]\(強制削除\)** をクリックします。

この操作を実行しても同期グループからデータベースを削除できない場合は、次の操作を実行します。

1. クライアント エージェント ホスト サービスをいったん停止した後、再び開始します。

    a. [スタート] メニューをクリックします。

    b. 検索ボックスに「*services.msc*」と入力します。

    c. 結果ウィンドウの [プログラム] セクションで、**[サービス]** をダブルクリックします。

    d. **[SQL データ同期 (プレビュー)]** サービスを見つけて右クリックします。

    e. サービスが実行中の場合は、停止します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 右クリックし、**[開始]** を選択します。

    g. データベースの登録が解除されているかどうかを確認します。 登録が解除されている場合は、作業は終了です。 それ以外の場合は、次の手順に進みます。

2. クライアント エージェント アプリ (SqlAzureDataSyncAgent) を開きます。

3. **[資格情報の編集]** をクリックし、データベースにアクセスできるように資格情報を入力します。

4. 登録の解除に進みます。

## <a name="i-cannot-submit-the-agent-key"></a>エージェント キーを送信できない

### <a name="description-and-symptoms"></a>説明および現象

エージェントのキーを作成または再作成した後、そのキーを SqlAzureDataSyncAgent アプリケーションで送信しようとしても、送信が完了しません。

![同期エラー ダイアログ ボックス - エージェント キーを送信できない](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

続行する前に、次のいずれかの条件が満たされていないことが問題の原因ではないか、確認してください。

-   SQL データ同期 (プレビュー) Windows サービスが実行されている。

-   [SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\) Windows サービスのサービス アカウントがネットワークにアクセスできる。

-   クライアント エージェントがロケーター サービスに接続できる。 次のレジストリ キーに値 "https://locator.sync.azure.com/LocatorServiceApi.svc" があることを確認します。

    -   x86 コンピューターの場合: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   x64 コンピューターの場合: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>原因

各ローカル エージェントは、エージェント キーによって一意に識別されます。 キーは、動作するために次の 2 つの条件を満たす必要があります。

-   SQL データ同期 (プレビュー) サーバーとローカル コンピューター上のクライアント エージェント キーが同一である必要があります。

-   クライアント エージェント キーは 1 回だけ使用することができます。

### <a name="solution-or-workaround"></a>解決策または対処法

エージェントが動作していない場合、これらの条件の 1 つまたは両方が満たされていないことにその原因があります。 再び動作するようにエージェントを設定するには、次の操作を実行します。

1. 新しいキーを生成します。

2. 新しいキーをエージェントに適用します。

新しいキーをエージェントに適用するには、次の操作を実行します。

1. エクスプローラーを使用して、エージェントのインストール ディレクトリに移動します。 既定のインストール ディレクトリは `c:\\program files (x86)\\microsoft sql data sync` です。

2. `bin` サブディレクトリをダブルクリックします。

3. `SqlAzureDataSyncAgent` アプリケーションを起動します。

4. **[Submit Agent Key]\(エージェント キーの送信\)** をクリックします。

5. 表示されたスペースにクリップボードからキーを貼り付けます。

6. **[OK]**をクリックします。

7. プログラムを閉じます。

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>システム サービスを開始するのに十分な特権がない

### <a name="cause"></a>原因

このエラーは、次の 2 つの状況で発生します。

-   ユーザー名やパスワードが正しくない。

-   指定されたユーザー アカウントに、サービスとしてログオンするための十分な特権がない。

### <a name="solution-or-workaround"></a>解決策または対処法

"サービスとしてログオン" 資格情報をユーザー アカウントに与えます。

1. **スタート、コントロール パネル、[管理ツール]、[ローカル セキュリティ ポリシー]、[ローカル ポリシー]、[User Rights Management]\(ユーザー権利の管理\)** の順に移動します。

2. **[サービスとしてログオン]** エントリを見つけてクリックします。

3. **[Log on as a service Properties]\(サービスとしてログオンのプロパティ\)** ダイアログで、ユーザー アカウントを追加します。

4. **[適用]**、**[OK]** の順にクリックします。

5. ウィンドウを閉じます。

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>ローカル同期エージェント アプリでローカル同期サービスに接続できない

### <a name="solution-or-workaround"></a>解決策または対処法

次の手順を試してみてください。

1. アプリを終了します。

2. [コンポーネント サービス] パネルを開きます。

    a. タスク バーの検索ボックスに「services.msc」と入力します。

    b. 検索結果で、[サービス] をダブルクリックします。

3. [SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\) サービスをいったん停止した後、再び開始します。

4. アプリを再起動します。

## <a name="install-uninstall-or-repair-fails"></a>インストール、アンインストール、または修復に失敗する

### <a name="cause"></a>原因

このエラーには複数の原因が考えられます。 このエラーの具体的な原因を特定するには、ログを調べる必要があります。

### <a name="solution-or-workaround"></a>解決策または対処法

発生したエラーの具体的な原因を特定するには、Windows インストーラー ログを生成し、内容を確認する必要があります。 ログ記録はコマンド ラインから有効にすることができます。 たとえば、ダウンロードした AgentServiceSetup.msi ファイルが LocalAgentHost.msi であるとします。 次のコマンド ラインを使用してログ ファイルを生成し、内容を確認します。

-   インストールの場合: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   アンインストールの場合: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Windows インストーラーによって実行されるすべてのインストールのログ記録を有効にすることもできます。 Microsoft サポート技術情報の記事「[Windows インストーラーのログの記録を有効にする方法](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)」には、Windows インストーラーのログの記録を有効にするワンクリック ソリューションが用意されています。 また、これらのログの場所も示されています。

## <a name="a-database-has-an-out-of-date-status"></a>データベースの状態が [Out-of-Date]\(最新ではありません\) になっている

### <a name="cause"></a>原因

SQL データ同期 (プレビュー) では、45 日以上オフライン状態が続いているデータベースをサービスから除外します (データベースがオフラインになったときからカウントされます)。 オフライン状態が 45 日以上続いていたデータベースがオンラインに戻った場合は、その状態が [Out-of-Date]\(最新ではありません\) に設定されます。

### <a name="solution-or-workaround"></a>解決策または対処法

[Out-of-Date]\(最新ではありません\) 状態を回避するには、データベースが 45 日以上オフラインにならないようにします。

データベースの状態が [Out-of-Date]\(最新ではありません\) の場合は、次の操作を実行する必要があります。

1. 同期グループから [Out-of-Date]\(最新ではありません\) 状態のデータベースを削除します。

2. そのデータベースを同期グループに再び追加します。

> [!WARNING]
> このデータベースがオフラインの間に加えた変更はすべて失われます。

## <a name="a-sync-group-has-an-out-of-date-status"></a>同期グループの状態が [Out-of-Date]\(最新ではありません\) になっている

### <a name="cause"></a>原因

45 日間のリテンション期間全体にわたって 1 つまたは複数の変更を適用できなかった場合、同期グループは最新とは言えない可能性があります。

### <a name="solution-or-workaround"></a>解決策または対処法

[Out-of-Date]\(最新ではありません\) 状態を回避するには、履歴ビューアーで同期ジョブの結果を定期的に調べ、適用に失敗した変更を調査して解決します。

同期グループの状態が [Out-of-Date]\(最新ではありません\) の場合は、同期グループをいったん削除した後、再作成する必要があります。

## <a name="i-see-erroneous-data-in-my-tables"></a>テーブルにエラーのあるデータが含まれる

### <a name="description-and-symptoms"></a>説明および現象

データベース内のスキーマが異なる一方で名前が同じであるテーブルが同期の対象である場合、同期後にこれらのテーブルにエラーのあるデータが表示されます。

### <a name="cause-and-fix"></a>原因と解決策

SQL データ同期 (プレビュー) のプロビジョニング プロセスでは、スキーマが異なる一方で名前が同じであるテーブルに対して同じ追跡テーブルが使用されます。 その結果、両方のテーブルからの変更が同じ追跡テーブルに反映され、この動作によって同期中にエラーのあるデータの変更が発生します。

### <a name="resolution-or-workaround"></a>解決策または対処法

(同期の対象となるテーブルが異なるスキーマに属している場合でも) テーブル名が異なっていることを確認します。

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>同期に成功した後に一貫性のない主キー データが表示される

### <a name="description-and-symptoms"></a>説明および現象

同期が成功と報告され、失敗またはスキップされた行がログに示されていないにもかかわらず、同期グループ内のデータベース間で主キーのデータに不整合が発生します。

### <a name="cause"></a>原因

この動作は仕様です。 任意の主キー列に変更が加えられると、主キーが変更された行のデータに不整合が発生します。

### <a name="resolution-or-workaround"></a>解決策または対処法

この問題を回避するには、主キー列のデータが変更されないようにします。

この問題が発生した後に修正するには、影響を受けた行を同期グループ内のすべてのエンドポイントから削除した後、再び挿入する必要があります。

## <a name="i-see-a-significant-degradation-in-performance"></a>パフォーマンスが大幅に低下する

### <a name="description-and-symptoms"></a>説明および現象

パフォーマンスが大幅に低下し、データ同期 UI を起動できなくなることもあります。

### <a name="cause"></a>原因

最も可能性の高い原因は同期ループです。 同期ループは、同期グループ A による同期によって同期グループ B による同期がトリガーされ、さらにそれによって同期グループ A による同期がトリガーされるときに発生します。実際の状況はより複雑で、ループに 3 つ以上の同期グループが含まれることもありますが、重大な要因は、互いに重複する同期グループによって引き起こされる同期の循環トリガーがあることです。

### <a name="resolution-or-workaround"></a>解決策または対処法

最善の解決策は予防です。 同期グループに循環参照がないことを確認します。 1 つの同期グループによって同期される行を別の同期グループによって同期することはできません。

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>関連付けられているオンプレミス データベースにアクセスできない場合、クライアント エージェントをポータルから削除できない

### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) クライアント エージェントに登録されているローカル エンド ポイント (つまり、データベース) にアクセスできない場合、クライアント エージェントを削除できません。

### <a name="cause"></a>原因

アクセスできないデータベースがエージェントに登録されたままであるため、ローカル エージェントを削除することはできません。 エージェントを削除しようとすると、削除プロセスはデータベースにアクセスしようとしますが失敗します。

### <a name="resolution-or-workaround"></a>解決策または対処法

アクセスできないデータベースを削除するには、"強制削除" を使用します。

> [!NOTE]
> "強制削除" の後に同期メタデータ テーブルが残っている場合は、deprovisioningutil.exe を使用してクリーンアップします。

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>エージェントをアンインストール/停止してから 3 分以内に同期グループを削除できない

### <a name="description-and-symptoms"></a>説明および現象

関連付けられている SQL データ同期 (プレビュー) クライアント エージェントをアンインストール/停止してから 3 分以内に同期グループを削除することはできません。

### <a name="resolution-or-workaround"></a>解決策または対処法

1. 関連付けられている同期エージェントがオンラインのときに同期グループを削除します (推奨)。

2. エージェントがオフラインになっているものの、インストールはされている場合は、オンプレミスのコンピューターでオンラインにします。 次に、SQL データ同期 (プレビュー) ポータルでエージェントの状態がオンラインとして表示されるのを待ってから、同期グループを削除します。

3. エージェントがアンインストールされたためにオフラインになっている場合は、次の操作を実行します。 その後、同期グループを削除します。

    a.  SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合はこのファイルを削除します。

    b.  同じオンプレミス コンピューターか、別のオンプレミス コンピューターにエージェントをインストールし、ポータルから、オフラインと表示されているエージェント用に生成されたエージェント キーを送信します。

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>同期グループが処理状態で停止する

### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) の同期グループが長時間処理状態のまま、停止コマンドに応答せず、ログに新しいエントリが表示されません。

### <a name="causes"></a>原因

次のいずれかの条件が満たされると、同期グループが処理状態で停止することがあります。

-   **クライアント エージェントがオフラインになっている。** クライアント エージェントがオンラインになっていることを確認してから、再試行します。

-   **クライアント エージェントがアンインストールされているか、見つからない。** クライアント エージェントがアンインストールされているか、見つからない場合:

    1. SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合は、そのファイルを削除します。

    2. 同一または別のオンプレミスのコンピューターにエージェントをインストールします。 次に、ポータルから、オフラインと表示されているエージェント用に生成されたエージェント キーを送信します。

-   **SQL データ同期 (プレビュー) サービスが停止している。**

    1. **[スタート]** メニューで、"サービス" を検索します。

    2. 検索結果で、[サービス] をクリックします。

    3. **[SQL データ同期 (プレビュー)]** サービスを見つけます。

    4. サービスの状態が **[停止]** の場合は、サービス名を右クリックし、ドロップダウン メニューから **[開始]** を選択します。

### <a name="solution-or-workaround"></a>解決策または対処法

問題を解決できない場合は、Microsoft サポート側で同期グループの状態をリセットすることができます。 状態をリセットするには、[Azure SQL Database フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)で、フォーラム投稿を作成し、サブスクリプション ID とリセットする必要があるグループの同期グループ ID を明記してください。 Microsoft サポート エンジニアが投稿に対応し、状態がリセットされたたらその旨を連絡します。

## <a name="next-steps"></a>次のステップ
SQL データ同期の詳細については、以下を参照してください。

-   [Azure SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-database-sync-data.md)
-   [Azure SQL データ同期の概要](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL データ同期のベスト プラクティス](sql-database-best-practices-data-sync.md)

-   SQL データ同期を構成する方法を示す完全な PowerShell の例
    -   [PowerShell を使用した複数の Azure SQL データベース間の同期](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL データ同期 REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database の詳細については、以下を参照してください。

-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
