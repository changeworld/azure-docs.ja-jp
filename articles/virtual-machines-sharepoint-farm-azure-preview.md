<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# SharePoint Server Farm

Microsoft Azure プレビュー ポータルは、SharePoint Server Farm を使用して、事前構成された SharePoint Server 2013 ファームを作成します。これにより、開発またはテストの環境で基本または高可用性の SharePoint ファームが必要な場合や、組織のコラボレーション ソリューションとして SharePoint Server 2013 を評価する場合の所要時間を大きく節約できます。

基本的な SharePoint ファームは、次に示す構成の 3 台の仮想マシンで構成されます。

![sharepointfarm][]

このファーム構成は、SharePoint アプリ開発の簡略化セットアップまたは、SharePoint 2013 の初回の評価で使用することができます。

高可用性 SharePoint ファームは、次に示す構成の 9 台の仮想マシンで構成されます。

![sharepointfarm][1]

このファーム構成は、より大量のクライアント読み込みや外部の SharePoint サイトの高可用性をテストしたり、SharePoint ファームの SQL Server AlwaysOn をテストしたりする場合に使用できます。この構成は、高可用環境での SharePoint アプリケーション開発でも使用できます。

この両ファームの構成の詳細については、「[SharePoint Server Farm Configuration Details (SharePoint Server ファームの構成の詳細)][]」を参照してください。

このファームには事前構成されたエンドポイントがあり、これによってインターネットで接続したクライアント コンピューター経由で SharePoint Web サーバーまでの認証済み Web トラフィック (TCP ポート 80) が許可されます。このエンドポイントは、事前構成されたチーム サイトまでのものです。このチーム サイトにアクセスするには、最初に、SharePoint ファームの Web サーバーのドメイン名を次の手順で取得する必要があります。

1.  Azure プレビュー ポータルの **[参照]** をクリックし、次に **[仮想マシン]** をクリックします。
2.  仮想マシンの一覧で、使用する Web サーバーの名前 (最後が SP、WEB1、または WEB2) をクリックします。
3.  Web サーバーの仮想マシンのウィンドウで、**[プロパティ]** をクリックします。
4.  **[ドメイン名]** の完全修飾ドメイン名をメモしておきます。
5.  インターネット ブラウザーで URL **[http://[FQDN]][]** にアクセスします。たとえば、spfarm1-web1-contoso.cloudpapp.net というドメイン名の場合、URL は **<http://spfarm1-web1-contoso.cloudpapp.net>** です。
6.  プロンプトが表示されたら、ファームの作成時に指定したユーザー アカウントの資格情報を入力します。

SharePoint のサーバーの全体管理サイトで [My Sites]、SharePoint アプリケーション、その他の機能を構成できます。詳細については、「[SharePoint 2013 を構成する][]」を参照してください。SharePoint のサーバー全体管理サイトにアクセスするには、最初に外部のポート番号を取得する必要があります。

1.  Azure プレビュー ポータルの **[参照]** をクリックし、次に **[仮想マシン]** をクリックします。
2.  仮想マシンの一覧で、基本構成の SharePoint サーバーの名前 (最後が SP) か、高可用構成のアプリケーション サーバーの名前 (最後が APP1 または APP2) をクリックします。
3.  仮想マシンのウィンドウで、**[プロパティ]** をクリックします。
4.  **[ドメイン名]** の完全修飾ドメイン名をメモしておきます。
5.  **[プロパティ]** ウィンドウを閉じます。
6.  仮想マシンのウィンドウで、**[エンドポイント]** の一覧まで下方向へスクロールします。
7.  SPCentralAdmin と名付けられたエンドポイントのポート番号をメモしておきます。
8.  インターネット ブラウザーで、URL **[http://[FQDN]:[ポート番号]** にアクセスします。たとえば、ドメイン名が spfarm1-app1-contoso.cloudpapp.net で外部ポート番号が 54398 の場合、URL は **<http://spfarm1-app1-contoso.cloudpapp.net:54398>** です。
9.  プロンプトが表示されたら、ファームの作成時に指定したユーザー アカウントの資格情報を入力します。

注:

-   Azure プレビュー ポータルは、これら仮想マシンをサブスクリプション内に作成します。
-   Azure プレビュー ポータルは、インターネットに接続する Web プレゼンスを持つクラウド専用の仮想ネットワークにこれら両ファームを作成します。組織のネットワークに戻るサイト間 VPN 接続はありません。
-   これらのサーバーはリモート デスクトップ接続を通じて管理できます。

## 構成の手順

SharePoint Farm を使用して SharePoint ファームを作成するには、以下の手順を実行します。

1.  [Microsoft Azure プレビュー ポータル][]で、**[仮想マシン]**、**[SharePoint Server Farm]** の順にクリックします。
2.  **[SharePoint ファームの作成]** ウィンドウにリソース グループの名前を入力します。
3.  ファームの各仮想マシンのローカル管理者アカウントのユーザー名とパスワードを入力します。推測しにくい名前とパスワードを選択してください。
4.  高可用性ファームを希望する場合は、**[高可用性を有効にする]** をクリックします。
5.  ドメイン コントローラーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、フォレスト ルート ドメイン名 (既定は contoso.com)、およびドメイン コントローラーのサイズ (既定は A1) を指定できます。
6.  SQL サーバーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、SQL サーバーのサイズ (既定は A5)、データベース アクセス アカウント名とパスワード (既定では管理者アカウントを使用)、SQL サーバーのサービス アカウント名 (既定は sqlservice)、パスワード (既定では管理者アカウントと同じパスワードを使用) を指定できます。
7.  SharePoint サーバーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、SharePoint サーバーのサイズ (既定は A23)、SharePoint ユーザー アカウント (既定は sp\_setup) とパスワード、SharePoint ファームのアカウント名 (既定は sp\_farm) とパスワード、および SharePoint ファームのパスフレーズを指定できます。既定では、管理者のパスワードを SharePoint のユーザー アカウント、ファーム アカウント、パスフレーズに使用します。
8.  オプションの構成 (仮想ネットワークまたはストレージ アカウント、診断) を構成するには矢印をクリックします。
9.  サブスクリプションを指定するには、矢印をクリックします。
10. 場所 (リージョン) を指定するには、矢印をクリックします。
11. 準備ができたら **[作成]** をクリックします。

## Azure リソース マネージャー

SharePoint Server Farm は、Azure リソース マネージャーとスクリプトを使用して、SharePoint ファームのインフラストラクチャとサーバー構成を自動作成します。詳細については、「[リソース マネージャーでの Windows PowerShell の使用][]」を参照してください。

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [SharePoint Server Farm Configuration Details (SharePoint Server ファームの構成の詳細)]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  [SharePoint 2013 を構成する]: http://technet.microsoft.com/library/ee836142.aspx
  [Microsoft Azure プレビュー ポータル]: https://portal.azure.com/
  [リソース マネージャーでの Windows PowerShell の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/powershell-azure-resource-manager/
