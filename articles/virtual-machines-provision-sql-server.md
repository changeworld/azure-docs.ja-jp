<properties linkid="manage-windows-commontask-install-sql-server" urlDisplayName="Install SQL Server" pageTitle="Provision a SQL Server virtual machine in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint"></tags>

# Azure での SQL Server 仮想マシンのプロビジョニング

Azure の仮想マシン イメージ ギャラリーには、Microsoft SQL Server を含むイメージがいくつか用意されています。ギャラリーからいずれかの仮想マシン イメージを選択すると、わずか数クリックで、Azure 環境への仮想マシンのプロビジョニングを行うことができます。

このチュートリアルでは、次のことについて説明します。

-   [Azure の管理ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う][Azure の管理ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う]
-   [リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する][リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する]
-   [別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する][別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する]
-   [次のステップ][次のステップ]

## <span id="Provision"></span>Azure の管理ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う</a>

1.  アカウントを使用して [Azure 管理ポータル][Azure 管理ポータル]にログインします。Azure アカウントを持っていない場合は、[Azure の無料評価版サイト][Azure の無料評価版サイト]にアクセスしてください。

2.  Azure 管理ポータルで、Web ページの左下にある **[+ 新規]** をクリックし、**[コンピューティング]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

3.  **[仮想マシンの作成]** ページで、SQL Server を含む仮想マシン イメージを選択し、ページの右下にある次へ進む矢印をクリックします。Azure でサポートされる SQL Server イメージの最新情報については、「[Azure の仮想マシンにおける SQL Server」][Azure の仮想マシンにおける SQL Server」]ドキュメント セットの「[Azure の仮想マシンにおける SQL Server の概要][Azure の仮想マシンにおける SQL Server の概要]」トピックを参照してください。

    > [WACOM.NOTE] プラットフォーム イメージの SQL Server 評価エディションを使用して仮想マシンを作成した場合、それをギャラリーにある分単位課金エディション イメージにアップグレードすることはできません。次の 2 つのオプションのいずれかを選択できます。

    > -   ギャラリーにある分単位課金エディションの SQL Server を使用して新しい仮想マシンを作成し、「[Azure の仮想マシン間でデータ ディスクを使用して SQL Server のデータベース ファイルとスキーマを移行する方法][Azure の仮想マシン間でデータ ディスクを使用して SQL Server のデータベース ファイルとスキーマを移行する方法]」の手順に従って、データベース ファイルをこの新しい仮想マシンに移行します。**または**、

    > -   「[SQL Server 2014 の別のエディションへのアップグレード (セットアップ)][SQL Server 2014 の別のエディションへのアップグレード (セットアップ)]」の手順に従って、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ][Azure でのソフトウェア アシュアランスによるライセンス モビリティ]」の合意に基づき、SQL Server 評価エディションの既存のインスタンスを異なるエディションの SQL Server にアップグレードします。SQL Server のライセンス コピーを購入する方法については、[SQL Server の購入方法に関するページ][SQL Server の購入方法に関するページ]を参照してください。

4.  **[仮想マシンの構成]** ページで、次の情報を指定します。

    -   **[仮想マシン名]** に入力します。
    -   **[新しいユーザー名]** ボックスに、VM ローカル管理者アカウントの一意のユーザー名を入力します。
    -   **[新しいパスワード]** ボックスに、強力なパスワードを入力します。詳細については、「[強力なパスワード][強力なパスワード]」を参照してください。
    -   **[パスワードの確認]** ボックスに、パスワードを再度入力します。
    -   **[サイズ]** ドロップダウン リストで、適切なサイズを選択します。

    > [WACOM.NOTE] 仮想マシンのサイズは、プロビジョニング中に指定します。運用環境のワークロードで推奨される最小サイズは A2 です。SQL Server Enterprise Edition の使用時に推奨される仮想マシンの最小サイズは A3 です。SQL Server Enterprise Edition を使用する場合は、A3 以上を選択してください。データ ウェアハウジング向け SQL Server 2012 Enterprise イメージを使用する場合は、A6 を選択してください。データ ウェアハウジング向け SQL Server 2014 イメージを使用する場合は、A7 を選択してください。選択したサイズにより、構成できるデータ ディスクの数が制限されます。使用可能な仮想マシンのサイズと仮想マシンに接続できるデータ ディスクの数に関する最新情報については、「[Azure の仮想マシンおよびクラウド サービスのサイズ][Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。

    右下にある次へ進む矢印をクリックして続行します。

    ![VM 構成][VM 構成]

5.  **[仮想マシン モード]** ページで、次の情報を指定します。

    -   **[スタンドアロンの仮想マシン]** をクリックします。
    -   **[DNS 名]** ボックスに、目的の DNS 名の最初の部分を入力すると、**TESTNAME.cloudapp.net** という形式の完全な名前を指定できます。
    -   **[リージョン/アフィニティ グループ/仮想ネットワーク]** ボックスで、この仮想イメージをホストするリージョンを選択します。

    矢印をクリックして次へ進みます。

    ![VM モード][VM モード]

6.  **[仮想マシンのオプション]** ページで、次の情報を指定します。

    -   **[可用性セット]** ボックスの一覧の **[(なし)]** を選択します。
    -   法律条項を読み、同意します。

    ![VM オプション][VM オプション]

7.  続行するには、右下隅にあるチェック マークをクリックします。

8.  Azure によって仮想マシンの準備が行われるまで待ちます。仮想マシンの状態は次のように進行します。

    -   開始中 (プロビジョニング)
    -   停止済み
    -   開始中 (プロビジョニング)
    -   実行中 (プロビジョニング)
    -   実行中

## <span id="RemoteDesktop"></span>リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する</a>

1.  プロビジョニングが完了したら、仮想マシンの名前をクリックして [ダッシュボード] ページに移動します。ページの下部にある **[接続]** をクリックします。

    ![[ダッシュボード] ページの選択](./media/virtual-machines-provision-sql-server/5VM-Connect.png)

2.  Windows リモート デスクトップ プログラム (`%windir%\system32\mstsc.exe`) を使用して rpd ファイルを開くよう選択します。

3.  **[Windows セキュリティ]** ダイアログ ボックスで、先ほど指定したローカル管理者アカウントのパスワードを入力します (仮想マシンの資格情報を確認するように求められることがあります)。

4.  この仮想マシンに初めてログオンした場合は、デスクトップのセットアップや Windows の更新プログラムの適用、Windows の初期構成タスク (sysprep) の完了など複数のプロセスが必要になる場合があります。Windows sysprep の完了後、SQL Server セットアップによって構成タスクが完了されます。これらのタスクにより、完了までに多少の遅延が生じることがあります。SQL Server のセットアップが完了するまでは、`SELECT @@SERVERNAME` から正しい名前が返されないことがあります。

Windows リモート デスクトップで仮想マシンに接続したら、仮想マシンは他のコンピューターと同様に使用できます。SQL Server Management Studio (仮想マシン上で実行) を使用して、通常どおりに SQL Server の既定インスタンスに接続します。

## <span id="SSMS"></span>別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する</a>

インターネットから SQL Server インスタンスに接続するには、次のタスクを完了している必要があります。詳細については、この後のセクションで説明します。

-   [仮想マシン用の TCP エンドポイントを作成する][仮想マシン用の TCP エンドポイントを作成する]
-   [Windows ファイアウォールで TCP ポートを開く][Windows ファイアウォールで TCP ポートを開く]
-   [TCP プロトコルでリッスンするように SQL Server を構成する][TCP プロトコルでリッスンするように SQL Server を構成する]
-   [混合モード認証用に SQL Server を構成する][混合モード認証用に SQL Server を構成する]
-   [SQL Server 認証ログインを作成する][SQL Server 認証ログインを作成する]
-   [仮想マシンの DNS 名を特定する][仮想マシンの DNS 名を特定する]
-   [別のコンピューターからデータベース エンジンに接続する][別のコンピューターからデータベース エンジンに接続する]
-   [アプリケーションからデータベース エンジンに接続する] (\#cdea)

次の図は、接続パスの概要を示したものです。

![SQL Server 仮想マシンに接続する][SQL Server 仮想マシンに接続する]

### <span id="Endpoint"></span>仮想マシン用の TCP エンドポイントを作成する</a>

仮想マシンには、着信する TCP 通信をリッスンするエンドポイントが必要です。この Azure 構成手順により、仮想マシンからアクセスできる TCP ポートに、着信する TCP ポート トラフィックが送信されます。

1.  Azure 管理ポータルで、**[仮想マシン]** をクリックします。

2.  新しく作成した仮想マシンをクリックします。仮想マシンに関する情報が表示されます。

3.  ページの最上部近くにある **[エンドポイント]** を選択し、ページの下部にある **[エンドポイントの追加]** をクリックします。

    ![[エンドポイントの追加] をクリックする](./media/virtual-machines-provision-sql-server/28Add-Endpoint.png)

4.  **[仮想マシンにエンドポイントを追加します]** ページで、**[エンドポイントの追加]** をクリックし、次へ進む矢印をクリックして続行します。

    ![[エンドポイントの追加] をクリックする][1]

5.  **[エンドポイントの詳細を指定します]** ページで、次の情報を指定します。

    -   **[名前]** ボックスに、エンドポイントの名前を指定します。
    -   **[プロトコル]** ボックスの一覧の **[TCP]** を選択します。**[プライベート ポート]** ボックスに、SQL Server の既定のリスニング ポートである「**1433**」と入力することもできます。同様に、**[パブリック ポート]** ボックスに「**57500**」と入力することもできます。多くの組織は、悪意のある攻撃を避けるために異なるポート番号を選択することに注意してください。

    ![エンドポイント画面][エンドポイント画面]

6.  チェック マークをクリックして続行します。エンドポイントが作成されます。

    ![エンドポイントが追加された VM][エンドポイントが追加された VM]

### <span id="FW"></span>データベース エンジンの既定のインスタンス用に Windows ファイアウォールで TCP ポートを開く</a>

1.  Windows リモート デスクトップを介して仮想マシンに接続します。ログインしたら、[スタート] ボタンをクリックし、**[ファイル名を指定して実行]** をクリックします。次に、「**WF.msc**」と入力して **[OK]** をクリックします。

    ![ファイアウォール プログラムを開始する][ファイアウォール プログラムを開始する]

2.  **[セキュリティが強化された Windows ファイアウォール]** の左ペインで、**[受信の規則]** を右クリックし、[操作] ペインの **[新しい規則]** をクリックします。

    ![新しい規則](./media/virtual-machines-provision-sql-server/13New-FW-Rule.png)

3.  **[規則の種類]** ダイアログ ボックスで、**[ポート]** を選択して **[次へ]** をクリックします。

4.  **[プロトコルおよびポート]** ダイアログ ボックスで、**[TCP]** を選択します。**[特定のローカル ポート]** を選択し、データベース エンジン インスタンスのポート番号を入力します (既定のインスタンスの場合は「**1433**」を指定し、エンドポイントの手順でプライベート ポートに別のポート番号を指定した場合はその番号を指定します)。

    ![TCP ポート 1433][TCP ポート 1433]

5.  **[次へ]** をクリックします。

6.  **[操作]** ダイアログ ボックスで、**[接続を許可する]** を選択し、**[次へ]** をクリックします。

    **セキュリティ上の注意:** **[セキュリティで保護されている場合のみ接続を許可する]** を選択すると、セキュリティが追加されます。お使いの環境で追加のセキュリティ オプションを構成する場合はこのオプションを選択してください。

    ![接続を許可する](./media/virtual-machines-provision-sql-server/15Allow-Connection.png)

7.  **[プロファイル]** ダイアログ ボックスで、**[パブリック]** をオンにし、**[次へ]** をクリックします。

    **セキュリティ上の注意:** **[パブリック]** をオンにすると、インターネット経由のアクセスが許可されます。可能であれば、できるだけ制限の厳しいプロファイルを選択してください。

    ![パブリック プロファイル][パブリック プロファイル]

8.  **[名前]** ダイアログ ボックスで、この規則の名前と説明を入力し、**[完了]** をクリックします。

    ![規則の名前][規則の名前]

必要に応じて他のコンポーネント用に追加のポートを開きます。詳細については、「[SQL Server のアクセスを許可するための Windows ファイアウォールの構成][SQL Server のアクセスを許可するための Windows ファイアウォールの構成]」を参照してください。

### <span id="TCP"></span>TCP プロトコルでリッスンするように SQL Server を構成する</a>

1.  リモート デスクトップを使用して仮想マシンに接続している状態で、[スタート] メニューの **[すべてのプログラム]** をクリックし、**[Microsoft SQL Server** *バージョン*]、**[構成ツール]**、**[SQL Server 構成マネージャー]** の順にクリックします。

    ![SSCM を開く][SSCM を開く]

2.  SQL Server 構成マネージャーのコンソール ペインで、**[SQL Server ネットワークの構成]** を展開します。

3.  コンソール ペインで **[*インスタンス名* のプロトコル]** をクリックします(既定のインスタンスでは、**[MSSQLSERVER のプロトコル]** です)。

4.  詳細ウィンドウで、[TCP] を右クリックします。ギャラリー イメージの場合、その状態は既定で [有効] です。カスタム イメージの場合、**[有効化]** をクリックします (状態が [無効] の場合)。

    ![TCP を有効にする][TCP を有効にする]

5.  コンソール ペインで、**[SQL Server のサービス]** をクリックします (データベース エンジンの再起動は次の手順が完了するまで延期されることがあります)。

6.  詳細ペインで **[SQL Server (*インスタンス名*)]** (既定のインスタンスでは **[SQL Server (MSSQLSERVER)]**) を右クリックして、**[再起動]** をクリックします。これにより、SQL Server のインスタンスが停止し、再起動されます。

    ![データベース エンジンの再起動][データベース エンジンの再起動]

7.  SQL Server 構成マネージャーを閉じます。

SQL Server データベース エンジン用のプロトコルを有効にする方法の詳細については、「[サーバー ネットワーク プロトコルの有効化または無効化][サーバー ネットワーク プロトコルの有効化または無効化]」を参照してください。

### <span id="Mixed"></span>混合モード認証用に SQL Server を構成する</a>

ドメイン環境がない場合、SQL Server データベース エンジンで Windows 認証を使用することはできません。別のコンピューターからデータベース エンジンに接続するには、混合モード認証用に SQL Server を構成します。混合モード認証では、SQL Server 認証と Windows 認証の両方が許可されます (Azure の仮想ネットワークを構成した場合は、混合モード認証の構成が不要である可能性があります)。詳細については、「[Azure の仮想マシンにおける SQL Server][Azure の仮想マシンにおける SQL Server]」ドキュメント セットの「[Azure の仮想マシンにおける SQL Server の接続に関する考慮事項][Azure の仮想マシンにおける SQL Server の接続に関する考慮事項]」トピックを参照してください。

1.  リモート デスクトップを使用して仮想マシンに接続している状態で、[スタート] メニューの **[すべてのプログラム]** をクリックし、**[Microsoft SQL Server *バージョン*]**、**[SQL Server Management Studio]** の順にクリックします。

    ![SSMS を起動する][SSMS を起動する]

    初めて Management Studio を開く場合は、ユーザーの Management Studio 環境の作成が必要になります。これには数分かかることがあります。

2.  Management Studio が開くと、**[サーバーへの接続]** ダイアログ ボックスが表示されます。**[サーバー名]** ボックスに、オブジェクト エクスプローラーを使用してデータベース エンジンに接続する仮想マシンの名前を入力します (**[サーバー名]** として、仮想マシン名の代わりに **[(ローカル)]** または単一のピリオドを指定することもできます)。**[Windows 認証]** を選択し、**[ユーザー名]** ボックスで ***your\_VM\_name*\\your\_local\_administrator** をそのまま使用します。**[接続]** をクリックします。

    ![サーバーに接続する][サーバーに接続する]

3.  SQL Server Management Studio のオブジェクト エクスプローラーで、SQL Server のインスタンス名 (仮想マシン名) を右クリックし、**[プロパティ]** をクリックします。

    ![サーバー プロパティ][サーバー プロパティ]

4.  **[セキュリティ]** ページの **[サーバー認証]** で、**[SQL Server 認証モードと Windows 認証モード]** を選択し、**[OK]** をクリックします。

    ![認証モードを選択する][認証モードを選択する]

5.  [SQL Server Management Studio] ダイアログ ボックスで、SQL Server の再起動が必要であるというメッセージに対して **[OK]** をクリックします。

6.  オブジェクト エクスプローラーでサーバーを右クリックし、**[再起動]** をクリックします (実行中であれば、SQL Server エージェントも再起動する必要があります)。

    ![再起動](./media/virtual-machines-provision-sql-server/22Restart2.png)

7.  [SQL Server Management Studio] ダイアログ ボックスで、SQL Server の再起動に同意を求めるメッセージに対して **[はい]** をクリックします。

### <span id="Logins"></span>SQL Server 認証ログインを作成する</a>

別のコンピューターからデータベース エンジンに接続するには、1 つ以上の SQL Server 認証ログインを作成する必要があります。

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、新しいログインを作成するサーバー インスタンスのフォルダーを展開します。

2.  **[セキュリティ]** フォルダーを右クリックし、**[新規作成]** をポイントして、**[ログイン]** を選択します。

    ![新しいログイン][新しいログイン]

3.  **[ログイン - 新規作成]** ダイアログ ボックスの **[全般]** ページで、新しいユーザーの名前を **[ログイン名]** ボックスに入力します。

4.  **[SQL Server 認証]** を選択します。

5.  **[パスワード]** ボックスに、新しいユーザーのパスワードを入力します。**[パスワードの確認]** ボックスに、パスワードを再度入力します。

6.  パスワードの強制と複雑さに関するパスワード ポリシー オプションを適用するには、**[パスワード ポリシーを適用する]** (推奨) をオンにします。[SQL Server 認証] が選択されている場合、これは既定のオプションです。

7.  失効に関するパスワード ポリシー オプションを適用するには、**[パスワードの期限を適用する]** (推奨) をオンにします。このチェック ボックスをオンにする場合は、[パスワード ポリシーを適用する] がオンになっている必要があります。[SQL Server 認証] が選択されている場合、これは既定のオプションです。

8.  ログインの初回使用後に新しいパスワードの作成をユーザーに強制するには、**[ユーザーは次回ログイン時にパスワードを変更する]** をオンにします (このログインが別のユーザー用の場合は、このチェック ボックスをオンにすることをお勧めします。ログインが自分用の場合は、オンにしないでください)。このチェック ボックスをオンにする場合は、[パスワードの期限を適用する] がオンになっている必要があります。[SQL Server 認証] が選択されている場合、これは既定のオプションです。

9.  **[既定のデータベース]** ボックスの一覧から、ログインの既定のデータベースを選択します。このオプションの既定値は **master** です。ユーザー データベースをまだ作成していない場合は、**master** のままにしておきます。

10. **[既定の言語]** の値は、**[\<既定\>]** のままにしておきます。

    ![ログインのプロパティ][ログインのプロパティ]

11. これが初めて作成するログインである場合は、このログインを SQL Server 管理者専用に使用することが考えられます。その場合は、**[サーバー ロール]** ページで、**[sysadmin]** をオンにします。

    **セキュリティ上の注意:** sysadmin 固定サーバー ロールのメンバーには、データベース エンジンに対する完全な制御権が与えられています。このロールのメンバーは、適切なユーザーのみに限定してください。

    ![sysadmin](./media/virtual-machines-provision-sql-server/25sysadmin.png)

12. [OK] をクリックします。

SQL Server のログインの詳細については、「[ログインの作成][ログインの作成]」を参照してください。

### <span id="DNS"></span>仮想マシンの DNS 名を特定する</a>

別のコンピューターから SQL Server データベース エンジンに接続するには、仮想マシンのドメイン ネーム システム (DNS) 名が必要になります (これは、仮想マシンを識別するためにインターネットで使用される名前です。IP アドレスを使用することもできますが、Azure で冗長化またはメンテナンスのためにリソースが移動された場合、IP アドレスは変わる可能性があります。DNS 名は、新しい IP アドレスにリダイレクトできるため、安定しています)。

1.  Azure 管理ポータルで (または前の手順から)、**[仮想マシン]** を選択します。

2.  **[仮想マシン インスタンス]** ページの **[DNS 名]** 列で、**http://** から始まる仮想マシンの DNS 名を探してコピーします (名前全体がユーザー インターフェイスに表示されないことがありますが、右クリックしてコピーすることができます)。

    ![DNS 名](./media/virtual-machines-provision-sql-server/32DNS-Name.png)

### <span id="cde"></span>別のコンピューターからデータベース エンジンに接続する</a>

1.  インターネットに接続されたコンピューターで、SQL Server Management Studio を開きます。

2.  **[サーバーへの接続]** または **[データベース エンジンへの接続]** ダイアログ ボックスで、**[サーバー名]** ボックスに、先のタスクで決定した仮想マシンの DNS 名とパブリック エンドポイントのポート番号を *DNS 名,ポート番号* という形式で入力します (例: **tutorialtestVM.cloudapp.net,57500**)。

3.  **[認証]** ボックスで、**[SQL Server 認証]** を選択します。

4.  **[ログイン]** ボックスに、前のタスクで作成したログインの名前を入力します。

5.  **[パスワード]** ボックスに、前のタスクで作成したログインのパスワードを入力します。

6.  **[接続]** をクリックします。

    ![SSMS を使用した接続][SSMS を使用した接続]

### <span id="cdea"></span> アプリケーションからデータベース エンジンに接続する</a>

Azure の仮想マシンで実行されている SQL Server のインスタンスに Management Studio から接続できる場合は、次のような接続文字列を使用して接続できます。

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

詳細については、「[How to Troubleshoot Connecting to the SQL Server Database Engine (SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法)][How to Troubleshoot Connecting to the SQL Server Database Engine (SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法)]」を参照してください。

## <span id="Optional"></span>次のステップ</a>

プラットフォーム イメージを使用してAzure に SQL Server 仮想マシンを作成し、構成する方法を見てきました。SQL Server をAzure の仮想マシンで使用するときは、ライブラリの「[Azure の仮想マシンにおける SQL Server][Azure の仮想マシンにおける SQL Server」]」ドキュメント セットで説明されている詳細なガイダンスに従うことをお勧めします。このドキュメント セットには、詳細なガイダンスを説明した一連の記事とチュートリアルが含まれます。このシリーズに含まれるセクションは次のとおりです。

[Azure の仮想マシンにおける SQL Server][Azure の仮想マシンにおける SQL Server」]

[Azure の仮想マシンでの SQL Server の概要][Azure の仮想マシンにおける SQL Server の概要]

[Azure の仮想マシンに SQL Server を移行するための準備][Azure の仮想マシンに SQL Server を移行するための準備]

-   Azure の仮想マシン間でデータ ディスクを使用して SQL Server のデータベース ファイルとスキーマを移行する方法

[Azure の仮想マシンにおける SQL Server のデプロイ][Azure の仮想マシンにおける SQL Server のデプロイ]

-   CSUpload を使用してデータ ディスク内の SQL Server データ ファイルおよびセットアップ ファイルをオンプレミスから Azure にコピーする方法
-   Hyper-V を使用してベースとなる仮想マシンをオンプレミスで作成する方法
-   既にあるオンプレミスの SQL Server を使用して Azure に SQL Server の仮想マシンを作成する方法
-   既にあるオンプレミスの SQL Server 仮想マシンを使用して Azure に SQL Server の仮想マシンを作成する方法
-   PowerShell を使用して Azure に SQL Server 仮想マシンを設定する方法
-   接続されたデータ ディスクを使用してデータベース ファイルを格納する方法

[Azure の仮想マシンにおける SQL Server の接続に関する考慮事項][2]

-   チュートリアル: 同一クラウド サービス内の SQL Server に接続する方法
-   チュートリアル: 異なるクラウド サービス内の SQL Server に接続する方法
-   チュートリアル: ASP.NET アプリケーションを Azure 内の SQL Server に仮想ネットワークを介して接続する方法

[Azure の仮想マシンにおける SQL Server のパフォーマンスに関する考慮事項][Azure の仮想マシンにおける SQL Server のパフォーマンスに関する考慮事項]

[Azure の仮想マシンにおける SQL Server のセキュリティに関する考慮事項][Azure の仮想マシンにおける SQL Server のセキュリティに関する考慮事項]

[Azure の仮想マシンで実行される SQL Server のトラブルシューティングと監視][Azure の仮想マシンで実行される SQL Server のトラブルシューティングと監視]

[Azure の仮想マシン内の SQL Server の高可用性と災害復旧][Azure の仮想マシン内の SQL Server の高可用性と災害復旧]

-   チュートリアル: Azure AlwaysOn 可用性グループ (GUI)
-   チュートリアル: Azure AlwaysOn 可用性グループ (PowerShell)
-   チュートリアル: Azure AlwaysOn 可用性グループのリスナー構成
-   チュートリアル: Azure レプリカ ウィザードの追加
-   チュートリアル: Azure における障害復旧のためのデータベース ミラーリング
-   チュートリアル: Hybrid IT における障害復旧のためのデータベース ミラーリング
-   チュートリアル: Azure における高可用性のためのデータベース ミラーリング
-   チュートリアル: Hybrid IT における障害復旧のためのログ配布
-   Azure の可用性グループ リスナーに関するトラブルシューティング

[Azure の仮想マシンにおける SQL Server のバックアップと復元][Azure の仮想マシンにおける SQL Server のバックアップと復元]

[Azure の仮想マシンでの SQL Server Business Intelligence][Azure の仮想マシンでの SQL Server Business Intelligence]

-   SQL Server BI と SharePoint 2010 を実行する Azure VM を PowerShell を使用して作成する
-   SQL Server BI と SharePoint 2013 を実行する Azure VM を PowerShell を使用して作成する
-   ネイティブ モードのレポート サーバーを実行する Azure VM を PowerShell を使用して作成する

[Azure の仮想マシンにおける SQL Server データ ウェアハウス][Azure の仮想マシンにおける SQL Server データ ウェアハウス]

[Azure の仮想マシンにおける SQL Server の技術解説記事][Azure の仮想マシンにおける SQL Server の技術解説記事]

-   [ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のアプリケーション パターンと開発計画][ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のアプリケーション パターンと開発計画]

-   [ホワイト ペーパー: Azure の仮想マシンでの SQL Server Business Intelligence のデプロイ][ホワイト ペーパー: Azure の仮想マシンでの SQL Server Business Intelligence のデプロイ]

-   [ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のパフォーマンス ガイダンス][ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のパフォーマンス ガイダンス]

-   [ホワイト ペーパー: Reporting Services のレポート ビューアー制御と Microsoft Azure 仮想マシン ベースのレポート サーバー][ホワイト ペーパー: Reporting Services のレポート ビューアー制御と Microsoft Azure 仮想マシン ベースのレポート サーバー]

  [Azure の管理ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う]: #Provision
  [リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する]: #RemoteDesktop
  [別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する]: #SSMS
  [次のステップ]: #Optional
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
  [Azure の仮想マシンにおける SQL Server」]: http://go.microsoft.com/fwlink/p/?LinkId=294719
  [Azure の仮想マシンにおける SQL Server の概要]: http://go.microsoft.com/fwlink/p/?LinkId=294720
  [Azure の仮想マシン間でデータ ディスクを使用して SQL Server のデータベース ファイルとスキーマを移行する方法]: http://go.microsoft.com/fwlink/p/?LinkId=294738
  [SQL Server 2014 の別のエディションへのアップグレード (セットアップ)]: http://go.microsoft.com/fwlink/?LinkId=396915
  [Azure でのソフトウェア アシュアランスによるライセンス モビリティ]: http://www.windowsazure.com/ja-jp/pricing/license-mobility/
  [SQL Server の購入方法に関するページ]: http://www.microsoft.com/ja-jp/sqlserver/get-sql-server/how-to-buy.aspx
  [強力なパスワード]: http://msdn.microsoft.com/library/ms161962.aspx
  [Azure の仮想マシンおよびクラウド サービスのサイズ]: http://msdn.microsoft.com/ja-jp/library/azure/dn197896.aspx
  [VM 構成]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
  [VM モード]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
  [VM オプション]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
  [仮想マシン用の TCP エンドポイントを作成する]: #Endpoint
  [Windows ファイアウォールで TCP ポートを開く]: #FW
  [TCP プロトコルでリッスンするように SQL Server を構成する]: #TCP
  [混合モード認証用に SQL Server を構成する]: #Mixed
  [SQL Server 認証ログインを作成する]: #Logins
  [仮想マシンの DNS 名を特定する]: #DNS
  [別のコンピューターからデータベース エンジンに接続する]: #cde
  [SQL Server 仮想マシンに接続する]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
  [1]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
  [エンドポイント画面]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
  [エンドポイントが追加された VM]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
  [ファイアウォール プログラムを開始する]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
  [TCP ポート 1433]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
  [パブリック プロファイル]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
  [規則の名前]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
  [SQL Server のアクセスを許可するための Windows ファイアウォールの構成]: http://msdn.microsoft.com/ja-jp/library/cc646023.aspx
  [SSCM を開く]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
  [TCP を有効にする]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
  [データベース エンジンの再起動]: ./media/virtual-machines-provision-sql-server/11Restart.png
  [サーバー ネットワーク プロトコルの有効化または無効化]: http://msdn.microsoft.com/ja-jp/library/ms191294.aspx
  [Azure の仮想マシンにおける SQL Server]: http://go.microsoft.com/fwlink/?LinkId=294719
  [Azure の仮想マシンにおける SQL Server の接続に関する考慮事項]: http://go.microsoft.com/fwlink/?LinkId=294723
  [SSMS を起動する]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
  [サーバーに接続する]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
  [サーバー プロパティ]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
  [認証モードを選択する]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
  [新しいログイン]: ./media/virtual-machines-provision-sql-server/23New-Login.png
  [ログインのプロパティ]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
  [ログインの作成]: http://msdn.microsoft.com/ja-jp/library/aa337562.aspx
  [SSMS を使用した接続]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
  [How to Troubleshoot Connecting to the SQL Server Database Engine (SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法)]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx
  [Azure の仮想マシンに SQL Server を移行するための準備]: http://go.microsoft.com/fwlink/p/?LinkId=294721
  [Azure の仮想マシンにおける SQL Server のデプロイ]: http://go.microsoft.com/fwlink/p/?LinkId=294722
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=294723
  [Azure の仮想マシンにおける SQL Server のパフォーマンスに関する考慮事項]: http://go.microsoft.com/fwlink/?LinkId=294724
  [Azure の仮想マシンにおける SQL Server のセキュリティに関する考慮事項]: http://go.microsoft.com/fwlink/p/?LinkId=294725
  [Azure の仮想マシンで実行される SQL Server のトラブルシューティングと監視]: http://go.microsoft.com/fwlink/p/?LinkId=294726
  [Azure の仮想マシン内の SQL Server の高可用性と災害復旧]: http://go.microsoft.com/fwlink/p/?LinkId=294727
  [Azure の仮想マシンにおける SQL Server のバックアップと復元]: http://go.microsoft.com/fwlink/p/?LinkId=294728
  [Azure の仮想マシンでの SQL Server Business Intelligence]: http://go.microsoft.com/fwlink/p/?LinkId=294729
  [Azure の仮想マシンにおける SQL Server データ ウェアハウス]: http://msdn.microsoft.com/library/windowsazure/dn387396.aspx
  [Azure の仮想マシンにおける SQL Server の技術解説記事]: http://msdn.microsoft.com/library/azure/dn248435.aspx
  [ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のアプリケーション パターンと開発計画]: http://msdn.microsoft.com/library/azure/dn574746.aspx
  [ホワイト ペーパー: Azure の仮想マシンでの SQL Server Business Intelligence のデプロイ]: http://msdn.microsoft.com/library/windowsazure/dn321998.aspx
  [ホワイト ペーパー: Azure の仮想マシンにおける SQL Server のパフォーマンス ガイダンス]: http://msdn.microsoft.com/library/windowsazure/dn248436.aspx
  [ホワイト ペーパー: Reporting Services のレポート ビューアー制御と Microsoft Azure 仮想マシン ベースのレポート サーバー]: http://msdn.microsoft.com/library/azure/dn753698.aspx
