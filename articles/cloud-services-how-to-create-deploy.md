<properties urlDisplayName="How to create and deploy" pageTitle="クラウド サービスを作成してデプロイする方法 - Azure" metaKeywords=" Azure でのクラウド サービスの作成方法, クラウド サービスの削除方法" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />





#クラウド サービスを作成してデプロイする方法

Azure の管理ポータルには、クラウド サービスを作成してデプロイする方法が 2 つ用意されています。**簡易作成**と**カスタム作成**です。 

このトピックでは、簡易作成の方法を使って新しいクラウド サービスを作成し、その後、**[アップロード]** を使用して Azure にクラウド サービス パッケージをアップロードしてデプロイする方法について説明します。この方法を使うと、Azure の管理ポータルに、必要な事項をすべて完了するのに便利なリンクが操作の進行につれて表示されます。クラウド サービスの作成時に展開する準備が整っている場合は、**[カスタム作成]** を使用して作成と展開を同時に実行できます。 

**注:**   Windows Team Foundation Service (TFS) からクラウド サービスを発行する予定の場合は、[簡易作成] を使用した後、**[クイック スタート]** またはダッシュボードから TFS 発行を設定する必要があります。詳細については、「[Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信に関するページ)][」TFSTutorialForCloudService] を参照するか、**[クイック スタート]** ページのヘルプを参照してください。

##目次##

* [概念](#concepts)
* [アプリケーションの準備](#prepare)
* [開始する前に](#begin)
* [方法: 簡易作成によるクラウド サービスの作成](#quick)
* [方法: クラウド サービスの証明書のアップロード](#uploadcertificate)
* [方法: クラウド サービスのデプロイ](#deploy)


<h2><a id="concepts"></a>概念</h2>
Azure のクラウド サービスとしてアプリケーションを展開するには、3 つのコンポーネントが必要です。

>- **サービス定義ファイル** - クラウド サービス定義ファイル (.csdef) は、ロールの数も含めて、サービス モデルを定義します。

>- **サービス構成ファイル** - クラウド サービス構成ファイル (.cscfg) は、ロール インスタンスの数も含めて、クラウド サービスおよび個々のロールの構成設定を指定します。

>- **サービス パッケージ** - サービス パッケージ (.cspkg) は、アプリケーション コードとサービス定義ファイルを含みます。

<h2><a id="prepare"></a>アプリケーションの準備</h2>
クラウド サービスをデプロイする前に、アプリケーション コードとクラウド サービス構成ファイル (.cscfg) からクラウド サービス パッケージ (.cspkg) を作成する必要があります。各クラウド サービス パッケージは、アプリケーション ファイルと構成を含みます。サービス構成ファイルは構成設定を指定します。

Azure SDK には、こういった必須の展開ファイルを準備するためのツールが用意されています。SDK は、[Azure のダウンロード](http://www.windowsazure.com/ja-jp/develop/downloads/) ページからアプリケーション コードの開発に使用する言語でインストールできます。

サービス パッケージをエクスポートする前に、以下の 3 つのクラウド サービス機能について特別な構成が必要です。

- データ暗号化のために Secure Sockets Layer (SSL) を使用するクラウド サービスをデプロイする場合は、アプリケーションを SSL 用に構成します。詳細については、「[How to Configure an SSL Certificate on an HTTPS Endpoint (HTTPS エンドポイント上での SSL 証明書の構成方法)](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff795779.aspx)」を参照してください。

- ロール インスタンスに対するリモート デスクトップ接続を構成する場合は、ロールをリモート デスクトップ用に構成します。リモート アクセス用のサービス定義ファイルを準備する方法の詳細については、「[Azure ロールのリモート デスクトップ接続をセットアップする](http://msdn.microsoft.com/ja-jp/library/hh124107.aspx)」を参照してください。

- クラウド サービスの詳細監視を構成する場合は、クラウド サービスの Azure 診断を有効にします。*最小監視* (既定の監視レベル) では、ロール インスタンス (仮想マシン) のホスト オペレーティング システムから収集したパフォーマンス カウンターが使用されます。詳細監視は、ロール インスタンス内のパフォーマンス データに基づいて追加のメトリックを収集して、アプリケーション処理時に発生する問題を詳しく分析できます。Azure 診断を有効にする方法については、「[Enabling Diagnostics in Azure (Azure における診断の有効化)](http://www.windowsazure.com/ja-jp/develop/net/common-tasks/diagnostics/)」を参照してください。

- Web ロールまたはワーカー ロールのデプロイメントを伴うクラウド サービスを作成するには、サービス パッケージを作成する必要があります。パッケージに関連したファイルの詳細については、「[Azure のクラウド サービスのセットアップ](http://msdn.microsoft.com/ja-jp/library/hh124108.aspx)」を参照してください。パッケージ ファイルの作成については、「[Azure アプリケーションのパッケージ化](http://msdn.microsoft.com/ja-jp/library/hh403979.aspx)」を参照してください。Visual Studio を使用してアプリケーションを開発する場合は、「[Azure Tools を使用したクラウド サービスの発行](http://msdn.microsoft.com/ja-jp/library/ff683672.aspx)」を参照してください。

<h2><a id="begin"></a>開始する前に</h2>

- Azure SDK をまだインストールしていない場合は、**[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ](http://www.windowsazure.com/ja-jp/develop/downloads/)を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。(これは後でも実行する機会があります)。

- 証明書を必要とするロール インスタンスがある場合は、証明書を作成します。クラウド サービスには、秘密キーのある .pfx ファイルが必要です。証明書は、クラウド サービスを作成して展開するときに、Azure にアップロードできます。証明書の詳細については、「[証明書の管理](http://msdn.microsoft.com/ja-jp/library/gg981929.aspx)」を参照してください。

- クラウド サービスをアフィニティ グループにデプロイする予定である場合は、アフィニティ グループを作成します。アフィニティ グループを使用すると、自分のクラウド サービスおよびその他の Azure サービスをリージョンの同じ場所に展開することができます。アフィニティ グループは、管理ポータルの **[ネットワーク]** 領域の **[アフィニティ グループ]** ページで作成できます。詳細については、「[管理ポータルでのアフィニティ グループの作成](http://msdn.microsoft.com/ja-jp/library/jj156209.aspx)」を参照してください。


<h2><a id="quick"></a>方法: 簡易作成によるクラウド サービスの作成</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[新規]**、**[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. **[URL]** ボックスに、運用デプロイのクラウド サービスにアクセスするパブリック URL で使用するサブドメイン名を入力します。運用デプロイの URL 形式は、http://*myURL*.cloudapp.net.

3. **[リージョン/アフィニティ グループ]** で、クラウド サービスを展開するリージョンまたはアフィニティ グループを選択します。リージョン内の他の Azure サービスと同じ場所にクラウド サービスを展開する場合は、アフィニティ グループを選択します。

4. **[クラウド サービスを作成する]** をクリックします。

	![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	**[クラウド サービス]** 領域が開き、新しいクラウド サービスが表示されます。状態が [作成済み] に変わると、クラウド サービスの作成が正常に完了しています。

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


<h2><a id="uploadcertificate"></a>方法: クラウド サービスの証明書のアップロード</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[クラウド サービス]** をクリックし、クラウド サービスの名前をクリックして、**[証明書]** をクリックします。

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. **[証明書をアップロードします]** または **[アップロード]** をクリックします。

3. **[ファイル]** の **[参照]** を使用して、証明書ファイル (.pfx file) を選択します。

4. **[パスワード]** ボックスに、証明書の秘密キーを入力します。

5. **[OK]** (チェックマーク) をクリックします。

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	アップロードの進捗状況は下図のメッセージ領域で監視できます。アップロードが完了すると、証明書がテーブルに追加されます。メッセージ領域で [OK] をクリックし、メッセージを閉じます。

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

<h2><a id="deploy"></a>方法: クラウド サービスのデプロイ</h2>

1. [管理ポータル](http://manage.windowsazure.com/)で、**[クラウド サービス]** をクリックし、クラウド サービスの名前をクリックして、**[ダッシュボード]** をクリックします。

	運用環境でダッシュボードが開きます。この時点で、[ステージング] を選択すると、アプリケーションをステージング環境にデプロイすることもできます。詳細については、「[Azure での配置の管理](http://msdn.microsoft.com/ja-jp/library/gg433027.aspx)」を参照してください。

	 
2. **[新しい運用環境のデプロイをアップロードします]** または **[アップロード]** をクリックします。

3. **[デプロイ名]** ボックスに、新しいデプロイの名前を入力します (たとえば、MyCloudServicev4)。

3. **[パッケージ]** で、**[参照]** を使って、使用するサービス パッケージ ファイル (.cspkg) を選択します。

4. **[構成]** で、**[参照]** を使って、使用するサービス構成ファイル (.cscfg) を選択します。

5. クラウド サービスにインスタンスが 1 つしかないロールがある場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイします]** チェック ボックスをオンにして、デプロイを有効にして続行します。

Azure でクラウド サービスの保守中およびサービス更新中に 99.95% のアクセスを保証できるのは、あらゆるロールに少なくとも 2 つのインスタンスがある場合だけです。必要な場合、クラウド サービスを展開した後に、**[スケール]** ページで、ロール インスタンスを追加できます。詳細については、「[サービス レベル アグリーメント](http://www.windowsazure.com/ja-jp/support/legal/sla/)」を参照してください。

6. **[OK]** (チェックマーク) をクリックして、クラウド サービスのデプロイを開始します。

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
 

	デプロイの状態は、メッセージ領域で監視できます。メッセージを非表示にするには、[OK] をクリックします。

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

###展開が正常に完了したことを確認するには###

1. **[ダッシュボード]** をクリックします。

	サービスのステータスが、**実行中**になっていることを確認します。

2. **[概要]** で、サイト URL をクリックして、Web ブラウザーでクラウド サービスを開きます。

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

