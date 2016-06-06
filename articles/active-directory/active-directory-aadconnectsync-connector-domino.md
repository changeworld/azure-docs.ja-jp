<properties
   pageTitle="Azure AD Connect Sync: Lotus Domino Connector | Microsoft Azure"
   description="この記事では、Microsoft の Lotus Domino Connector を構成する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Lotus Domino Connector のテクニカル リファレンス

この記事では Lotus Domino Connector について説明します。この記事は次の製品に適用されます。

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   4\.1.3671.0 以降の修正プログラム [KB3092178](https://support.microsoft.com/kb/3092178) を使用する必要があります。

MIM2016 および FIM2010R2 の場合、コネクタは [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)からダウンロードして入手できます。

## Lotus Domino Connector の概要

Lotus Domino Connector を使用すると、IBM の Lotus Domino サーバーと同期サービスを統合することができます。

包括的な観点から見た場合、コネクタの現在のリリースでサポートされている機能は次のとおりです。

機能 | サポート
--- | ---
接続先のデータ ソース | サーバー: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>クライアント:<li>Lotus Notes 9.x</li>
シナリオ | <li>オブジェクトのライフサイクル管理</li><li>グループ管理</li><li>パスワード管理</li>
操作 | <li>完全および差分インポート</li><li>エクスポート</li><li>HTTP パスワードにおけるパスワードの設定と変更</li>
スキーマ | <li>Person (ローミング ユーザー、連絡先 (証明書のない Person))</li><li>グループ</li><li>リソース (リソース、ルーム、オンライン会議)</li><li>メールイン データベース</li><li>サポートされているオブジェクトの属性の動的検出</li>

Lotus Domino Connector は、Lotus Domino サーバーとの通信に、Lotus Notes クライアントを使用します。そのため、同期サーバーサポートされている Lotus Notes クライアントをインストールする必要があります。クライアントとサーバー間の通信は、Lotus Notes .NET Interop (Interop.domino.dll) インターフェイスを介して実装されています。このインターフェイスにより、Microsoft.NET プラットフォームと Lotus Notes 間の通信は促され、Lotus Domino ドキュメントおよびビューにアクセスできるようになります。(選択している差分インポートの方法に応じて) 差分インポートで C++ ネイティブ インターフェイスを使用することも可能です。

### 前提条件

コネクタを使用する前に、前述した修正プログラムに加えて次のものが同期サーバーにインストールされていることを確認してください。

- Microsoft .NET 4.5.2 Framework 以降
- 同期サーバーへの Lotus Notes クライアントのインストール
- Lotus Domino Connector では、Domino Directory サーバー上に既定の Lotus Domino LDAP スキーマ データベース (schema.nsf) が必要です。存在することを確認してください。存在しない場合、Domino サーバーで LDAP サービスを実行または再起動して、インストールします。

### 接続先のデータ ソースのアクセス権

Lotus Domino Connector でサポートされている任意のタスクを実行するには、次のグループのメンバーである必要があります。

- フル アクセスを持つ管理者
- 管理者
- データベース管理者

次の表に、各操作に必要なアクセス権を示します。

操作 | アクセス権
--- | ---
インポート | <li>公開されているドキュメントの読み取り</li><li> フル アクセスを持つ管理者 (フル アクセスを持つ管理者グループのメンバーである場合、ACL には実質的に自動的にアクセスできます)。</li>
エクスポートおよびパスワードの設定 | 実質的なアクセス: <li>ドキュメントの作成</li><li>ドキュメントの削除</li><li>公開されているドキュメントの読み取り</li><li>公開されているドキュメントの書き込み</li><li>ドキュメントの複製またはコピー</li>エクスポート操作には、上記のアクセス権のみでなく、次のロールも割り当てられている必要があります。<li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### 直接の操作および AdminP

操作は、Domino ディレクトリに直接実行されるか、AdminP プロセスを介して実行されます。次にサポートされているすべてのオブジェクト、操作、そして存在する場合は、関連する実装方法を示します。

**プライマリ アドレス帳**

オブジェクト | 作成 | 更新 | 削除
--- | --- | --- | ---
Person | AdminP | 直接 | AdminP
グループ | AdminP | 直接 | AdminP
MailInDB | 直接 | 直接 | 直接
リソース | AdminP | 直接 | AdminP

**セカンダリ アドレス帳**

オブジェクト | 作成 | 更新 | 削除
--- | --- | --- | ---
Person | 該当なし | 直接 | 直接
グループ | 直接 | 直接 | 直接
MailInDB | 直接 | 直接 | 直接
リソース | 該当なし | 該当なし | 該当なし

リソースが作成されると、Notes ドキュメントが作成されます。同様にリソースが削除されると、Notes ドキュメントが削除されます。

### ポートとプロトコル

IBM Lotus Notes クライアントおよび Domino サーバーは、Notes Remote Procedure Call (NRPC) を使用して通信します。このとき、NRPC では TCP/IP が使用される必要があります。既定のポート番号は 1352 ですが、これは Domino 管理者が変更することができます。

### サポートされていません

次の操作は、Lotus Domino Connector の現在のリリースではサポートされていません。

- サーバー間でのメールボックスの移動。

## 新しいコネクタの作成

### クライアント ソフトウェアのインストールおよび構成

Lotus Notes は、コネクタのインストール**前に**、サーバーにインストールする必要があります。

インストールする際には、必ず **[Single User Install]** を実行してください。既定の **[Multi-User Install]** は機能しません。

![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

機能のページでは、必要な Lotus Notes 機能および **[Client Single Logon]** のみをインストールします。コネクタが Domino サーバーにログオンするには、シングル ログオンが必要です。

![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**注:** Lotus Notes は、一度はコネクタのサービス アカウントとして使用するアカウントと同じサーバーにあるユーザーが開始する必要があります。また、サーバー上の Lotus Notes クライアントは必ず終了してください。クライアントが実行されているときに、コネクタが Domino サーバーに接続することはできません。

### コネクタの作成

Lotus Domino Connector を作成するには、**Synchronization Service** で **[管理エージェント]** を選択し、**[作成]** を選択します。**[Lotus Domino (Microsoft)]** のコネクタを選択します。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

使用している同期サービスのバージョンで**アーキテクチャ**を設定できる場合、**プロセス**で実行できるようコネクタがその既定に設定されていることを確認します。

### 接続

[接続] ページで、Lotus Domino サーバー名を指定し、ログオン資格証明を入力します。

![接続](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Domino Server のプロパティでは、サーバー名に次の 2 つの形式がサポートされています。

- ServerName
- ServerName/DirectoryName

コネクタと Domino Server が通信する際、応答速度が速いため、この属性には **ServerName/DirectoryName** の形式が推奨されます。

提供された UserID ファイルは、同期サービスの構成データベースに格納されます。

**差分インポート**には、次のオプションがあります。

- **[なし]**。コネクタは差分インポートを実行しません。
- **[追加/更新]**。コネクタは、追加および更新操作を差分インポートします。削除については、**[フル インポート]** 操作が必要です。この操作では、Notes の .Net interop が使用されます。
- **[追加/更新/削除]**。コネクタは、追加、更新および削除操作を差分インポートします。この操作では、Notes のネイティブ C++ インターフェイスが使用されます。

**[スキーマ オプション]** には次のオプションがあります。

- **[既定のスキーマ]**。既定のオプションで、コネクタは Domino サーバーからスキーマを検出します。
- **[DSML スキーマ]**。Domino サーバーがスキーマを公開しない場合のみ使用されます。この場合、スキーマを使用して作成した DSML ファイルを代わりにインポートします。DSML の詳細については、「[OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml)」を参照してください。

[次へ] をクリックすると UserID とパスワード構成のパラメーターが検証されます。

### グローバル パラメーター

[グローバル パラメーター] ページでは、タイム ゾーンと、インポートおよびエクスポート操作のオプションを構成します。

![グローバル パラメーター](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

**[Domino サーバーのタイム ゾーン]** パラメーターでは Domino サーバーの場所を定義します。

この構成オプションは、最後の 2 回のインポート間の変更を同期サービスが特定するのを可能にするため、**差分インポート**操作で必要です。

#### インポート設定および方法

**[フル インポートの実行方法]** には次のオプションがあります。

- 検索
- ビュー (推奨)

**[検索]** は Domino のインデックスを使用しますが、一般にインデックスはリアルタイムで更新されず、サーバーから返されるデータが常に正しいとは限りません。このオプションは、変更が多いシステムではうまく機能しないことが多く、場合によっては削除が不正に行われることがあります。ただし、**[検索]** の方が **[ビュー]** よりも高速です。

データの正しい状態を示す **[ビュー]** オプションが推奨されます。これは [検索] より若干動作が遅くなります。

#### Virtual Contact オブジェクトの作成

**[\_Contact オブジェクトの作成]** には、次のオプションがあります。

- なし
- 非参照値
- 参照および非参照値

Domino の参照属性には、その他のオブジェクトを参照するさまざまな形式があります。コネクタには、さまざまなバリエーションを表現する Virtual Contacts (VC) とも呼ばれる \_Contact オブジェクトが実装されています。これらのオブジェクトは、既存の MV オブジェクトに結合して、新しいオブジェクトとしてプロジェクションし、この方法で参照属性を保持できるよう作成されています。

この設定を有効にし、参照属性の内容が DN 形式でない場合、\_Contact オブジェクトが作成されます。たとえば、グループのメンバー属性に SMTP アドレスを含めることができます。また、参照属性に shortName およびその他のオブジェクトを含めることができます。このシナリオでは、**[非参照値]** を選択します。これが Domino の実装で最も一般的な設定です。

Lotus Domino に同じオブジェクトを表す異なる識別名のアドレス帳がある場合も、アドレス帳のすべての参照値用に \_Contact オブジェクトを作成することができます。このシナリオでは、**[参照および非参照値]** オプションを選択します。

Domino の属性 **FullName** に複数の値がある場合、参照が解決されるように Virtual Contacts の作成も有効にします。この属性は、たとえば結婚や離婚の後に複数の値を持つことが可能です。このシナリオのために、チェックボックス **[Enable ...FullName has multiple values]** をオンにします。

正しい属性を結合させることにより、\_Contact オブジェクトは MV オブジェクトに結合されます。

これらのオブジェクトの DN には、VC=\_Contact が追加されます。

#### インポート設定および競合オブジェクト

**競合するオブジェクトの除外**

大規模な Domino の実装では、レプリケーションの問題により、複数のオブジェクトの DN が同じである場合があります。このような場合、コネクタには UniversalID は同じですが DN は異なる 2 つのオブジェクトが示されます。これにより、コネクタ スペースに一時オブジェクトが作成されます。コネクタは、Domino で選択したオブジェクトは、レプリケーション上作成されたものとして無視できます。このチェックボックスはオンにすることをお勧めします。

#### 設定のエクスポート

**[参照の更新に AdminP を使用する]** オプションがオンになっていない場合、メンバーなどの参照属性のエクスポートは、AdminP プロセスは使用しない、直接での呼び出しになります。これは、参照整合性を維持するために AdminP が構成されていない場合のみ使用します。

#### ルーティング情報

Domino の参照属性には、DN へのルーティング情報をサフィックスとして埋め込むことが可能です。たとえば、グループの member 属性に **CN=example/organization@ABC** を含めることが可能です。サフィックス @ABC がルーティング情報です。このルーティング情報は、Domino が (別の組織のシステムである場合もある) 正しい Domino システムに電子メールを送信する際に使用します。[ルーティング情報] フィールドには、コネクタのスコープで組織が使用しているルーティング サフィックスを指定します。これらのいずれかの値が参照属性のサフィックスとして検出される場合、参照からルーティング情報は削除され、コネクタ スペースのオブジェクトの DN と一致するようになります。参照値のルーティング サフィックスが指定したものの 1 つと一致しない場合、\_Contact オブジェクトが作成されます。これらの \_Contact オブジェクトは **RO=@<RoutingSuffix>** が DN に挿入され、作成されます。これらの \_Contact オブジェクトには、必要に応じて実際のオブジェクトと結合できるようにする \_routingName、\_contactName、\_displayName、UniversalID のオブジェクトも追加されます。

#### その他のアドレス帳

セカンダリ アドレス帳の名前を提供する **Directory Assistance** がインストールされていない場合、これらのアドレス帳を手動で入力できます。

#### 複数値の変換

Lotus Domino の属性の多くには値が複数あります。対応するメタバース属性の値は、通常 1 つです。インポートおよびエクスポート操作オプションを構成すると、影響を受ける属性の変換をコネクタが支援するようになり、構成が簡単になります。

**エクスポート**

エクスポート操作には、次の 2 つのモードがあります。

- 項目の追加
- 項目の置換

**項目の置換**: このオプションを選択すると、コネクタは常に Domino の属性の現在の値を削除し、指定された値に置き換えます。指定する値は、1 つでも複数でも可能です。

例: Person オブジェクトの Assistant 属性値は次のとおりです。

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**David Alexander** という名前の Assistant がこの Person オブジェクトに割り当てられる場合、次のような結果になります。

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf

**項目の置換**: このオプションを選択すると、コネクタは Domino の属性の既存の値を保持し、データ リストの上部に新しい値を挿入します。

例: Person オブジェクトの Assistant 属性値は次のとおりです。

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**David Alexander** という名前の Assistant がこの Person オブジェクトに割り当てられる場合、次のような結果になります。

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**インポート**

インポート操作オプションでは、次の 2 つのモードがサポートされています。

- 既定値
- 複数値から単一値

**既定**: 既定のオプションを選択すると、すべての属性のすべての値がインポートされます。

**複数値から単一値**: このオプションを選択すると、複数値の属性は単一値の属性に変換されます。値が 1 つ以上存在する場合、上にある値 (通常これが最新の値です) が使用されます。

例:

Person オブジェクトの Assistant 属性値は以下のとおりです。

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

この属性に対する最新の更新は **David Alexander** です。[インポート操作] オプションは、[複数値から単一値] に設定されているので、コネクタはコネクタ スペースに **David Alexander** のみをインポートします。

複数の値を持つ属性を単一値の属性に変換するロジックは、group オブジェクトの member 属性および Person オブジェクトの fullname 属性には使用できません。

グローバル規則に、例外として、複数の値を持つ属性に属性ごとのインポートおよびエクスポート変換規則を構成することも可能です。このオプションを構成するには、[objecttype].[attributename] を **[インポートしない属性の一覧]** と **[エクスポートしない属性の一覧]** テキスト ボックスに入力します。たとえば、すべての値をインポートするようにグローバル フラグが設定されている場合に Person.Assistant と入力したとき、最初の値のみがアシスタント用にインポートされます。

#### 証明者

すべての組織および組織単位が一覧表示されています。プライマリ アドレス帳に Person オブジェクトをエクスポートするには、証明者とそのパスワードが必要です。

すべての証明者のパスワードが同一である場合、**[すべての証明者用パスワード]** を使用できます。その後、ここにパスワードを入力し、証明者ファイルのみを指定します。

インポートのみを行う場合、証明者は指定する必要がありません。

### プロビジョニング階層の構成

Lotus Domino Connector を構成する場合は、このダイアログ ページをスキップできます。これは、Lotus Domino Connector が階層のプロビジョニングをサポートしていないためです。

![プロビジョニング階層](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### パーティションと階層の構成

パーティションおよび階層を構成する場合、NAB=names.nsf というプライマリ アドレス帳を選択する必要があります。

存在する場合は、プライマリ アドレス帳のみでなく、セカンダリ アドレス帳を選択することも可能です。

![パーティション](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### 属性の選択

属性を構成する場合、**\_MMS\_** のプレフィックスが付いているすべての属性を選択する必要があります。これらの属性は、Lotus Domino に新しいオブジェクトをプロビジョニングする際に必要です。

![属性](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## オブジェクトのライフサイクル管理

このセクションでは、Domino のさまざまなオブジェクトの概要を説明します。

### Person オブジェクト

Person オブジェクトとは、組織と組織単位内のユーザーです。Domino 管理者は、既定の属性以外にカスタム属性を Person オブジェクトに追加できます。Person オブジェクトには、すべての必須属性を最低でも含んでいる必要があります。必須属性の一覧については、「[Lotus Notes プロパティ](#lotus-notes-properties)」を参照してください。Person オブジェクトを登録する場合、次の前提条件を満たしている必要があります。

- アドレス帳 (names.nsf) が定義されており、それがプライマリ アドレス帳になっています。
- 組織および組織単位に特定のユーザーを登録するには、組織および組織単位の証明者 ID とパスワードが必要です。
- Person オブジェクトには、特定の Lotus Notes のプロパティ セットを定義する必要があります。これらは、Person オブジェクトのプロビジョニングで使用されます。詳細については、このドキュメントの後の「[Lotus Notes プロパティ](#lotus-notes-properties)」セクションを参照してください。
- Person 用の初期 HTTP パスワードは属性で、プロビジョニング時に設定されます。
- Person オブジェクトは、次の 3 つのサポートされている種類のいずれかである必要があります。
    1. メール ファイルおよびユーザー ID ファイルを持つ標準ユーザー
    2. ローミング ユーザー (すべてのローミング データベース ファイルを含む標準ユーザー)
    3. 連絡先 (ID ファイルを持たないユーザー)

(連絡先を除き) Person は、\_MMS\_IDRegType プロパティの値で定義する米国ユーザーと海外ユーザーにさらにグループ化できます。これらは、Lotus Domino サーバー、データベースへのアクセスに Notes Client を使用する、Notes ID と Person ドキュメントを持つユーザーです。Notes メールを使用している場合、メール ファイルも持っています。ユーザーは、登録済みでないとアクティブにはなりません。詳細情報

- [Notes ユーザーの設定](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [ユーザーの登録](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [ユーザーの管理](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [ユーザーの名前変更](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

これらのすべての操作は Lotus Domino で実行された後に、同期サービスにインポートされます。

### リソースおよびルーム

リソースは Lotus Domino のもう 1 種類のデータベースです。リソースは、プロジェクターなどのさまざまな種類の機器がある会議室である場合があります。リソースには、リソースの種類の属性に基づいて定義される Lotus Domino Connector によってサポートされるそのサブタイプがあります。

リソースの種類 | リソースの種類の属性
--- | ---
ルーム | 1
リソース (その他) | 2
オンライン会議 | 3

リソース オブジェクトの種類が機能するには、次が必要です。

- Resource Reservation データベースが接続されている Domino サーバーに存在します。
- リソース用にサイトが定義済みです。

Resource Reservation データベースには、3 種類のドキュメントが含まれています。

- サイト プロファイル
- リソース
- 予約

Resource Reservation データベースの設定方法の詳細については、「[Resource Reservation データベースを設定する](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html)」を参照してください。

**リソースの作成、更新、および削除**

作成、更新、および削除操作は、Resource Reservation データベース内で Lotus Domino コネクタによって実行されます。この結果、リソースは Names.nsf (つまり、プライマリ アドレス帳) にドキュメントとして作成されます。リソースの編集および削除の詳細については、「[リソース ドキュメントの編集および削除](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html)」を参照してください。

**リソースのインポートおよびエクスポート操作**

リソースは、他の種類のオブジェクトと同様に、同期サービスからインポートおよびエクスポートできます。オブジェクトの種類は、構成時にリソースとして選択する必要があります。正しくエクスポートするには、リソースの種類、会議データベース、およびサイト名の詳細が必要です。

### メールイン データベース

メールイン データベースは、メールを受信するために設計されたデータベースです。これは (独自の ID ファイルおよびパスワードを持たない) 特定の Lotus Domino ユーザー アカウントに関連付けられていない Lotus Domino のメールボックス (メール テンプレートに基づく Notes データベース) です。メールイン データベースには、メールイン データベースとそれ独自のメール アドレスに関連付けられている一意の UserID ("省略名") があります。

各ユーザーには Lotus Domino アカウントは、1 つのみ必要です。自身のメール アドレスに別のユーザーと共有する別のメールボックスが必要な場合 (例: group@contoso.com))、Notes アカウントを別途作成する代わりにメールイン データベースを作成します。このメールボックスへのアクセスは、自身の ID ファイルおよびパスワードを使用して開くことが許可されている Notes ユーザーの名前が含まれたアクセス制御リスト (ACL) を介して制御されています。メールイン データベースのアクセスには、別のパスワードは必要ありません。

必要な属性の一覧については、この記事の後の「[必須属性](#mandatory-attributes)」というセクションを参照してください。

データベースがメールを受信するよう設計されている場合、Lotus Domino にメールイン データベースが作成されます。このドキュメントは、データベースのコピーを格納するすべてのサーバーの Domino Directory で必要です。メールイン データベース ドキュメントの作成方法の詳細については、「[メールイン データベース ドキュメントの作成](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)」を参照してください。

メールイン データベースを作成する前に、(Lotus の管理者が作成した) データベースが Domino サーバーに既に存在している必要があります。

### グループ管理

Lotus Domino のグループ管理の詳細な説明については、次を参照してください。

- [グループの使用](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [グループの作成](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [グループの作成および変更](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [グループの管理](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [グループの名前変更](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### パスワード管理

登録済みの Lotus Domino ユーザーには、次の 2 種類のパスワードがあります。

1. (User.id ファイルに格納されている) ユーザー パスワード
2. インターネット/HTTP パスワード

Lotus Domino Connector では、HTTP パスワードでの操作のみサポートしています。

パスワードを管理するには、Management Agent Designer でコネクタのパスワード管理を有効にする必要があります。パスワード管理を有効にするには、**[拡張機能の構成]** ダイアログ ページで **[パスワードの管理を有効にする]** を選択してください。

![拡張機能の構成](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino Connector では、インターネット パスワードの次の操作をサポートしています。

- パスワードの設定: パスワードの設定を使用すると、Domino ユーザーに新しい HTTP およびインターネット パスワードを設定できます。既定ではアカウントのロックは解除されます。ロック解除のフラグは、同期エンジンの WMI インターフェイスで公開されています。
- パスワードの変更: これは、ユーザーがパスワードの変更を希望したり、指定した期間後にパスワードの変更が求めらたりするシナリオ用です。この操作を実行するには、(新旧のパスワードの) 両方が必須です。変更後、Lotus Domino で新しいパスワードが更新されます。

詳細については、次を参照してください。

- [インターネット ロックアウト機能の使用](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [インターネット パスワードの管理](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## 参照情報

このセクションでは、Lotus Domino Connector の属性および属性の要件について説明します。

### Lotus Notes のプロパティ

Lotus Domino ディレクトリに Person オブジェクトをプロビジョニングする際、オブジェクトには特定の値が入った特定のプロパティのセットが必要です。これらの値は、Create 操作でのみ必要です。

これらのプロパティとその説明は、次の表で示します。

プロパティ | 説明
--- | ---
\_MMS\_AltFullName | ユーザーの別の完全名。
\_MMS\_AltFullNameLanguage | ユーザーの別の完全名を指定するために使用する言語。
\_MMS\_CertDaysToExpire | 証明書が有効期限切れになる前の現在の日付からの日数。これを指定しない場合、既定の日付は現在の日から 2 年後になります。
\_MMS\_Certifier | 認証者の組織階層名が格納されたプロパティ。例: OU=OrganizationUnit,O=Org,C=Country。
\_MMS\_IDPath | このプロパティが空の場合、ユーザー ID ファイルが同期サーバーのローカルに作成されません。プロパティにファイル名が含まれる場合、ユーザー ID ファイルがメタデータ フォルダーに作成されます。このプロパティには、ユーザー ID ファイルが作成された場所の完全なパスを含めることも可能です。
\_MMS\_IDRegType | Person は、連絡先、米国ユーザーおよび海外ユーザーに分類できます。可能な値を次に示します。<li>0 - 連絡先</li><li>1 - 米国ユーザー</li><li>2 - 海外ユーザー</li>
\_MMS\_IDStoreType | 米国および海外ユーザーに必須のプロパティ。このプロパティには、ユーザー ID を Notes のアドレス帳に添付ファイルとして格納するか、ユーザーのメール ファイルに格納するかを指定する整数値が含まれます。ユーザー ID ファイルがアドレス帳の添付ファイルの場合は、必要に応じて \_MMS\_IDPath 付きのファイルとして作成できます。<li>空 - ID ファイルは ID コンテナーに格納。(連絡先に使用する) ID ファイルはない。</li><li> 1: Notes アドレス帳の添付ファイル。添付ファイルのユーザー ID ファイルには、\_MMS\_Password プロパティを設定する必要があります。</li><li>2: ユーザーのメール ファイルへの ID の格納。Person の登録時にメール ファイルを作成するには、\_MMS\_UseAdminP は false に設定する必要があります。\_MMS\_Password プロパティは、ユーザーの ID ファイルに設定する必要があります。</li>
\_MMS\_MailQuotaSizeLimit | 電子メール ファイル データベースに許可されるメガバイト数での領域。
\_MMS\_MailQuotaWarningThreshold | 警告が発行される前に電子メール ファイル データベースに許可されるメガバイト数での領域。
\_MMS\_MailTemplateName | ユーザーの電子メール ファイルの作成に使用される電子メール テンプレート ファイル。テンプレートを指定すると、指定したテンプレートを使用してメール ファイルが作成されます。テンプレートを指定しない場合、ファイルの作成に既定のテンプレート ファイルが使用されます。
\_MMS\_OU | 証明者の下の OU 名である省略可能なプロパティ。連絡先の場合、このプロパティは空になっている必要があります。
\_MMS\_Password | ユーザーに必須のプロパティ。このプロパティには、オブジェクトの ID ファイルのパスワードが含まれます。
\_MMS\_UseAdminP | Domino サーバーの AdminP プロセスでメール ファイルを作成する場合、プロパティは true に設定する必要があります (エクスポート プロセスに対して非同期)。プロパティを false に設定する場合、メール ファイルは Domino ユーザーと共に作成されます (エクスポート プロセスで同期)。

ID ファイルが関連付けられているユーザーについては、\_MMS\_Password プロパティに値を含める必要があります。Lotus Notes クライアントを使用した電子メールでのアクセスの場合、ユーザーの MailServer および MailFile プロパティには値を含める必要があります。

Web ブラウザーを介して電子メールにアクセスするには、次のプロパティに値がある必要があります。

- MailFile: メール ファイルが格納されている Lotus Domino サーバーのパスが含まれる必須プロパティです。
- MailServer: Lotus Domino サーバーの名前を含む必須プロパティです。Domino サーバー上に Lotus メール ファイルを作成するときに、使用する名前です。
- HTTPPassword: オブジェクトの Web アクセス用のパスワードを含む省略可能なプロパティです。

メール機能なしに Domino Server にアクセスするには、HTTPPassword プロパティには値が必要で、MailFile プロパティおよび MailServer プロパティは空にできます。

\_MMS\_ IDStoreType = 2 (メール ファイルに ID を格納) を使用すると NotesRegistrationclass の MailSystem プロパティは、REG\_MAILSYSTEM\_INOTES (3) に設定されます。

### 必須の属性

Lotus Domino Connector は、同期サービスで主に次の 4 種類のオブジェクト (ドキュメントの種類) をサポートしています。

- グループ
- メールイン データベース
- Person
- 連絡先 (証明者のない Person)
- リソース

このセクションでは、Domino サーバーへのオブジェクトのエクスポート用にサポートされている各種類のオブジェクトに必須の属性について説明します。

オブジェクトの種類 | 必須の属性
--- | ---
グループ | <li>ListName</li>
メールイン データベース | <li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>
Person | <li>LastName</li><li>MailFile</li><li>ShortName</li><li>\_MMS\_Password</li><li>\_MMS\_IDStoreType</li><li>\_MMS\_Certifier</li><li>\_MMS\_IDRegType</li><li>\_MMS\_UseAdminP</li>
連絡先 (証明者のない Person) | <li>\_MMS\_IDRegType</li>
リソース | <li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Site</li><li>DisplayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>

## お問い合わせの多い問題および質問

### スキーマが検出できない

スキーマを検出するには、Domino サーバーに schema.nsf ファイルが存在している必要があります。このファイルは、サーバー上に LDAP がインストールされている場合のみ表示されます。スキーマを検出できない場合は、次を確認してください。

- ファイル schema.nsf が Domino サーバーのルート フォルダーにあります。
- ユーザーに schema.nsf ファイルを参照する権限があります。
- LDAP サーバーを再起動します。'Lotus Domino コンソール' を開き、'Tell LDAP ReloadSchema' コマンドを使用して、スキーマを再度読み込みます。

### すべてのセカンダリ アドレス帳が表示されない

Domino Connector は、セカンダリ アドレス帳の検索を Directory Assistance 機能に依存しています。セカンダリ アドレス帳が見つからない場合、[Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) が有効で、Domino サーバーに構成されていることを確認します。

### Domino のカスタム属性

Domino には、コネクタで使用できるカスタム属性など、スキーマを拡張するいくつかの方法があります。

**方法 1: Lotus Domino スキーマの拡張**

1. [これらの手順](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html)に従い Domino Directory テンプレート {PUBNAMES.NTF} のコピーを作成します (既定の IBM Lotus Domino ディレクトリ テンプレートはカスタマイズしないでください)。
2. Domino Designer で作成したばかりの Domino Directory テンプレート {CONTOSO.NTF} テンプレートのコピーを開き、次の手順に従います。
    - [Shared Elements] をクリックし、[Subforms] を展開します。
    - ${ObjectName}InheritableSchema サブフォームをダブルクリックします (ここで {ObjectName} は既定の構造上のオブジェクト クラス名です。例: Person)。
    - スキーマ {MyPersonAtrribute} に追加する属性に名前を付け、[Create] メニューを選択し、次いでメニューから [Field] を選択し、それに対応するフィールドを作成します。
    - 追加したフィールドの [Properties] ウィンドウで、その種類、スタイル、サイズ、フォントおよびその他の関連パラメーターを選択して、そのプロパティを設定します。
    - 属性の既定値は、その属性の名前と同じにします (例: 属性名が MyPersonAttribute の場合は、既定値は同じ名前にします)。
    - 更新値での ${ObjectName}InheritableSchema サブフォームの保存
3. Domino Directory テンプレート {PUBNAMES.NTF} を、[これらの手順に従い](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)、新しいカスタム テンプレート {CONTOSO.NTF} に置き換えます。
4. Domino Admin を閉じ、Domino コンソールを開いて LDAP サービスを再起動して、LDAP スキーマを再度読み込みます。
    - Domino コンソールの **[Domino Command]** テキストフィールドの下に [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html) コマンドを入力し、LDAP サービスを再起動します。
    - LDAP スキーマを再度読み込むために Tell LDAP コマンドの Tell LDAP ReloadSchema を使用します。
5. Domino Admin を開き、[People & Groups] タブを選択し、追加した属性が Domino の [Add Person] に反映されていることを確認します。
6. [Files] タブから Schema.nsf を開き、追加した属性が dominoPerson LDAP オブジェクト クラスに反映されていることを確認します。

**方法 2: は、カスタム属性を使用し auxClass を作成し、オブジェクト クラスと関連付ける**

1. [これらの手順](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) に従い Domino Directory テンプレート {PUBNAMES.NTF} のコピーを作成します (既定の IBM Lotus Domino ディレクトリ テンプレートはカスタマイズしないでください)。
2. 作成したばかりの Domino ディレクトリ テンプレート {CONTOSO.NTF} テンプレートのコピーを Domino Designer で開きます。
3. 左のウィンドウから [Shared Code]、[Subforms] の順に選択します。
4. [New Subform] をクリックします。
5. 次を実行し、新しいサブフォームのプロパティを指定します。
    - 新しいサブフォームが開いたら、[Design - Subform Properties] を選択します。
    - [Name] プロパティの横に、TestSubform など、補助オブジェクト クラスの名前を入力します。
    - [Options] プロパティの [Include in Insert Subform... dialog] のチェックボックスはオンのままにします。
    - [Options] プロパティの [Render pass through HTML in Notes] のチェックボックスはオフにします。
    - その他のプロパティはそのままにし、[Subform Properties] ボックスを閉じます。
    - 新しいサブフォームを保存し閉じます。
6. 次を実行し、補助オブジェクト クラスを定義するフィールドを追加します。
    - 作成したばかりのサブフォームを開きます。
    - [Create]、[Field] の順に選択します。
    - [Field] ダイアログ ボックスの [Basics] タブで [Name] の横に、{MyPersonTestAttribute} などの任意の名前を指定します。
    - 追加したフィールドの種類、スタイル、サイズ、フォントおよびその他の関連パラメーターを選択して、そのプロパティを設定します。
    - 属性の既定値は、その属性の名前と同じにします (例: 属性名が MyPersonTestAttribute の場合は、既定値は同じ名前にします)。
    - 更新された値でサブフォームを保存し、次を実行します。
        - 左のウィンドウから [Shared Code]、[Subforms] の順に選択します。
        - 新しいサブフォームを選択し、[Design - Design Properties] を選択します。
        - 左から 3 番目のタブをクリックし、**[Propagate this prohibition of design change]** を選択します。
7. ${ObjectName}ExtensibleSchema サブフォームを開きます (ここで {ObjectName} は、Person などの既定の構造オブジェクト クラスです)。
8. リソースを挿入し、サブフォーム (TestSubform など作成したばかりのもの) を選択し、${ObjectName}ExtensibleSchema サブフォームを保存します。
9. Domino Directory テンプレート {PUBNAMES.NTF} を、[これらの手順に従い](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)、新しいカスタム テンプレート {CONTOSO.NTF} に置き換えます。
10. Domino Admin を閉じ、Domino コンソールを開いて LDAP サービスを再起動して、LDAP スキーマを再度読み込みます。
    - Domino コンソールの **[Domino Command]** テキストフィールドの下に [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html) コマンドを入力し、LDAP サービスを再起動します。
    - LDAP スキーマを再度読み込むために Tell LDAP コマンドの Tell LDAP ReloadSchema を使用します。
11. Domino Admin を開き、[People & Groups] タブを選択し、追加した属性が Domino の ([Others] タブ下の) [Add Person] に反映されていることを確認します。
12. **[Files]** タブから Schema.nsf を開き、追加した属性が TestSubform LDAP 補助オブジェクト クラスに反映されていることを確認します。

**方法 3: ExtensibleObject クラスへのカスタム属性の追加**

1. ルート ディレクトリにある {Schema.nsf} ファイルを開きます。
2. 左のメニューの **[All Schema Documents]** の下の [Select LDAP Object Classes] を選択し、**[Add Object class]** ボタンをクリックします。
3. {zzzExtensibleSchema} の形式で、スキーマを拡張するオブジェクトへの参照と共に LDAP 名を指定します (ここで zzz は Person などの既定の構造オブジェクトの名前です)。たとえば、Person オブジェクト クラスのスキーマを拡張するには、LDAP 名 {PersonExtensibleSchema} を提供します。
4. スキーマを拡張する上位のオブジェクト クラス名を入力します。たとえば、Person オブジェクト クラスのスキーマを拡張するには、上位オブジェクト クラス名 {dominoPerson} を入力します。
5. オブジェクト クラスに対応する有効な OID を入力します。
6. 必要に応じて、[Mandatory Attribute Types] または [Optional Attribute Types] フィールドの下で [Extended attributes] または [custom attributes] を選択します。
7. 必要なオブジェクトを ExtensibleObjectClass に追加した後、**[Save & Close]** ボタンをクリックします。
8. ExtensibleObjectClass が、拡張属性を使用して該当する既定のオブジェクト クラス用に作成されます。

## トラブルシューティング

-	コネクタのトラブルシューティングを行うためにログ記録を有効にする方法については、「[How to Enable ETW Tracing for Connectors (コネクタの ETW トレースを有効にする方法)](http://go.microsoft.com/fwlink/?LinkId=335731)」を参照してください。

<!---HONumber=AcomDC_0525_2016-->