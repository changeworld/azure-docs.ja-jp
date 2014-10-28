<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Azure Site Recovery の概要: 内部設置型と Azure 間の保護

<div class="dev-callout"> 

<p>Azure Site Recovery を使用して、System Center Virtual Machine Manager (VMM) クラウド内に配置されている Hyper-V ホスト上で実行する仮想マシンを保護します。そのための構成方法は次のとおりです。</p>

<ul>
<li><b>内部設置型と Azure 間の保護</b> &mdash; VMM クラウド内の Hyper-V ホスト サーバー上に置かれた内部設置型仮想マシンを Azure にレプリケートします。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。仮想マシンのデータが内部設置型の Hyper-V サーバーから Azure ストレージにレプリケートされます。</li>

<li><b>内部設置型間の保護</b> &mdash; VMM クラウド内の Hyper-V ホスト サーバー上に置かれた 1 つの内部設置型サイトの仮想マシンから別の内部設置型サイトの仮想マシンにレプリケートします。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。仮想マシンのデータが 1 つの内部設置型の Hyper-V サーバーから別のサーバーにレプリケートされます。Azure Site Recovery は、このプロセスを調整するだけです。
このシナリオの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=398765">Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection (Azure の Hyper-V 回復マネージャーの構成)</a>」を参照してください。</li>

</ul>
 
<h2><a id="about"></a>このチュートリアルについて</h2>


<P>このチュートリアルは、すばやい概念実証のために Azure Site Recovery のデプロイを支援することが目的です。ここではできる限り簡潔な方法と既定の設定を用いて説明します。以下の手順について説明します。
<ul>
<li>Azure Site Recovery コンテナーのセットアップ &mdash; 証明書をコンテナーにアップロードし、ソース VMM サーバーでのセットアップ後、コンテナー キーを生成します。 </li>
<li>ソース VMM サーバーと Hyper-V ホスト サーバーのセットアップ &mdash; ソース VMM サーバーに Azure Site Recovery Provider をインストールし、Hyper-V ホスト サーバーに Azure Recovery Services Agent をインストールします。</li>
<li>VMM クラウドの構成 &mdash; ソース VMM サーバー上のクラウドの保護設定を構成します。</li>
<li>仮想マシンの有効化 &mdash; 仮想マシンの保護を有効にします。</li>
<li>フェールオーバーの実行 &mdash; 復旧計画を作成してテスト フェールオーバーを実行します。</li>
</ul>


完全なデプロイに関する詳細については、以下を参照してください。</P>

<UL>
<LI>「<a href="http://go.microsoft.com/fwlink/?LinkId=321294">Plan for Azure Site Recovery Deployment (手順 5: 証明書の準備)</a>」&mdash; 完全なデプロイを開始する前に必要な計画手順について説明します。</LI>
<LI>「<a href="http://go.microsoft.com/fwlink/?LinkId=402679">Azure Site Recovery のデプロイ : オンプレミスと Azure 間の保護</a>」&mdash; 完全なデプロイの手順をステップ バイ ステップで説明しています。</LI>
<LI>「<a href="http://go.microsoft.com/fwlink/?LinkId=378272">Azure Site Recovery の管理および監視</a>」&mdash; フェールオーバーの実行方法および、デプロイの管理と監視の方法について説明します。</LI>
</UL>
<P>このチュートリアルを使用している場合に問題が発生した場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery のエラーの一般的なシナリオと解決策に関するページ</a>を参照するか、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>のページに質問を投稿してください。</P>

</div>

## <span id="before"></span></a>開始する前に

<div class="dev-callout"> 
<P>このチュートリアルを開始する前に、前提条件を確認してください。</P>

<h3><a id="HVRMPrereq"></a>Azure の前提条件</h3>

<UL>
<LI><b>Azure アカウント</b> &mdash; Azure アカウントが必要になります。まだアカウントを持っていない場合は、「<a href="http://aka.ms/try-azure">Microsoft Azure 1 か月間無料評価版</a>」を参照してください。価格情報については、「<a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery Pricing (Azure Site Recovery の価格設定)</a>」を参照してください。</LI>
<LI><b>証明書</b> &mdash; 公開キーと共に管理証明書 (.cer) をコンテナーにアップロードする必要があります。この証明書は、.pfx ファイル (秘密キー付き) としてエクスポートし、コンテナーに登録する各 VMM サーバーでインポートします。このチュートリアルでは、自己署名証明書を使用します。完全なデプロイを実行する場合は、計画ガイドで説明されている「<a href="http://go.microsoft.com/fwlink/?LinkId=321294">certificate requirements (手順 5: 証明書の準備)</a>」に準拠した有効な SSL 証明書を使用できます。</a> </LI>


</LI>

<LI><b>Azure ストレージのアカウント</b> &mdash; Azure にレプリケートしたデータを格納するために Azure ストレージのアカウントが必要になります。アカウントではジオ (主要地域) レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。Azure ストレージのセットアップの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure ストレージの概要</a>」を参照してください。</LI>
</UL>

<h3><a id="VMMPrereq"></a>VMM の前提条件</h3>

<UL>
<LI><b>VMM サーバー</b> &mdash; System Center 2012 R2 上で実行される VMM サーバー。</LI>
<LI><b>VMM クラウド</b> &mdash; VMM サーバー上の少なくとも 1 つのクラウド。このクラウドには以下のものが含まれている必要があります。
    <UL>
<LI>1 つ以上の VMM ホスト グループ</LI>
<LI>各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。</LI>
<li>クラウド内のソース Hyper-V サーバーに配置されている 1 つ以上の仮想マシン。仮想マシンは世代 1 にする必要があります。</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>仮想マシンの前提条件</h3>

<UL>
<LI><b>世代</b> &mdash; Azure は世代 1 の仮想マシンのみサポートします。</LI>
<LI>Azure にフェールオーバーする仮想マシンのすべてのサポート要件の一覧については、計画ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=402602">前提条件とサポート</a>」をお読みください。 </LI>  
</UL>

<h2><a id="tutorial"></a>チュートリアルの手順</h2> 

前提条件を確認した後、以下の手順を実行します。
<UL>
<LI><a href="#createcert">手順 1.証明書の取得と構成</a> - .cer 証明書を取得し、.pfx ファイルとしてエクスポートして、その .pfx ファイルを VMM サーバーにインポートします。</LI>
<LI><a href="#vault">手順 2.コンテナーの作成</a> &mdash; Azure Site Recovery コンテナーを作成します。</LI>
<LI><a href="#upload">手順 3.コンテナーの構成</a> &mdash; 管理証明書をコンテナーにアップロードし、コンテナーにキーを生成します。このキーを使用することで、VMM サーバーに配置されている Provider は Azure Site Recovery から送信されたコマンドのみを実行するようになります。</LI>
<LI><a href="#download">手順 4.Provider アプリケーションのインストール</a> &mdash; Microsoft Azure Site Recovery Provider アプリケーションを VMM サーバー上で実行します。この手順で Provider がインストールされ、コンテナーに VMM サーバーが登録されます。</LI>
<LI><a href="#agent">手順 5.Agent アプリケーションのインストール</a> &mdash; Microsoft Azure Recovery Services Agent を Hyper-V ホストごとにインストールします。</LI>
<LI><a href="#clouds">手順 6.クラウドの保護の構成</a> - VMM クラウドの保護設定を構成します。</LI>
<LI><a href="#NetworkMapping">手順 7.ネットワーク マッピングの構成</a> &mdash; ソース VM ネットワークをターゲット Azure ネットワークにマッピングするネットワーク マッピングをオプションで構成できます。</LI>
<LI><a href="#virtualmachines">手順 8.仮想マシンの保護の有効化</a> - 保護する VMM クラウドに配置された仮想マシンの保護を有効にします。</LI>
<LI><a href="#recovery plans">手順 9.復旧計画の構成と実行</a> &mdash; 復旧計画を作成し、計画したテスト フェールオーバーを実行します。</LI>
</UL>



<a name="createcert"></a> <h2>手順 1.証明書の取得と構成</h2>

次のステップで、証明書を取得して構成します。
<OL>
<LI><a href="#obtaincert">チュートリアルで使用する自己署名証明書の取得</a> - MakeCert ツールを使用して証明書を取得します。</LI>
<LI><a href="#exportcert">.pfx 形式での証明書のエクスポート</a> - 証明書の作成元であるサーバーで、.cer ファイルを、(秘密キーを含む) .pfx ファイルとしてエクスポートします。 </LI>
<LI><a href="#importcert">VMM サーバーへの .pfx 証明書のインポート</a> - エクスポート後、その .pfx ファイルを、コンテナーに登録する各 VMM サーバーのローカル コンピューター ストアの Personal フォルダーにインポートします。</LI>
</OL>


<h3><a id="obtaincert"></a>自己署名証明書 (.cer) の取得</h3>
<P>証明書のすべての要件に準拠する .cer x.509 証明書を以下の手順で作成します。</P>
<ol>
<LI>
MakeCert を実行するコンピューターに、<a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a> の最新バージョンをダウンロードします。SDK 全体をインストールする必要はありません。</LI>
<ol>
<LI>[場所の指定] ページで、<b>[Windows Software Development Kit for Windows 8.1 をこのコンピューターにインストールします]</b> を選択します。</LI>
<LI>[インストールする機能を選択してください] ページで、<b>[Windows ソフトウェア開発キット]</b> を除き、他のすべてのオプションをオフにします。</LI>
<LI>インストールが完了した後、makecert.exe が C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64 フォルダーに含まれていることを確認します。</LI>
<LI>管理者特権でコマンド プロンプト (cmd.exe) を開き、makecert.exe フォルダーに移動します。</LI> 
<LI>次のコマンドを実行して、自己署名証明書を作成します。CertificateName を、その証明書で使用する名前に置き換えます。また、-e: の後に、証明書の実際の有効期限を指定します。</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>成功状態は、証明書が作成されたことを示します。証明書は makecert.exe と同じフォルダーに格納されます。証明書はエクスポート時にアクセスしやすい別の場所に移動することができます。</P>

<h3><a id="exportcert"></a>.pfx 形式での証明書のエクスポート</h3>
<P>次のステップを完了して、.cer ファイルを .pfx 形式にエクスポートします。</P>
<ol>
<li>成功状態は、証明書が作成されたことを示します。証明書は makecert.exe と同じフォルダーに格納されます。証明書はエクスポート時にアクセスしやすい別の場所に移動することができます。</li>
<li>詳細ウィンドウで、管理する証明書をクリックします。</li>
<li><b>[アクション]</b> メニューの <b>[すべてのタスク]</b> をポイントし、<b>[エクスポート]</b> をクリックします。証明書のエクスポート ウィザードが表示されます。<b>[次へ]</b> をクリックします。</li>
<li><b>[秘密キーのエクスポート]</b> ページで <b>[はい]</b> をクリックして秘密キーをエクスポートします。<b>[次へ]</b> をクリックします。この操作は、インストール後に別のサーバーに秘密キーをエクスポートする場合にのみ必要です。</li>
<li>[エクスポート ファイル形式] ページで、<b>[個人情報の交換 &ndash; PKCS #12 (.PFX)]</b> を選択します。<b>[次へ]</b> をクリックします。</li>
<li><b>[パスワード]</b> ページで、秘密キーの暗号化時に使用するパスワードを入力し、確認のためにもう一度パスワードを入力します。<b>[次へ]</b> をクリックします。</li>
<li>ウィザードの指示に従って、証明書を .pfx 形式でエクスポートします。</li>
</ol>

<h3><a id="importcert"></a>VMM サーバーへの .pfx 証明書のインポート</h3>
<p>.pfx のコピーを VMM サーバーにエクスポートした後で、これをインポートします。VMM サーバーで MakeCert.exe を実行した場合は、そのサーバーに証明書をインポートする必要はありません。</p>
 
<ol>
<li>ローカル サーバーに秘密キー (.pfx) 証明書ファイルをコピーします。</li>
<li>証明書 MMC スナップインで <b>[コンピューター アカウント]</b> を選択し、<b>[次へ]</b> をクリックします。</li>
<li><b>[ローカル コンピューター]</b> を選択し、<b>[完了]</b> をクリックします。</li>
<li>MMC で <b>[証明書]</b> を展開し、<b>[個人用]</b> を右クリックしてから <b>[すべてのタスク] </b>をポイントします。次に <b>[インポート]</b> をクリックして、証明書のインポート ウィザードを開始します。</li>
<li>[インポートするファイル] ページで、<b>[参照]</b> をクリックし、インポートする証明書を含む .pfx 証明書ファイルが格納されたフォルダーを見つけます。適切なファイルを選択し、<b>[開く]</b> をクリックします。</li>
<li>[パスワード] ページの <b>[パスワード]</b> ボックスに、秘密キーが含まれる .pfx ファイルのパスワードを入力して、<b>[次へ]</b> をクリックします。</li>
<li>[証明書ストア] ページで、<b>[証明書をすべて次のストアに配置する]</b> を選択し、<b>[参照]</b> をクリックします。<b>[個人用]</b> ストアを選択し、<b>[OK]</b> をクリック後、<b>[次へ]</b> をクリックします。ウィザードを終了します。</li>
</ol>

これらのステップを完了した後、コンテナーの構成時にアップロードする .cer 証明書を選択し、Provider のインストール時に VMM サーバーを登録するときに .pfx 証明書を選択することができます。
</div>

<a name="vault"></a>

## 手順 2.コンテナーの作成

1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[データ サービス]** を展開し、**[Recovery Services]** を展開してから、**[Site Recovery コンテナー]** をクリックします。

3.  **[新規作成]** をクリックしてから、**[簡易作成]** をクリックします。

4.  **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5.  **[リージョン]** ボックスで、コンテナーのリージョンを選択します。利用可能なリージョンには、東アジア、西ヨーロッパ、米国西部、米国東部、北ヨーロッパ、東南アジアが含まれています。
6.  **[コンテナーの作成]** をクリックします。

    ![新しいコンテナー][新しいコンテナー]

コンテナーが正常に作成されたことを状態バーで確認します。コンテナーはメインの [Recovery Services] ページに **[アクティブ]** として一覧されます。

<a name="upload"></a>

## 手順 3.コンテナーの構成

1.  **[Recovery Services]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。このページはいつでもアイコンで開くことができます。

    ![クイック スタート アイコン][クイック スタート アイコン]

2.  **[Recovery のセットアップ]** ドロップダウン リストから、**[内部設置型サイトと Microsoft Azure 間]** を選択します。
3.  証明書 (.cer) をコンテナーにアップロードするために **[証明書の管理]** をクリックします。

    ![クイック スタート][クイック スタート]

4.  **[証明書の管理]** ダイアログ ボックスで、**[ファイルの参照]** をクリックして .cer ファイルを選択します。

    ![証明書の管理][証明書の管理]

5.  コンテナー用のキーを生成するために、**[コンテナー キーの取得]** をクリックします。キーが自動生成されます。キーを再生成すると前のキーは上書きされます。このキーは、後で Azure Site Recovery Provider を VMM サーバーにインストールするときに必要になります。

    ![証明書の管理][1]

<a name="download"></a>

## 手順 4.Azure Site Recovery Provider のインストール

1.  **[クイック スタート]** ページで **[Provider のダウンロード]** をクリックして、最新バージョンの Provider のインストール ファイルを取得します。

    ![Download Provider File][Download Provider File]

2.  ソース VMM サーバーでこのファイルを実行します。

    ![エージェントのダウンロード][エージェントのダウンロード]

3.  Provider がインストールされたら、サーバーをコンテナーに登録するセットアップを続行します。
    ![Setup Complete][Setup Complete]
4.  [インターネット接続] ページで、VMM サーバーで実行中の Provider がインターネットに接続する方法を指定します。**[次へ]** をクリックして、サーバーに構成されている既定のインターネット接続設定を使用します。
    ![Internet Settings][Internet Settings]
5.  [コンテナーの登録] ページで、以下の手順を実行します。

    -   VMM サーバーにインポートした秘密キー (.pfx) を選択します。
    -   サーバーに登録するコンテナーを選択します。
    -   コンテナー キーを指定します。これは、先ほど生成したコンテナーのキーです。[クイック スタート] ページからキーの値を切り取って貼り付けます。

    ![証明書の登録][証明書の登録]

6.  [データ暗号化] ページで、特定のクラウドのレプリケーション時にデータを暗号化するオプションを許可するかどうかを指定します。このオプションを選択すると、SSL 証明書が自動生成されます。この証明書は、フェールオーバーを実行するときに選択する必要があります。この設定を有効にすると、Azure Site Recovery ポータルで、クラウドのデータの暗号化を有効または無効にできます。このチュートリアルでは既定の設定のままにし、**[次へ]** をクリックします。

    ![証明書コンテナー][証明書コンテナー]

7.  [VMM サーバー] ページで、以下の手順を実行します。

    -   VMM サーバーの表示名を指定します。この名前は、Azure Site Recovery コンソールでサーバーを識別するために使用されます。
    -   VMM クラウドの情報を Azure Site Recovery コンテナーと同期するために、**[コンテナーとのクラウド メタデータの同期]** を選択します。この操作は、各サーバーで 1 回のみ実行する必要があります。すべてのクラウドを同期することを希望しない場合は、クラウドの保護設定を構成する前に、各クラウドを個別に発行して同期することができます。

8.  **[登録]** をクリックしてプロセスを完了します。

    ![PublishCloud][PublishCloud]

サーバーが正常に登録されると、その表示名が、コンテナーの [サーバー] ページの **[リソース]** タブに表示されます。

## <span id="storage"></span></a>手順 5.Azure Recovery Services Agent のインストール

保護する VMM クラウドに配置されている Hyper-V ホスト サーバーごとに、Azure Recovery Services Agent をインストールします。

1.  [クイック スタート] ページで、**[Azure Site Recovery Services Agent をダウンロードしてホストにインストールする]** をクリックして、最新バージョンの Agent のインストール ファイルを取得します。

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  保護する VMMM クラウドに配置されている Hyper-V ホスト サーバーごとにインストール ファイルを実行します。
3.  [前提条件のチェック] ページで **[次へ]** をクリックします。不足している前提条件があると自動的にインストールされます。

![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

1.  [インストール設定] ページで Agent のインストール先を指定し、バックアップのメタデータがインストールされるキャッシュの場所を選択します。その後、**[インストール]** をクリックします。

[インターネット接続] ページで、VMM サーバーで実行中の Provider がインターネットに接続する方法を指定します。**[次へ]** をクリックして、サーバーに構成されている既定のインターネット接続設定を使用します。![Recovery Services Agent location][Recovery Services Agent location]

## <span id="clouds"></span></a>手順 6.クラウドの保護設定の構成

VMM サーバーを登録した後、クラウドの保護設定を構成することができます。Provider のインストール時にオプション **[コンテナーとのクラウド データの同期]** を有効にしているため、コンテナーの **[保護された項目]** タブに VMM サーバー上のすべてのクラウドが表示されます。

![発行済みのクラウド][発行済みのクラウド]

以下の手順で保護設定を構成します。

1.  [クイック スタート] ページで、**[VMM クラウドの保護のセットアップ]** をクリックします。

![Enable cloud protection][Enable cloud protection]

1.  **[保護された項目]** タブで、構成するクラウドをクリックし、**[構成]** タブに移動します。
2.  **[ターゲット]** で、**[Microsoft Azure]** を選択します。
3.  **[ストレージ アカウント]** で、仮想マシンを格納する Azure ストレージを選択します。
4.  **[格納データの暗号化]** を **[オフ]** に設定します。この設定は、内部設置型サイトと Azure 間のレプリケートでデータを暗号化する必要があることを指定します。
5.  **[コピーの頻度]** で、既定の設定をそのまま使用します。この値は、ソースとターゲットの場所の間でデータが同期される頻度を指定します。
6.  **[保持する復旧ポイント]** で、既定の設定をそのまま使用します。既定値である 0 を使用する場合は、プライマリ仮想マシンに対応する最新の復旧ポイントのみが、レプリカのホスト サーバーに格納されます。
7.  **[アプリケーション整合性スナップショットの頻度]** で、既定の設定をそのまま使用します。この値は、スナップショットを作成する頻度を指定します。スナップショットは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。値を設定する場合は、構成する追加の復旧ポイント数よりも少ない値にしてください。

    ![クラウド構成][クラウド構成]

8.  **[レプリケーションの開始時刻]** で、Azure へのデータの初期レプリケーションを開始する時刻を指定します。Hyper-V ホスト サーバーのタイムゾーンが使用されます。初期レプリケーションはピーク時以外にスケジュールすることをお勧めします。

    ![Cloud replication settings][Cloud replication settings]

クラウド設定を保存すると、ジョブが作成され、これを **[ジョブ]** タブで監視できます。VMM ソース クラウド内のすべての Hyper-V ホスト サーバーのレプリケーションが構成されます。 保存後は、クラウド設定は **[構成]** タブで変更できます。ターゲットの場所またはターゲットのストレージを変更するには、クラウド構成を削除してから、クラウドを再構成する必要があります。ストレージ アカウントを変更する場合は、ストレージ アカウントの修正後に保護を有効にした仮想マシンにのみ、その変更が適用されることに注意してください。既存の仮想マシンは新しいストレージ アカウントに移行されません。

## <span id="networkmapping"></span></a>手順 7.ネットワーク マッピングの構成

ソースの VM ネットワークをターゲットの Azure 仮想ネットワークにマッピングするネットワーク マッピングをオプションで有効にできます。ネットワーク マッピングを作成しない場合は、同一の復旧計画でフェールオーバーする仮想マシンどうしのみ、Azure で通信できます。ネットワーク マッピングを作成すると、属している復旧計画の種類にかかわらず、同じネットワーク上でフェールオーバーするすべての仮想マシンが相互に通信できるようになります。さらに、ターゲットの Azure ネットワーク上にネットワーク ゲートウェイをセットアップすると、仮想マシンは内部設置型仮想マシンと通信できるようになります。このチュートリアルの一環としてネットワーク マッピングを構成する場合は、デプロイ ガイドの「[手順 4: ネットワーク マッピングの構成: オンプレミス間][手順 4: ネットワーク マッピングの構成: オンプレミス間]」を参照してください。

## <span id="virtualmachines"></span></a>手順 8.仮想マシンの保護の有効化

サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンで保護を有効にすることができます。

仮想マシンの保護を有効にする前に、必要に応じて、仮想マシンの設定を検証および更新します。たとえば、仮想マシン上のゲスト オペレーティング システムは、Windows Server 2008 以降または Linux でなければなりません。仮想マシンは、世代 1 のみでなければなりません。Azure Site Recovery のすべての要件の一覧については、計画ガイドの「[前提条件とサポート][前提条件とサポート]」を参照してください。

VMM コンソールで設定を確認および更新します。仮想マシンのプロパティの [全般] ページで、仮想マシンのオペレーティング システムの設定を変更します。[ハードウェア構成] ページで、オペレーティング システム ディスクの設定を更新します。

![Modify virtual machine properties][Modify virtual machine properties]

![Modify virtual machine properties][2]

1.  保護を有効にするために、仮想マシンが配置されているクラウドの **[仮想マシン]** タブで、**[保護を有効にする]** をクリックしてから **[仮想マシンを追加する]** を選択します。
2.  クラウド内の仮想マシンのリストから保護するマシンを選択します。

![Enable virtual machine protection][Enable virtual machine protection]

保護を有効にすると、2 つのジョブが作成されます。Enable Protection ジョブが実行されます。そして、初期レプリケーションが完了した後に、保護の最終処理が実行されます。仮想マシンは、これらのジョブが正常に完了してからフェールオーバーできる状態になります。ジョブの進捗状況は **[ジョブ]** タブで監視できます。

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>手順 9.復旧計画の構成と実行

復旧計画では、一括でのフェールオーバーを可能にするために、仮想マシンをグループにまとめます。復旧計画を作成するには、以下の手順を実行します。

1.  **[復旧計画]** タブで、**[作成]** をクリックします。
2.  [復旧計画の名前とターゲットを指定します] ページで、ソース VMM サーバーを選択し、ターゲットとして Azure を選択します。

    ![復旧計画の作成][復旧計画の作成]

3.  [仮想マシンの選択] ページで、復旧計画に追加する仮想マシンを選択します。保護が有効化された仮想マシンだけが表示されます。これらの仮想マシンが復旧計画の既定のグループ (グループ 1) に追加されます。
4.  チェック マークをクリックして、復旧計画を作成します。

    ![復旧計画の VM][復旧計画の VM]

### <span id="run"></span></a>フェールオーバーのテスト

復旧計画は、事前対応的なテストまたは計画されたフェールオーバーの一部として実行することも、計画されていないフェールオーバー時に実行することもできます。このチュートリアルでは、フェールオーバー戦略が予想どおりに動作するか検証するための、VMM から Azure へのテストのフェールオーバーを実行する方法を説明します。テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。以下の点に注意してください。

-   フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。
-   フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。

</p>
#### 

<p>
<span id="runtest"></span></a>フェールオーバーの実行

</h5>
復旧計画のテスト フェールオーバーを、次のように実行します。

</p>
1.  復旧計画を実行する前に、計画内の仮想マシンの設定を検証できます。検証するには、クラウドの [プロパティ] ページで仮想マシンをクリックします。[フェールオーバー] ページの [ソース] と [ターゲット] の各プロパティで、設定を確認します。 具体的には、Azure のターゲット仮想マシンの推奨サイズが正しいことと、ネットワーク設定が正確であることを確認します。仮想マシンのすべての前提条件の一覧については、「[前提条件とサポート][3]」を参照してください。

![Virtual Machine Properties][Virtual Machine Properties]

1.  **[復旧計画]** タブで、必要な復旧計画を選択します。
2.  フェールオーバーを開始するには、**[テスト フェールオーバー]** ボタンをクリックします。
3.  [テスト フェールオーバーの確認] ページで、テスト フェールオーバー後に仮想マシンが接続される Azure ネットワークを選択します。オプションで **[ネットワークなし]** を選択することができます。このように設定すると、選択された仮想マシンはフェールオーバー後にネットワークに接続されません。

![Test Failover][Test Failover]

テスト フェールオーバー ジョブの進行状況は、**[ジョブ]** タブで確認できます。テスト フェールオーバーが完了したら、以下の手順を実行します。

1.  仮想マシンが Azure で正常に起動することを確認します。
2.  **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
3.  **[ジョブ]** タブの詳細情報に加えて、復旧計画のテスト フェールオーバーを実行すると、そのプロセスが復旧計画の詳細ページに表示されます。フェールオーバーの各手順と状態を表示することができます。また、テスト フェールオーバーに関連するメモを作成して表示することができます。


<h4><a id="runtest"></a>アクティビティの監視</h5>

**[ジョブ]** タブと **[ダッシュボード]** を使用して、Azure Site Recovery コンテナーによって実行される主要なジョブを表示および監視することができます。ここには、クラウドの保護の構成、仮想マシンの保護の有効化と無効化、フェールオーバーの実行 (計画、計画外、テスト)、さらに計画外のフェールオーバーのコミットなどが含まれます。

**[ジョブ]** タブでは、ジョブを表示し、ジョブの詳細やエラーにドリルダウンします。また、ジョブのクエリを実行して指定した条件に一致するジョブを取得します。さらに、ジョブを Excel にエクスポートしたり、失敗したジョブを再開したりできます。

**[ダッシュボード]** では、Provider や Agent の最新バージョンのインストール ファイルをダウンロードしたり、コンテナーの構成情報を取得したりできます。また、コンテナーで保護管理された仮想マシンの数を確認することができ、さらに、最新のジョブの表示、コンテナーの証明書の管理、仮想マシンの再同期ができます。

ジョブとダッシュボードの操作の詳細については、「[Azure Site Recovery の管理および監視][4]」を参照してください。

## <span id="next"></span></a>次のステップ

-   完全な運用環境に Azure Site Recovery を計画、デプロイするには、「[Planning Guide for Azure Site Recovery (Azure Site Recovery の計画ガイド)][Plan for Azure Site Recovery Deployment (手順 5: 証明書の準備)]」および「[Azure Site Recovery のデプロイ: オンプレミス間の保護][Azure Site Recovery のデプロイ: オンプレミス間の保護]」を参照してください。
-   疑問がある場合は、「[Azure Recovery Services Forum (Azure 復旧サービス フォーラム)][Azure Recovery Services フォーラム]」にアクセスします。

  [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection (Azure の Hyper-V 回復マネージャーの構成)]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Plan for Azure Site Recovery Deployment (手順 5: 証明書の準備)]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Azure Site Recovery のデプロイ : オンプレミスと Azure 間の保護]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery の管理および監視]: http://go.microsoft.com/fwlink/?LinkId=378272
  [Azure Site Recovery のエラーの一般的なシナリオと解決策に関するページ]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services フォーラム]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Microsoft Azure 1 か月間無料評価版]: http://aka.ms/try-azure
  [Azure Site Recovery Pricing (Azure Site Recovery の価格設定)]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Microsoft Azure ストレージの概要]: http://go.microsoft.com/fwlink/?LinkId=398704
  [前提条件とサポート]: http://go.microsoft.com/fwlink/?LinkId=402602
  [手順 1.証明書の取得と構成]: #createcert
  [手順 2.コンテナーの作成]: #vault
  [手順 3.コンテナーの構成]: #upload
  [手順 4.Provider アプリケーションのインストール]: #download
  [手順 5.Agent アプリケーションのインストール]: #agent
  [手順 6.クラウドの保護の構成]: #clouds
  [手順 7.ネットワーク マッピングの構成]: #NetworkMapping
  [手順 8.仮想マシンの保護の有効化]: #virtualmachines
  [手順 9.復旧計画の構成と実行]: #recovery%20plans
  [チュートリアルで使用する自己署名証明書の取得]: #obtaincert
  [.pfx 形式での証明書のエクスポート]: #exportcert
  [VMM サーバーへの .pfx 証明書のインポート]: #importcert
  [Windows SDK]: http://go.microsoft.com/fwlink/?LinkId=378269
  [管理ポータル]: https://manage.windowsazure.com
  [新しいコンテナー]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [クイック スタート アイコン]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [クイック スタート]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [証明書の管理]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Download Provider File]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [エージェントのダウンロード]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup Complete]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [証明書の登録]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [証明書コンテナー]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Recovery Services Agent location]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [発行済みのクラウド]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Enable cloud protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [クラウド構成]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [手順 4: ネットワーク マッピングの構成: オンプレミス間]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [復旧計画の作成]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [復旧計画の VM]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Virtual Machine Properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Test Failover]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [4]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Azure Site Recovery のデプロイ: オンプレミス間の保護]: http://go.microsoft.com/fwlink/?LinkId=321295
