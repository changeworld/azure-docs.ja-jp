<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="作成して展開する方法" pageTitle="クラウド サービスを作成して展開する方法 - Azure" metaKeywords="Azure のクラウド サービスの作成, クラウド サービスの削除" description="Azure の簡易作成の方法によりクラウド サービスを作成して展開する方法について説明します。" metaCanonical="" services="cloud-services" documentationCenter="" title="クラウド サービスを作成して展開する方法" authors=""  solutions="" writer="ryanwi" manager="" editor=""  />





#クラウド サービスを作成して展開する方法

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Azure の管理ポータルには、クラウド サービスを作成して展開する方法が 2 つ用意されています (**[簡易作成]** と **[カスタム作成]**)。

このトピックでは、簡易作成の方法を使って新しいクラウド サービスを作成し、その後、**[アップロード]** を使用して Azure にクラウド サービス パッケージをアップロードして展開する方法について説明します。この方法を使うと、Azure の管理ポータルに、必要な事項をすべて完了するのに便利なリンクが操作の進行につれて表示されます。クラウド サービスの作成時に展開する準備が整っている場合は、**[カスタム作成]** を使用して作成と展開を同時に実行できます。

**注:**   Windows Team Foundation Service (TFS) からクラウド サービスを発行する予定の場合は、[簡易作成] を使用した後、**[クイック スタート]** またはダッシュボードから TFS 発行を設定する必要があります。詳細については、[Team Foundation Service Preview を使用した Azure への継続的な配信に関するページ][TFSTutorialForCloudService]を参照するか、**[クイック スタート]** ページのヘルプを参照してください。

##目次##

* [概念](#concepts)
* [アプリケーションの準備](#prepare)
* [開始する前に](#begin)
* [方法: 簡易作成によるクラウド サービスの作成](#quick)
* [方法: クラウド サービスの証明書のアップロード](#uploadcertificate)
* [方法: クラウド サービスの展開](#deploy)


<h2><a id="concepts"></a>概念</h2>
Azure のクラウド サービスとしてアプリケーションを展開するには、3 つのコンポーネントが必要です。

>- **サービス定義ファイル**   クラウド サービス定義ファイル (.csdef) は、ロールの数も含めて、サービス モデルを定義します。

>- **サービス構成ファイル**   クラウド サービス構成ファイル (.cscfg) は、ロール インスタンスの数も含めて、クラウド サービスおよび個々のロールの構成設定を指定します。

>- **サービス パッケージ**   サービス パッケージ (.cspkg) は、アプリケーション コードとサービス定義ファイルを含みます。

<h2><a id="prepare"></a>アプリケーションの準備</h2>
クラウド サービスを展開する前に、アプリケーション コードとクラウド サービス構成ファイル (.cscfg) からクラウド サービス パッケージ (.cspkg) を作成する必要があります。各クラウド サービス パッケージは、アプリケーション ファイルと構成を含みます。サービス構成ファイルは構成設定を指定します。

Azure SDK には、こういった必須の展開ファイルを準備するためのツールが用意されています。SDK は、[Azure のダウンロード](http://www.windowsazure.com/ja-jp/develop/downloads/) ページからアプリケーション コードの開発に使用する言語でインストールできます。

クラウド サービスに慣れていない場合は、[Azure Code Samples](http://code.msdn.microsoft.com/windowsazure/) からサンプルのクラウド サービス パッケージ (.cspkg) とクラウド サービス構成ファイル (.cscfg) をダウンロードして利用することができます。

サービス パッケージをエクスポートする前に、以下の 3 つのクラウド サービス機能について特別な構成が必要です。

- データ暗号化のために Secure Sockets Layer (SSL) を使用するクラウド サービスを展開する場合は、アプリケーションを SSL 用に構成します。詳細については、「[HTTPS エンドポイントでの SSL 証明書の構成](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff795779.aspx)」を参照してください。

- ロール インスタンスに対するリモート デスクトップ接続を構成する場合は、ロールをリモート デスクトップ用に構成します。リモート アクセス用のサービス定義ファイルを準備する方法の詳細については、「[Overview of Setting Up a Remote Desktop Connection for a Role (ロールのリモート デスクトップ接続をセットアップする手順の概要)](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433010.aspx)」を参照してください。

- クラウド サービスの詳細監視を構成する場合は、クラウド サービスの Azure 診断を有効にします。*最小監視* (既定の監視レベル) では、ロール インスタンス (仮想マシン) のホスト オペレーティング システムから収集したパフォーマンス カウンターが使用されます。"詳細監視*は、ロール インスタンス内のパフォーマンス データに基づいて追加のメトリックを収集して、アプリケーション処理時に発生する問題を詳しく分析できます。Azure 診断を有効にする方法については、「[Enabling Diagnostics in Azure (Azure における診断の有効化)](http://www.windowsazure.com/ja-jp/develop/net/common-tasks/diagnostics/)」を参照してください。


<h2><a id="begin"></a>開始する前に</h2>

- Azure SDK をまだインストールしていない場合は、**[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ](http://www.windowsazure.com/ja-jp/develop/downloads/)を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします (これは後でも実行する機会があります)。

- 証明書を必要とするロール インスタンスがある場合は、証明書を作成します。クラウド サービスには、秘密キーのある .pfx ファイルが必要です。証明書は、クラウド サービスを作成して展開するときに、Azure にアップロードできます。証明書の作成方法の詳細については、「[HTTPS エンドポイントでの SSL 証明書の構成](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff795779.aspx)」を参照してください。

- クラウド サービスをアフィニティ グループに展開する予定である場合は、アフィニティ グループを作成します。アフィニティ グループを使用すると、自分のクラウド サービスおよびその他の Azure サービスをリージョンの同じ場所に展開することができます。アフィニティ グループは、管理ポータルの **[ネットワーク]** 領域の **[アフィニティ グループ]** ページで作成できます。詳細については、**[アフィニティ グループ]** ページのヘルプを参照してください。


<h2><a id="quick"></a>方法: 簡易作成によるクラウド サービスの作成</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[新規]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. **[URL]** ボックスに、運用展開のクラウド サービスにアクセスするパブリック URL で使用するサブドメイン名を入力します。運用展開の URL 形式は、http://*myURL*.cloudapp.net です。

3. **Region/Affinity Group** で、クラウド サービスを展開するリージョンまたはアフィニティ グループを選択します。リージョン内の他の Azure のサービスと同じ場所にクラウド サービスを展開する場合は、アフィニティ グループを選択します。

	> [WACOM.NOTE]
	> アフィニティ グループを作成するには、管理ポータルの **[ネットワーク]** 領域を開き、**[アフィニティ グループ]** をクリックして、**[新しいアフィニティ グループの作成]** または **[作成]** をクリックします。以前の Azure の管理ポータルで作成したアフィニティ グループを使用できます。また、Azure サービス管理 API を使用してアフィニティ グループを作成し管理することもできます。詳細については、「[アフィニティ グループに対する操作](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460798.aspx)」を参照してください。

4. **[クラウド サービスを作成する]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	**[クラウド サービス]** 領域が開き、新しいクラウド サービスが表示されます。状態が [作成済み] に変わると、クラウド サービスの作成が正常に完了しています。

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

	Secure Sockets Layer (SSL) によるデータ暗号化のために証明書を必要とするロールがクラウド サービスにあり、証明書がまだ Azure にアップロードされていない場合は、クラウド サービスを展開する前に証明書をアップロードする必要があります。証明書をアップロードすると、ロール インスタンスで実行されているどの Windows アプリケーションも証明書にアクセスできるようになります。


<h2><a id="uploadcertificate"></a>方法: クラウド サービスの証明書のアップロード</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[クラウド サービス]** をクリックします。目的のクラウド サービスの名前をクリックして、ダッシュボードを開きます。

	![CloudServices_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. **[証明書]** をクリックします。下図のような **[証明書]** ページが開きます。

	![CloudServices_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3. **[新しい証明書の追加]** または **[アップロード]** をクリックします。
 **[証明書の追加]** が開きます。

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4. **[証明書ファイル]** で、**[参照]** を使って、使用する証明書 (.pfx ファイル) を選択します。

5. **[パスワード]** ボックスに、証明書の秘密キーを入力します。

6. [OK] (チェックマーク) をクリックします。

	アップロードの進捗状況は下図のメッセージ領域で監視できます。アップロードが完了すると、証明書がテーブルに追加されます。メッセージ領域で、下向き矢印をクリックしてメッセージを閉じるか、[X] をクリックしてメッセージを削除します。

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

	クラウド サービスの展開は、ダッシュボードまたは **[クイック スタート]** から実行できます。

<h2><a id="deploy"></a>方法: クラウド サービスの展開</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[クラウド サービス]** をクリックします。目的のクラウド サービスの名前をクリックして、ダッシュボードを開きます。

2. **[クイック スタート]** (**[ダッシュボード]** の左側にあるアイコン) をクリックして、下図のような **[クイック スタート]** ページを開きます (クラウド サービスは、ダッシュボードの **[アップロード]** を使用して展開することもできます)。

	![CloudServices_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3. Azure SDK をまだインストールしていない場合は、**[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ](http://www.windowsazure.com/ja-jp/develop/downloads/)を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。

	ダウンロード ページでは、Node.js や Java、PHP などの言語で Web アプリケーションを開発するためのクライアント ライブラリやソース コードをインストールして、拡張性の高い Azure のクラウド サービスとして展開することもできます。

	> [WACOM.NOTE]
	> 以前に作成したクラウド サービス (旧称は*ホステッド サービス*) の場合、仮想マシン (ロール インスタンス) のゲスト オペレーティング システムが、インストールする Azure SDK バージョンと互換性があることを確認する必要があります。詳細については、「[Azure SDK .NET リリース ノート](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh552718.aspx)」を参照してください。

4. **[新規運用環境の展開]** または **[新規ステージング環境の展開]** をクリックします。

	運用環境に展開する前に Azure でクラウド サービスをテストする場合は、ステージング環境に展開することができます。ステージング環境の場合、URL ではクラウド サービスのグローバル一意識別子 (GUID) によってクラウド サービスが識別されます (*GUID*.cloudapp.net)。運用環境の場合、もっとわかりやすい割り当てた DNS プレフィックスが使用されます (たとえば、*myservice*.cloudapp.net)。ステージング環境のクラウド サービスを運用環境に昇格する準備ができたら、**[スワップ]** を使用してクライアントのリクエストをその展開にリダイレクトします。

	展開環境を選択すると、**[パッケージのアップロード]** が開きます。

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
 
5. **[展開名]** ボックスに、新しい展開の名前を入力します (たとえば、MyCloudServicev1)。

6. **[パッケージ]** で、**[参照]** を使って、使用するサービス パッケージ ファイル (.cspkg) を選択します。

7. **[構成]** で、**[参照]** を使って、使用するサービス構成ファイル (.cscfg) を選択します。

8. クラウド サービスにインスタンスが 1 つしかないロールがある場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でも展開する]** チェック ボックスをオンにして、展開を有効にして続行します。

 Azure でクラウド サービスの保守中およびサービス更新中に 99.95% のアクセスを保証できるのは、あらゆるロールに少なくとも 2 つのインスタンスがある場合だけです。必要な場合、クラウド サービスを展開した後に、**[スケール]** ページで、ロール インスタンスを追加できます。詳細については、「[サービス レベル アグリーメント](http://www.windowsazure.com/ja-jp/support/legal/sla/)」を参照してください。

9. [OK] (チェックマーク) をクリックして、クラウド サービスの展開を開始します。

	展開の状態は、メッセージ領域で監視できます。下向き矢印をクリックすると、メッセージが表示されなくなります。

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

###展開が正常に完了したことを確認するには###

1. **[ダッシュボード]** をクリックします。

2. **[概要]** で、サイト URL をクリックして、Web ブラウザーでクラウド サービスを開きます。

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


