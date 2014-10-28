<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId=""  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg"></tags>

# SQL Database 監査の使用

Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure Storage アカウントの監査ログに書き込みます監査は、Basic、Standard、および Premium の各サービス層でプレビューとして使用できます。監査を使用するには、[プレビューにサインアップ][プレビューにサインアップ]してください。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。標準準拠をサポートする Azure プログラムの詳細については、「[Azure Trust Center (Azure トラスト センター)][Azure Trust Center (Azure トラスト センター)]」を参照してください。

-   [Azure SQL Database の監査の基本][Azure SQL Database の監査の基本]
-   [データベースに対する監査を設定する][データベースに対する監査を設定する]
-   [監査ログとレポートを分析する][監査ログとレポートを分析する]

## <span id="subheading-1"></span>Azure SQL Database の監査の基本</a>

Azure プレビュー ポータルで監査を設定しますが、データベースの作成に Azure ポータルを使用した場合でも Azure プレビュー ポータルを使用した場合でも違いはありません。SQL Database 監査により、以下のことが可能になります。

-   選択したイベントの監査証跡の**保持**。ログに記録するデータベース活動とイベントのカテゴリを定義します。
-   データベース活動の**レポート**。事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
-   レポートの**分析**。疑わしいイベント、異常な活動、および傾向を見つけます。

次の活動とイベントを監査できます。

-   **データへのアクセス**
-   **スキーマの変更 (DDL)**
-   **データの変更 (DML)**
-   **アカウント、ロール、アクセス許可 (DCL)**
-   **セキュリティ例外**

ログに記録された活動やイベントの詳細については、「[Audit Log Format Reference (doc file download) (監査ログ形式のリファレンス (doc ファイルのダウンロード)][Audit Log Format Reference (doc file download) (監査ログ形式のリファレンス (doc ファイルのダウンロード)]」を参照してください。

監査ログが保存されるストレージ アカウントも選択します。

### セキュリティが有効な接続文字列

監査を設定すると、Azure はデータベースに対しセキュリティが有効な接続文字列を提供します。この接続文字列を使用するクライアント アプリケーションだけが、活動とイベントをログに記録できます。そのため、既存のクライアント アプリケーションが新しい文字列形式を使用するように更新する必要があります。

従来の接続文字列形式: \<*サーバー名*\>.database.windows.net

セキュリティが有効な接続文字列: \<*サーバー名*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>データベースに対する監査を設定する

1.  [監査プレビューにサインアップ][プレビューにサインアップ]します。
2.  また、必ず[新しいサービス層のプレビューにもサインアップ][新しいサービス層のプレビューにもサインアップ]して、Basic、Standard、または Premium のデータベースを作成します。
3.  <https://portal.azure.com> で [Azure プレビュー ポータル][Azure プレビュー ポータル]を起動します。
4.  監査するデータベースをクリックし、**[監査プレビュー]** をクリックして、監査プレビューを有効にし、監査構成ブレードを起動します。

    ![][]

5.  監査構成ブレードで、ログが保存される Azure ストレージ アカウントを選択します。**ヒント:** すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。

    ![][1]

6.  **[監査オプション]** で、**[すべて]** をクリックしてすべてのイベントをログに記録するか、または個別のイベントの種類を選択します。

    ![][2]

7.  これらの設定を、今後のサーバーのすべてのデータベース、およびまだ監査を設定していないデータベースに適用する場合は、**[この構成を既定値として保存する]** をクリックします。同じ手順によって、後でデータベースごとにこの設定をオーバーライドできます。

    ![][3]

8.  **[データベース接続文字列を表示する]** をクリックしてから、アプリケーションに対して該当するセキュリティが有効な接続文字列をコピーするか、メモします。活動を監査するすべてのクライアント アプリケーションに対して、この文字列を使用します。

    ![][4]

9.  **[OK]** をクリックします。

## <span id="subheading-3"></span>監査ログとレポートを分析する</a>

監査ログは、設定時に選択した Azure ストレージ アカウントで **AuditLogs** という名前の単一の Azure ストア テーブルに集計されます。[Azure ストレージ エクスプローラー][Azure ストレージ エクスプローラー]などのツールを使用してログ ファイルを表示できます。

事前に構成されたダッシュボード レポート テンプレートが[ダウンロードできる Excel スプレッドシート][ダウンロードできる Excel スプレッドシート]として用意されているので、ログ データをすぐに分析できます。監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です ([ここ][ここ]でダウンロードできます)。

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。

レポート テンプレートの使用手順の詳細については、「[How To (doc download) (使用方法 (doc のダウンロード))][How To (doc download) (使用方法 (doc のダウンロード))]」を参照してください。

![][5]



  [プレビューにサインアップ]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure Trust Center (Azure トラスト センター)]: http://azure.microsoft.com/ja-jp/support/trust-center/compliance/
  [Azure SQL Database の監査の基本]: #subheading-1
  [データベースに対する監査を設定する]: #subheading-2
  [監査ログとレポートを分析する]: #subheading-3
  [Audit Log Format Reference (doc file download) (監査ログ形式のリファレンス (doc ファイルのダウンロード)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [新しいサービス層のプレビューにもサインアップ]: https://account.windowsazure.com/PreviewFeatures?fid=premiumdb
  [Azure プレビュー ポータル]: https://portal.azure.com
  []: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure ストレージ エクスプローラー]: http://azurestorageexplorer.codeplex.com/
  [ダウンロードできる Excel スプレッドシート]: http://go.microsoft.com/fwlink/?LinkId=403540
  [ここ]: http://www.microsoft.com/ja-jp/download/details.aspx?id=39379
  [How To (doc download) (使用方法 (doc のダウンロード))]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
