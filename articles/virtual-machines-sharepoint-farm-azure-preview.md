<properties 
	pageTitle="SharePoint Server Farm" 
	description="Azure プレビュー ポータルで利用できる SharePoint Server ファームの新機能について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="josephd"/>

# SharePoint Server Farm

Microsoft Azure プレビュー ポータルは、SharePoint Server Farm を使用して、事前構成された SharePoint Server 2013 ファームを作成します。これにより、開発またはテストの環境で基本または高可用性の SharePoint ファームが必要な場合や、組織のコラボレーション ソリューションとして SharePoint Server 2013 を評価する場合の所要時間を大きく節約できます。

この構成では、次の 3 つの仮想マシンの基本的な SharePoint ファームで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

このファーム構成は、SharePoint アプリ開発の簡略化セットアップまたは、SharePoint 2013 の初回の評価で使用することができます。

高可用性 SharePoint ファームは、次に示す構成の 9 台の仮想マシンで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

このファーム構成は、より大量のクライアント読み込みや外部の SharePoint サイトの高可用性をテストしたり、SharePoint ファームの SQL Server AlwaysOn をテストしたりする場合に使用できます。この構成は、高可用環境での SharePoint アプリケーション開発でも使用できます。
 
この両ファームの構成の詳細については、「[SharePoint Server Farm Configuration Details (SharePoint Server ファームの構成の詳細)](virtual-machines-sharepoint-farm-config-azure-preview.md)」を参照してください。

## 構成の手順
 
SharePoint Server Farm のテンプレートを使用して SharePoint ファームを作成するには、以下の手順を実行します。

1. [Microsoft Azure プレビュー ポータル](https://portal.azure.com/)で、**[新規]**、**[コンピューティング]**、**[SharePoint Server Farm]** の順にクリックします。**SharePoint Server Farm** が表示されない場合、**[新規]**、**[コンピューティング]**、**[Azure Marketplace]** の順にクリックして、**[すべてを検索]** に「**SharePoint**」と入力してから、**[SharePoint Server Farm]** をクリックします。 
2. **[SharePoint ファームの作成]** ウィンドウにリソース グループの名前を入力します。
3. ファームの各仮想マシンのローカル管理者アカウントのユーザー名とパスワードを入力します。推測されにくい名前とパスワードを選んで登録し、安全な場所に保管します。
4. 高可用性ファームを希望する場合は、**[高可用性を有効にする]** をクリックします。
5. ドメイン コントローラーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、フォレスト ルート ドメイン名 (既定は contoso.com)、およびドメイン コントローラーのサイズ (既定は A1) を指定できます。
6. SQL サーバーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、SQL サーバーのサイズ (既定は A5)、データベース アクセス アカウント名とパスワード (既定では管理者アカウントを使用)、SQL サーバーのサービス アカウント名 (既定は sqlservice)、パスワード (既定では管理者アカウントと同じパスワードを使用) を指定できます。
7. SharePoint サーバーを構成するには、矢印をクリックします。ホスト名プレフィックス (既定はリソース グループ名)、SharePoint サーバーのサイズ (既定は A2)、SharePoint ユーザー アカウント (既定は sp_setup) とパスワード、SharePoint ファームのアカウント名 (既定は sp_farm) とパスワード、SharePoint ファームのパスフレーズを指定できます。既定では、管理者のパスワードを SharePoint のユーザー アカウント、ファーム アカウント、パスフレーズに使用します。
8. 仮想ネットワーク、ストレージ アカウント、診断のオプションの構成設定を構成するには、対応する矢印をクリックします。
9. サブスクリプションを指定するには、矢印をクリックします。
10. 準備ができたら **[作成]** をクリックします。

> [AZURE.NOTE]ドメイン コントローラーには、既定で、Active Directory 管理ツールがインストールされていません。管理ツールをインストールするには、ドメイン コントローラーの仮想マシンで、管理者レベルの Windows PowerShell コマンド プロンプトから **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** コマンドを実行します。

## SharePoint ファームにアクセスして管理を開始します。

この SharePoint ファームには事前構成されたエンドポイントがあり、インターネット接続されたクライアント コンピューターの SharePoint Web サーバーに対して、未認証の Web トラフィック (TCP ポート 80) が許可されます。このエンドポイントは、事前構成されたチーム サイトまでのものです。このチームサイトにアクセスするには:

1.	Azure プレビュー ポータルの **[参照]** をクリックし、**[リソース グループ]** をクリックします。 
2.	リソース グループの一覧で、SharePoint ファーム リソース グループの名前をクリックします。
3.	SharePoint ファーム リソース グループのウィンドウで、**[デプロイ履歴]** をクリックします。 
4.	**[デプロイ履歴]** ウィンドウで、**[Microsoft.SharePointFarm]** をクリックします。
5.	**[Microsoft.SharePointFarm]** ウィンドウで、[SHAREPOINTSITEURL] フィールドの URL を選択してコピーします。 
6.	その URL をインターネット ブラウザーのアドレス フィールドに貼り付けます。
7.	プロンプトが表示されたら、ファームの作成時に指定したユーザー アカウントの資格情報を入力します。

SharePoint のサーバーの全体管理サイトで [My Sites]、SharePoint アプリケーション、その他の機能を構成できます。詳細については、「[SharePoint 2013 を構成する](http://technet.microsoft.com/library/ee836142.aspx)」を参照してください。SharePoint サーバーの全体管理サイトにアクセスするには:

1.	Azure プレビュー ポータルの **[参照]** をクリックし、**[リソース グループ]** をクリックします。 
2.	リソース グループの一覧で、SharePoint ファーム リソース グループの名前をクリックします。
3.	SharePoint ファーム リソース グループのウィンドウで、**[デプロイ履歴]** をクリックします。 
4.	**[デプロイ履歴]** ウィンドウで、**[Microsoft.SharePointFarm]** をクリックします。
5.	**[Microsoft.SharePointFarm]** ウィンドウで、[SHAREPOINTCENTRALADMINURL] フィールドの URL を選択してコピーします。 
6.	その URL をインターネット ブラウザーのアドレス フィールドに貼り付けます。
7.	プロンプトが表示されたら、ファームの作成時に指定したユーザー アカウントの資格情報を入力します。


注:

- Azure プレビュー ポータルは、これら仮想マシンをサブスクリプション内に作成します。
- Azure プレビュー ポータルは、インターネットに接続する Web プレゼンスを持つクラウド専用の仮想ネットワークにこれら両ファームを作成します。組織のネットワークに戻るサイト間 VPN 接続や ExpressRoute 接続はありません。 
- これらのサーバーはリモート デスクトップ接続を通じて管理できます。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)」を参照してください。


## Azure リソース マネージャー

SharePoint Server Farm は、Azure リソース マネージャーとスクリプトを使用して、SharePoint ファームのインフラストラクチャとサーバー構成を自動作成します。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。

## その他のリソース

[SharePoint サーバー ファームの構成の詳細](virtual-machines-sharepoint-farm-config-azure-preview.md)

[Azure インフラストラクチャ サービスでの SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

<!---HONumber=58-->