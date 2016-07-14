<properties
	pageTitle="Azure AD Connect 同期: Synchronization Service Manager UI | Microsoft Azure"
	description="Azure AD Connect の Synchronization Service Manager の [コネクタ] タブについて"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>


# Azure AD Connect 同期: Synchronization Service Manager

[操作](active-directory-aadconnectsync-service-manager-ui-operations.md) | [コネクタ](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [メタバース デザイナー](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [メタバース検索](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

[コネクタ] タブを利用し、同期エンジンが接続されているすべてのシステムを管理します。

## コネクタのアクション

アクション | Comment (コメント)
--- | ---
作成 | 使用しないでください。その他の AD フォレストに接続するには、インストール ウィザードを使用します。
プロパティ | ドメインと OU フィルター処理に使用されます。
[削除](#delete) | コネクタ スペースのデータを削除するか、フォレストへの接続を削除するために使用されます。
[実行プロファイルの構成](#configure-run-profiles) | ドメイン フィルター処理を除き、ここで構成するものはありません。既に構成されている実行プロファイルを確認する場合に、これを使用します。
実行 | プロファイルの 1 回限りの実行を開始するために使用されます。
Stop | 現在プロファイルを実行しているコネクタを停止します。
コネクタのエクスポート | 使用しないでください。
コネクタのインポート | 使用しないでください。
コネクタの更新 | 使用しないでください。
スキーマの更新 | キャッシュされたスキーマを更新します。同期ルールも更新されるため、インストール ウィザードのオプションを代わりに利用することが推奨されます。
[コネクタ スペースの検索](#search-connector-space) | オブジェクトを検索し、[オブジェクトとそのデータをシステム全体でフォローする](#follow-an-object-and-its-data-through-the-system)ために使用されます。

### 削除
削除アクションには次の 2 つがあります。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

**[Delete connector space only]** (コネクタ スペースのみを削除する) オプションを選択すると、すべてのデータが削除されますが、構成はすべて維持されます。

**[Delete Connector and connector space]** (コネクタとコネクタ スペースを削除する) オプションを選択すると、すべてのデータと構成が削除されます。これは、フォレストに今後接続しない場合に使用されます。

いずれのオプションでも、すべてのオブジェクトが同期され、メタバース オブジェクトが更新されます。これは実行時間の長い操作となります。

### 実行プロファイルの構成
このオプションを使用すると、コネクタ用に構成された実行プロファイルを表示できます。

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### コネクタ スペースの検索
コネクタ スペースの検索アクションは、オブジェクトを検索し、データ問題を解決するのに役立ちます。

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

まず、**[scope]** (範囲) を選択します。データ (RDN、DN、アンカー、サブツリー) またはオブジェクトの状態 (その他すべてのオプション) に基づいて検索できます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
たとえば、サブツリー検索を行うと、1 つの OU のすべてのオブジェクトが取得されます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)
ここからオブジェクトを選択し、**[properties]** (プロパティ) を選択して、ソース コネクタ スペースからメタバースを経てターゲット コネクタ スペースまで[フォロー](#follow-an-object-and-its-data-through-the-system)できます。

## オブジェクトとそのデータをシステム全体でフォローする
データの問題を解決するとき、ソース コネクタ スペースから、メタバースを経由し、ターゲット コネクタ スペースまでオブジェクトをフォローすることがデータの値が予想と異なる理由を理解するための鍵となります。

### コネクタ スペース オブジェクトのプロパティ
**インポート**  
cs オブジェクトを開くと、いくつかのタブが上部に表示されます。**[Import]** (インポート) タブには、インポート後にステージングされるデータが表示されます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png)
**[Old Value]** (古い値) にはシステムに現在保存されているデータが、**[New Value]** (新しい値) にはソース システムから受け取り、まだ適用されていないデータが表示されます。この場合、同期エラーがあるため、変更は適用できません。

**エラー**  
エラー ページは、オブジェクトに問題がある場合にのみ表示されます。[同期エラーを解決する](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab)方法については、操作ページの詳細を参照してください。

**系列**  
[Lineage]\(系列) タブには、コネクタ スペース オブジェクトとメタバース オブジェクトの関係が表示されます。接続されているシステムから変更を最後にインポートしたタイミングとメタバースにデータを入力するために適用されたルールを確認できます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png)
**[Action]** (アクション) 列に、アクションが **[Provision]** (プロビジョニング) である **[Inbound]** (受信) 同期ルールが 1 つ表示されています。これは、このコネクタ スペース オブジェクトが存在する限り、メタバース オブジェクトが残ることを示します。同期ルールの一覧に **[Outbound]** (送信) と **[Provision]** (プロビジョニング) が表示されている場合は、メタバース オブジェクトが削除されたときに、このオブジェクトが削除されることを示しています。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png)
また、**[PasswordSync]** 列を見ると、1 つの同期ルールの値が **[True]** に設定されており、受信コネクタ スペースに起因してパスワードが変更される可能性があることがわかります。このパスワードはその後、受信ルールを経由して Azure AD に送信されます。

[Lineage]\(系列) タブで [[Metaverse Object Properties]](#metaverse-object-properties) (メタバース オブジェクトのプロパティ) をクリックすると、メタバースに移動できます。

すべてのタブの一番下に **[Preview]** (プレビュー) ボタンと **[Log]** (ログ) ボタンがあります。

**プレビュー**  
[Preview]\(プレビュー) ページは、1 つのオブジェクトの同期に使用されます。顧客の同期ルールの問題を解決しているとき、1 つのオブジェクトに与える変更の影響を確認するのに便利です。**[Full Sync]** (完全同期) か**[Delta sync]** (差分同期) を選択できます。**[Generate Preview]** (プレビューの生成) か **[Commit Preview]** (プレビューのコミット) かも選択できます。生成の場合、変更はメモリにのみ保存されます。コミットの場合、すべての変更がターゲット コネクタ スペースにステージングされます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png)
オブジェクトを調べ、特定の属性フローに適用されたルールを確認できます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**ログ**  
[Log]\(ログ) ページは、パスワードの同期状態と履歴を確認するために使用されます。詳細については、「[パスワード同期のトラブルシューティング](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)」を参照してください。

### メタバース オブジェクトのプロパティ
**属性**  
[Attributes]\(属性) タブには、値と、値を提供したコネクタが表示されます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**コネクタ**  
[Connectors]\(コネクタ) タブには、オブジェクトを表すすべてのコネクタ スペースが表示されます。
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png)
このタブから[コネクタ スペース オブジェクト](#connector-space-object-properties)に移動することもできます。

## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0629_2016-->

