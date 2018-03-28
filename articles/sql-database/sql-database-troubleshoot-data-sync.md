---
title: Azure SQL データ同期 (プレビュー) のトラブルシューティング | Microsoft Docs
description: Azure SQL データ同期 (プレビュー) における一般的な問題のトラブルシューティングの方法について説明します。
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a24e16abd78a89d6143c00fc08a3ef91d7dd8202
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>SQL データ同期 (プレビュー) に関する問題のトラブルシューティング

この記事では、Azure SQL データ同期 (プレビュー) に関する既知の問題のトラブルシューティングを行う方法について説明します。 問題の解決方法がある場合は、ここにその方法を示します。

SQL データ同期 (プレビュー) の概要については、[Azure SQL データ同期 (プレビュー) を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](sql-database-sync-data.md)に関する記事をご覧ください。

## <a name="sync-issues"></a>同期に関する問題

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>ポータル UI で、クライアント エージェントに関連付けられているオンプレミス データベースの同期に失敗する

#### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) のポータル UI で、エージェントに関連付けられているオンプレミス データベースの同期に失敗します。 エージェントを実行しているローカル コンピューターのイベント ログに、System.IO.IOException エラーが記録されます。 このエラーには、ディスクの空き領域が不足していると記載されています。

#### <a name="resolution"></a>解決策

%TEMP% ディレクトリがあるドライブ上の空き領域を増やします。

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>同期グループが処理状態で停止する

#### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) の同期グループが、長時間にわたり処理状態になっています。 **停止** コマンドに反応せず、ログに新しいエントリが記録されていません。

#### <a name="cause"></a>原因

次のいずれかの条件が満たされると、同期グループが処理状態のままになることがあります。

-   **クライアント エージェントがオフラインになっている**。 クライアント エージェントがオンラインになっていることを確認してから、再試行します。

-   **クライアント エージェントがアンインストールされているか、見つからない**。 クライアント エージェントがアンインストールされているか、見つからない場合:

    1. SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合は、このファイルを削除します。
    2. オンプレミスのコンピューター (同じコンピューターでも別のコンピューターでもかまいません) にエージェントをインストールします。 次に、オフラインと表示されているエージェント用にポータルで生成されたエージェント キーを送信します。

-   **SQL データ同期サービスが停止している**。

    1. **[スタート]** メニューで、**サービス**を検索します。
    2. 検索結果で、**[サービス]** を選択します。
    3. **[SQL データ同期 (プレビュー)]** サービスを見つけます。
    4. このサービスの状態が **[停止]** の場合は、サービス名を右クリックして **[開始]** を選択します。

#### <a name="resolution"></a>解決策

上記の情報で同期グループの処理状態を解除できない場合は、Microsoft サポートが同期グループの状態をリセットします。 同期グループの状態のリセットを依頼するには、[Azure SQL Database フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)に投稿してください。 この投稿には、サブスクリプション ID およびリセットが必要なグループの同期グループ ID を記載してください。 Microsoft サポート エンジニアが投稿に対応し、状態がリセットされたらその旨を連絡します。

### <a name="i-see-erroneous-data-in-my-tables"></a>テーブルにエラーのあるデータが含まれる

#### <a name="description-and-symptoms"></a>説明および現象

同期に、名前が同じであるもののデータベース スキーマが異なるテーブルが含まれている場合、同期後のテーブルには誤ったデータが表示されます。

#### <a name="cause"></a>原因

SQL データ同期 (プレビュー) のプロビジョニング プロセスでは、スキーマが異なる一方で名前が同じであるテーブルについては、同じ追跡テーブルが使用されます。 このため、両方のテーブルの変更内容が同一の追跡テーブルに反映されます。 これにより、同期中にデータの変更が誤って行われます。

#### <a name="resolution"></a>解決策

同期の対象となるテーブルについて、データベース内で別のスキーマに属している場合でも名前が異なっていることを確認してください。

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>同期に成功した後に一貫性のない主キー データが表示される

#### <a name="description-and-symptoms"></a>説明および現象

同期が成功と報告され、失敗またはスキップされた行がログに示されていないにもかかわらず、同期グループ内のデータベース間で主キーのデータに不整合が発生します。

#### <a name="cause"></a>原因

この結果は仕様です。 任意の主キー列に変更が加えられると、主キーが変更された行のデータに不整合が発生します。

#### <a name="resolution"></a>解決策

この問題を回避するには、主キー列のデータが変更されないようにします。

問題を発生後に解決するには、同期グループのすべてのエンドポイントから、データに不整合が発生した行を削除します。 その後、削除した行を挿入し直します。

### <a name="i-see-a-significant-degradation-in-performance"></a>パフォーマンスが大幅に低下する

#### <a name="description-and-symptoms"></a>説明および現象

パフォーマンスが大幅に低下し、データ同期 UI を起動できなくなることもあります。

#### <a name="cause"></a>原因

最も可能性の高い原因は同期ループです。 同期グループ A による同期で同期グループ B による同期がトリガーされ、さらにこの同期で同期グループ A による同期がトリガーされると、同期ループが発生します。実際の状況はさらに複雑であり、ループに同期グループが 3 つ以上含まれる場合もあります。 問題は、同期グループが互いに重複していることが原因で、同期の循環トリガーが発生することです。

#### <a name="resolution"></a>解決策

最善の解決策は予防です。 同期グループに循環参照がないことを確認します。 1 つの同期グループによって同期される行を、別の同期グループによって同期することはできません。

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>"列 \<column\> に値 NULL を挿入できません。 この列では NULL 値が許可されていません" というエラー メッセージが表示されました。 このメッセージの意味と修正方法を教えてください。 
このエラー メッセージは、次の 2 つの問題のいずれかを示しています。
-  テーブルに主キーが設定されていません。 この問題を解決するには、同期しているすべてのテーブルに主キーを追加します。
-  CREATE INDEX ステートメントに WHERE 句があります。 データ同期 (プレビュー) では、この状態は処理されません。 この問題を解決するには、WHERE 句を削除するか、手動ですべてのデータベースに変更を加えます。 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>データ同期 (プレビュー) では循環参照はどのように処理されますか? つまり、同じデータが複数の同期グループで同期されるとき、その変更を結果として保持しますか?
データ同期 (プレビュー) は循環参照を処理しません。 必ず回避してください。 

## <a name="client-agent-issues"></a>クライアント エージェントに関する問題

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>クライアント エージェントのインストール、アンインストール、または修復に失敗する

#### <a name="cause"></a>原因

このエラーはさまざまなシナリオで発生します。 このエラーの具体的な原因を特定するには、ログを調べます。

#### <a name="resolution"></a>解決策

エラーの具体的な原因を特定するには、Windows インストーラー ログを生成し、内容を確認します。 ログ記録はコマンド プロンプトから有効にすることができます。 たとえば、ダウンロードした AgentServiceSetup.msi ファイルが LocalAgentHost.msi である場合は、次のコマンド ラインを使用してログ ファイルを生成し、内容を確認します。

-   インストールの場合: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   アンインストールの場合: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Windows インストーラーによって実行されるすべてのインストールについて、ログ記録を有効にすることもできます。 Microsoft サポート技術情報の記事「[Windows インストーラーのログの記録を有効にする方法](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)」には、Windows インストーラーのログの記録を有効にするワンクリック ソリューションが用意されています。 また、ログの場所も示されています。

### <a name="my-client-agent-doesnt-work"></a>クライアント エージェントが動作しない

#### <a name="description-and-symptoms"></a>説明および現象

クライアント エージェントを使用しようとすると、次のメッセージが表示されます。

"Sync failed with exception (例外で同期に失敗しました) There was an error while trying to deserialize parameter www.microsoft.com/.../05:GetBatchInfoResult. (パラメーター www.microsoft.com/.../05:GetBatchInfoResult を逆シリアル化しようとしているときにエラーが発生しました。) 詳細については、InnerException をご覧ください。

"Inner exception message: Type 'Microsoft.Synchronization.ChangeBatch' is an invalid collection type since it does not have a default constructor. (内部例外メッセージ: タイプ 'Microsoft.Synchronization.ChangeBatch' は、既定のコンストラクターがないため無効なコレクション型です。)"

#### <a name="cause"></a>原因

これは、SQL データ同期 (プレビュー) インストールの既知の問題です。 このメッセージの原因は、次のいずれかであると考えられます。

-   Windows 8 Developer Preview を実行している。
-   .NET Framework 4.5 をインストールしている。

#### <a name="resolution"></a>解決策

Windows 8 Developer Preview が実行されておらず、かつ .NET Framework 4.5 がインストールされていないコンピューターにクライアント エージェントをインストールしてください。

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>アンインストールを取り消した後、クライアント エージェントが動作しない

#### <a name="description-and-symptoms"></a>説明および現象

クライアント エージェントのアンインストールを取り消した後でも、クライアント エージェントが動作しません。

#### <a name="cause"></a>原因

この原因は、SQL データ同期 (プレビュー) クライアント エージェントに資格情報が格納されないことです。

#### <a name="resolution"></a>解決策

次の 2 つの解決策を試してください。

-   services.msc を使用してクライアント エージェントの資格情報を再入力します。
-   今のクライアント エージェントをアンインストールした後、新規にクライアント エージェントをインストールします。 [ダウンロード センター](http://go.microsoft.com/fwlink/?linkid=221479)から最新のクライアント エージェントをダウンロードしてインストールします。

### <a name="my-database-isnt-listed-in-the-agent-list"></a>データベースがエージェントの一覧に表示されない

#### <a name="description-and-symptoms"></a>説明および現象

既存の SQL Server データベースを同期グループに追加しようとしたときに、対象のデータベースがエージェントの一覧に表示されません。

#### <a name="cause"></a>原因

この問題は以下のシナリオで発生します。

-   クライアント エージェントと同期グループが異なるデータセンターにある。
-   クライアント エージェントのデータベースの一覧が最新ではない。

#### <a name="resolution"></a>解決策

解決策は、原因に応じて異なります。

- **クライアント エージェントと同期グループが異なるデータセンターにある**

    クライアント エージェントと同期グループは同じデータセンターに配置する必要があります。 この設定を行うには、2 つの方法があります。

    -   同期グループが配置されているデータセンターに新しいエージェントを作成します。 次に、そのエージェントにデータベースを登録します。
    -   現在の同期グループを削除します。 次に、エージェントが配置されているデータセンターで同期グループを再作成します。

- **クライアント エージェントのデータベースの一覧が最新ではない**

    クライアント エージェント サービスをいったん停止した後、再び開始します。

    関連付けられているデータベースの一覧をローカル エージェントがダウンロードするのは、エージェント キーの最初の送信時のみです。 それ以降のエージェント キーの送信時には、関連付けられているデータベースの一覧はダウンロードされません。 したがって、エージェントの移動中に登録されたデータベースは、元のエージェント インスタンスに表示されません。

### <a name="client-agent-doesnt-start-error-1069"></a>クライアント エージェントが起動しない (エラー 1069)

#### <a name="description-and-symptoms"></a>説明および現象

SQL Server をホストしているコンピューターで、エージェントが実行されていませんでした。 エージェントを手動で起動しようとすると、"エラー 1069: ログオンに失敗したため、サービスを開始できませんでした。" というメッセージを示すダイアログ ボックスが表示されます。

![データ同期エラー 1069 のダイアログ ボックス](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>原因

このエラーの原因としては、エージェントを作成しエージェントのパスワードを指定した後でローカル サーバー上のパスワードが変更されたことが考えられます。

#### <a name="resolution"></a>解決策

エージェントのパスワードを現在のサーバー パスワードに更新します。

1. [SQL Data Sync (Preview) client agent Preview]\(SQL データ同期 (プレビュー) クライアント エージェント プレビュー\) サービスを見つけます。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[開始]** を選択します。  
    b. 検索ボックスに「**services.msc**」と入力します。  
    c. 検索結果で、**[サービス]** を選択します。  
    d. **[サービス]** ウィンドウで、**[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** のエントリまでスクロールします。  
2. **[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** を右クリックして、**[停止]** を選択します。
3. **[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** を右クリックして、**[プロパティ]** を選択します。
4. **[SQL Data Sync (Preview) Agent Preview Properties]\(SQL データ同期 (プレビュー) エージェント プレビューのプロパティ\)** ウィンドウで、**[ログイン]** タブを選択します。
5. **[パスワード]** ボックスにパスワードを入力します。
6. **[パスワードの確認]** ボックスにパスワードをもう一度入力します。
7. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。
8. **[サービス]** ウィンドウで、**[SQL Data Sync (Preview) Agent Preview]\(SQL データ同期 (プレビュー) エージェント プレビュー\)** サービスを右クリックし、**[開始]** をクリックします。
9. **[サービス]** ウィンドウを閉じます。

### <a name="i-cant-submit-the-agent-key"></a>エージェント キーを送信できない

#### <a name="description-and-symptoms"></a>説明および現象

エージェントのキーを作成または再作成した後、そのキーを SqlAzureDataSyncAgent アプリケーションで送信しようとしました。 しかし、送信が完了しませんでした。

![同期エラー ダイアログ ボックス - エージェント キーを送信できない](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

続行する前に、次の条件を確認します。

-   SQL データ同期 (プレビュー) Windows サービスが実行されている。  
-   [SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\) Windows サービスのサービス アカウントがネットワークにアクセスできる。    
-   クライアント エージェントがロケーター サービスに接続できる。 次のレジストリキーの値が https://locator.sync.azure.com/LocatorServiceApi.svc: であることを確認します。  
    -   x86 コンピューターの場合: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   x64 コンピューターの場合: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>原因

各ローカル エージェントは、エージェント キーによって一意に識別されます。 キーは次の 2 つの条件を満たす必要があります。

-   SQL データ同期 (プレビュー) サーバーとローカル コンピューター上のクライアント エージェント キーが同一である必要があります。
-   クライアント エージェント キーは 1 回だけ使用することができます。

#### <a name="resolution"></a>解決策

エージェントが動作していない場合、これらの条件の 1 つまたは両方が満たされていないことが原因です。 再び動作するようにエージェントを設定するには、次の操作を実行します。

1. 新しいキーを生成します。
2. 新しいキーをエージェントに適用します。

新しいキーをエージェントに適用するには、次の手順を実行します。

1. エクスプローラーで、エージェントのインストール ディレクトリに移動します。 既定のインストール ディレクトリは、C:\\Program Files (x86)\\Microsoft SQL Data Sync です。
2. bin サブディレクトリをダブルクリックします。
3. SqlAzureDataSyncAgent アプリケーションを開きます。
4. **[Submit Agent Key]\(エージェント キーの送信\)** を選択します。
5. 表示されたスペースにクリップボードからキーを貼り付けます。
6. **[OK]**を選択します。
7. プログラムを閉じます。

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>関連付けられているオンプレミス データベースにアクセスできない場合、クライアント エージェントをポータルから削除できない

#### <a name="description-and-symptoms"></a>説明および現象

SQL データ同期 (プレビュー) クライアント エージェントに登録されているローカル エンドポイント (つまり、データベース) にアクセスできない場合、クライアント エージェントを削除できません。

#### <a name="cause"></a>原因

アクセスできないデータベースがエージェントに登録されたままであるため、ローカル エージェントを削除することはできません。 エージェントを削除しようとすると、削除プロセスはデータベースにアクセスしようとしますが失敗します。

#### <a name="resolution"></a>解決策

アクセスできないデータベースを削除するには、"強制削除" を使用します。

> [!NOTE]
> "強制削除" の後に同期メタデータ テーブルが残っている場合は、deprovisioningutil.exe を使用してクリーンアップします。

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>ローカル同期エージェント アプリでローカル同期サービスに接続できない

#### <a name="resolution"></a>解決策

次の手順を試してみてください。

1. アプリを終了します。  
2. [コンポーネント サービス] パネルを開きます。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 タスク バーの検索ボックスに「**services.msc**」と入力します。  
    b. 検索結果で、**[サービス]** をダブルクリックします。  
3. **[SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\)** サービスを停止します。
4. **[SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\)** サービスを再開します。  
5. アプリをもう一度開きます。

## <a name="setup-and-maintenance-issues"></a>セットアップとメンテナンスに関する問題

### <a name="i-get-a-disk-out-of-space-message"></a>"disk out of space (ディスク領域不足)" メッセージが表示される

#### <a name="cause"></a>原因

"disk out of space (ディスク領域不足)" メッセージは、未処理のファイルを削除する必要がある場合に表示される可能性があります。 この原因としては、ウイルス対策ソフトウェアや、削除操作を行おうとしたときにファイルが開かれていることなどが考えられます。

#### <a name="resolution"></a>解決策

%temp% フォルダーに含まれる同期ファイルを手動で削除します (`del \*sync\* /s`)。 その後、%temp% フォルダーのサブディレクトリを削除します。

> [!IMPORTANT]
> 同期の進行中はファイルを削除しないでください。

### <a name="i-cant-delete-my-sync-group"></a>同期グループを削除できない

#### <a name="description-and-symptoms"></a>説明および現象

同期グループを削除できません。

#### <a name="causes"></a>原因

以下のシナリオのいずれかに該当する場合、同期グループの削除に失敗する可能性があります。

-   クライアント エージェントがオフラインになっている。
-   クライアント エージェントがアンインストールされているか、見つからない。 
-   データベースがオフラインになっている。 
-   同期グループのプロビジョニング中または同期中である。 

#### <a name="resolution"></a>解決策

同期グループの削除エラーを解決するには、次の手順を実行します。

-   クライアント エージェントがオンラインになっていることを確認してから、再試行します。
-   クライアント エージェントがアンインストールされているか、見つからない場合:  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合は、このファイルを削除します。  
    b. オンプレミスのコンピューター (同じコンピューターでも別のコンピューターでもかまいません) にエージェントをインストールします。 次に、オフラインと表示されているエージェント用にポータルで生成されたエージェント キーを送信します。
-   SQL データ同期 (プレビュー) サービスが実行されていることを確認します。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[スタート]** メニューで、**サービス**を検索します。  
    b. 検索結果で、**[サービス]** を選択します。  
    c. **[SQL Data Sync (Preview) Preview]\(SQL データ同期 (プレビュー) プレビュー\)** サービスを見つけます。  
    d. このサービスの状態が **[停止]** の場合は、サービス名を右クリックして **[開始]** を選択します。
-   SQL データベースと SQL Server データベースがすべてオンラインになっていることを確認します。
-   プロビジョニング プロセスまたは同期プロセスが完了するまで待ってから、同期グループをもう一度削除します。

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>オンプレミスの SQL Server データベースの登録を解除できない

#### <a name="cause"></a>原因

既に削除されているデータベースの登録を解除しようとしていると考えられます。

#### <a name="resolution"></a>解決策

オンプレミスの SQL Server データベースの登録を解除するには、データベースを選択し、**[Force Delete]\(強制削除\)** を選択します。

この操作を実行しても同期グループからデータベースを削除できない場合は、次の手順を実行します。

1. クライアント エージェント ホスト サービスをいったん停止した後、再び開始します。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[スタート]** メニューを選択します。  
    b. 検索ボックスに「**services.msc**」と入力します。  
    c. 検索結果ウィンドウの **[プログラム]** セクションで、**[サービス]** をダブルクリックします。  
    d. **[SQL Data Sync (Preview)]\(SQL データ同期 (プレビュー)\)** サービスを右クリックします。  
    e. サービスが実行中の場合は、停止します。  
    f. サービスを右クリックして、**[開始]** を選択します。  
    g. データベースがまだ登録されているかどうかを確認します。 登録が解除されている場合は、作業は終了です。 それ以外の場合は、次の手順に進みます。
2. クライアント エージェント アプリ (SqlAzureDataSyncAgent) を開きます。
3. **[資格情報の編集]** を選択し、データベースの資格情報を入力します。
4. 登録の解除に進みます。

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>システム サービスを開始するのに十分な権限がない

#### <a name="cause"></a>原因

このエラーは、次の 2 つの状況で発生します。
-   ユーザー名やパスワードが正しくない。
-   指定されたユーザー アカウントに、サービスとしてログオンするための十分な権限がない。

#### <a name="resolution"></a>解決策

"サービスとしてログオン" 資格情報をユーザー アカウントに付与します。

1. **[スタート]** > **[コントロール パネル]** > **[管理ツール]** > **[ローカル セキュリティ ポリシー]** > **[ローカル ポリシー]** > **[User Rights Management]\(ユーザー権限の管理\)** の順に選択します。
2. **[サービスとしてログオン]** を選択します。
3. **[プロパティ]** ダイアログ ボックスでこのユーザー アカウントを追加します。
4. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。
5. すべてのウィンドウを閉じます。

### <a name="a-database-has-an-out-of-date-status"></a>データベースの状態が [Out-of-Date]\(最新ではありません\) になっている

#### <a name="cause"></a>原因

SQL データ同期 (プレビュー) では、45 日以上サービスでオフライン状態となっているデータベースは除外されます (データベースがオフラインになったときからカウントされます)。 オフライン状態が 45 日以上続いていたデータベースがオンラインに戻った場合、状態は **[Out-of-Date]\(最新ではありません\)** になります。

#### <a name="resolution"></a>解決策

**[Out-of-Date]\(最新ではありません\)** 状態を回避するには、データベースが 45 日以上オフラインにならないようにします。

データベースの状態が **[Out-of-Date]\(最新ではありません\)** の場合、次の手順を実行します。

1. 状態が **[Out-of-Date]\(最新ではありません\)** となっているデータベースを同期グループから削除します。
2. そのデータベースを同期グループに再び追加します。

> [!WARNING]
> このデータベースがオフラインの間に加えた変更はすべて失われます。

### <a name="a-sync-group-has-an-out-of-date-status"></a>同期グループの状態が [Out-of-Date]\(最新ではありません\) になっている

#### <a name="cause"></a>原因

45 日間のリテンション期間全体にわたって 1 つまたは複数の変更を適用できなかった場合、同期グループは最新とは言えない可能性があります。

#### <a name="resolution"></a>解決策

同期グループの **[Out-of-Date]\(最新ではありません\)** 状態を回避するには、履歴ビューアーで同期ジョブの結果を定期的に調べます。 適用に失敗した変更を調査して解決します。

同期グループの状態が **[Out-of-Date]\(最新ではありません\)** の場合は、同期グループを削除してから再度作成します。

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>エージェントのアンインストール/停止後 3 分以内に同期グループを削除できない

#### <a name="description-and-symptoms"></a>説明および現象

関連付けられている SQL データ同期 (プレビュー) クライアント エージェントをアンインストールまたは停止してから 3 分以内に同期グループを削除することはできません。

#### <a name="resolution"></a>解決策

1. 関連付けられている同期エージェントがオンラインのときに同期グループを削除します (推奨)。
2. エージェントがオフラインになっているもののインストールはされている場合は、オンプレミスのコンピューターでオンラインにします。 次に、SQL データ同期 (プレビュー) ポータルでエージェントの状態が **[Online]\(オンライン\)** と表示されるまで待機します。 その後、同期グループを削除します。
3. エージェントがアンインストールされたためにオフラインになっている場合は、次の手順を実行します。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。  SQL データ同期 (プレビュー) のインストール フォルダーにエージェント XML ファイルがある場合は、このファイルを削除します。  
    b.  オンプレミスのコンピューター (同じコンピューターでも別のコンピューターでもかまいません) にエージェントをインストールします。 次に、オフラインと表示されているエージェント用にポータルで生成されたエージェント キーを送信します。  
    c. 同期グループを削除します。

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>紛失または破損したデータベースを復元するとどうなりますか?

紛失または破損したデータベースをバックアップから復元した場合、データベースが属している同期グループのデータが収束しない可能性があります。

## <a name="next-steps"></a>次の手順
SQL データ同期 (プレビュー) の詳細については、以下をご覧ください。

-   [Azure SQL データ同期 (プレビュー) を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-database-sync-data.md)  
-   [Azure SQL データ同期 (プレビュー) のセットアップ](sql-database-get-started-sql-data-sync.md)  
-   [Azure SQL データ同期 (プレビュー) のベスト プラクティス](sql-database-best-practices-data-sync.md)  
-   [OMS Log Analytics を使用した Microsoft Azure SQL データ同期 (プレビュー) の監視](sql-database-sync-monitor-oms.md)  
-   SQL データ同期 (プレビュー) の構成方法を示した完全な PowerShell の例:  
    -   [PowerShell を使用した複数の Azure SQL データベース間の同期](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [SQL データ同期 (プレビュー) の REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database の詳細については、以下をご覧ください。

-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
