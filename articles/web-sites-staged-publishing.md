<properties
	pageTitle="Azure App Service の Web アプリのステージング環境を設定する"
	description="Azure App Service の Web アプリのステージングされた発行を使用する方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="cephalin"/>

# Azure App Service の Web アプリのステージング環境を設定する
<a name="Overview"></a>

使用している [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プラン モードが **Standard** または **Premium**である場合は、Web アプリを App Service にデプロイする際、既定の運用スロットではなく、個別の展開スロットにデプロイすることができます。展開スロットは、実際には固有のホスト名を持つライブ Web アプリです。Web アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つの展開スロットの間でスワップすることができます。展開スロットにアプリケーションを展開することには、次のメリットがあります。

- ステージング展開スロットで Web アプリの変更を検証した後に、運用スロットにスワップできます。

- スロットに Web アプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。これにより、Web アプリをデプロイする際のダウンタイムがなくなります。トラフィックのリダイレクトはシームレスであるため、スワップ操作によりドロップされる要求はありません。このワークフロー全体は、スワップ前の検証が必要ない場合、[自動スワップ](#configure-auto-swap-for-your-web-app)を構成することで自動化できます。

- スワップ後も、以前のステージング Web アプリ スロットに元の運用 Web アプリが残っているため、運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。

サポートされる展開スロット数は、App Service のプラン モードごとに異なります。使用している Web アプリのモードでサポートされるスロット数を確認するには、「[アプリ サービス 料金](/pricing/details/app-service/)」を参照してください。

- Web アプリに複数のスロットがある場合は、モードを変更することはできません。

- スケーリングは

- リンク済みリソースの管理は運用サイト スロットでのみサポートされています。

	> [AZURE.NOTE][Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用している場合のみ、非運用スロットを異なる App Service プラン モードに変更することで、この運用スロットに対する影響を回避することができます。なお、2 つのスロットをスワップする前には、非運用スロットと運用スロットを再度同じモードにする必要があります。

<a name="Add"></a>
## Web アプリに展開スロットを追加する ##

複数の展開スロットを有効にするには、Web アプリが **Standard** または **Premium** モードで実行されている必要があります。

1. [Azure プレビュー ポータル](https://portal.azure.com/)で、Web アプリのブレードを開きます。
2. **[展開スロット]** をクリックします。次に、**[展開スロット]** ブレードで **[スロットの追加]** をクリックします。

	![新しい展開スロットの追加][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]Web アプリがまだ **Standard** または **Premium** モードでない場合は、ステージングされた発行を有効にするためのモードを示すメッセージが表示されます。その場合は、操作を継続する前に、**[アップグレード]** を選択して Web アプリの **[スケール]** タブに移動できます。

2. **[スロットの追加]** ブレードで、スロット名を設定し、別の既存の展開スロットから Webアプリ構成を複製するかどうかを選択します。チェック マークをクリックして続行します。

	![構成元][ConfigurationSource1]

	初めてスロットを追加する場合、選択肢は 2 つのみです。運用環境内の既定のスロットから構成を複製するか、または複製しないかのどちらかです。

	複数のスロットを作成すると、運用サイトのスロット以外のスロットから構成を複製できるようになります。

	![構成元][MultipleConfigurationSources]

5. **[展開スロット]** ブレードで、展開スロットをクリックしてスロットのブレードを開くと、他の Web アプリと同様、一連のメトリックと構成が表示されます。現在表示している展開スロットが、<strong><i>your-web-app-name</i>-<i>deployment-slot-name</i></strong> という形式でブレードの上部に表示されます。

	![展開スロットのタイトル][StagingTitle]

5. スロットのブレードで、アプリの URL をクリックします。展開スロットが独自のホスト名を持つライブ アプリであることに注意してください。展開スロットへのパブリック アクセスを制限する方法については、「[App Service Web App - 運用環境以外の展開スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)」を参照してください。

展開スロットを作成した直後は、コンテンツはありません。スロットには、異なるリポジトリ分岐、またはまったく異なるリポジトリから展開できます。またスロットの構成を変更することもできます。コンテンツの更新には、展開スロットに関連付けられた発行プロファイルまたは展開資格情報を使用してください。たとえば、[Git を使用してこのスロットに発行する](web-sites-publish-source-control.md)ことができます。

<a name="AboutConfiguration"></a>
## 展開スロットの構成 ##
別の展開スロットから構成を複製する場合、複製された構成を編集することができます。なお、スワップ先のコンテンツに反映される構成要素 (非スロット固有) もあれば、スワップ後にも同じスロットに維持される構成要素 (スロット固有) もあります。次の一覧では、スロットのスワップ時に変更される構成を示します。

**スワップされる設定**:

- 一般設定 - フレームワーク バージョン、32/64 ビット、Web ソケットなど
- アプリ設定 (スロット固有として構成可能)
- 接続設定 (スロット固有として構成可能)
- ハンドラー マッピング
- 監視と診断の設定
- Web ジョブ コンテンツ

**スワップされない設定**:

- 発行エンドポイント
- カスタム ドメイン名
- SSL 証明書とバインド
- スケールの設定
- Web ジョブ スケジューラ

アプリ設定や接続文字列をスロット固有として構成する (スワップされないようにする) には、特定のスロットの **[アプリケーション設定]** ブレードにアクセスし、スロット固有にする構成要素の **[スロット設定]** ボックスを選択します。構成要素をスロット固有としてマークすると、その Web アプリに関連付けられたすべての展開スロットにわたってその要素がスワップできなくなります。

![スロットの設定][SlotSettings]

<a name="Swap"></a>
## 展開スロットをスワップするには ##

>[AZURE.IMPORTANT]Web アプリを展開スロットからを運用環境にスワップする前に、スロット固有でないすべての設定が、スワップ ターゲットへと反映させたいとおりに構成されていることを確認してください。

1. 展開スロットをスワップするには、Web アプリのコマンド バーか、または展開スロットのコマンド バーで **[スワップ]** ボタンをクリックします。スワップのソースとターゲットが正しく設定されていることを確認してください。通常、スワップ ターゲットは運用スロットになります。  

	![[スワップ] ボタン][SwapButtonBar]

3. **[OK]** をクリックして操作を完了します。操作が完了すると、展開スロットはスワップされています。

## Web アプリの自動スワップの構成 ##

自動スワップを使用すると、Web アプリのエンドユーザーにコールド スタートやダウンタイムを強制することなく、Web アプリを連続的に配置できる、効率的な DevOps シナリオを実現できます。展開スロットを運用環境に自動スワップするよう構成した場合、そのスロットにコードの更新をプッシュすると、Web アプリがスロット内で起動されしだい、App Service によって自動的に運用環境へとスワップされます。

>[AZURE.IMPORTANT]自動スワップを有効にする場合は、スロット構成がターゲット スロット (通常は運用スロット) に反映させたいとおりの構成になっていることを確認してください。

スロットの自動スワップは簡単に構成できます。次の手順に従ってください。

1. **[展開スロット]** ブレードで非運用スロットを選択し、そのスロットのブレードの **[すべての設定]** をクリックします。  

	![][Autoswap1]

2. **[アプリケーションの設定]** をクリックします。**[自動スワップ]** の **[オン]** を選択し、**[自動スワップ スロット]** で目的のターゲット スロットを選択して、コマンド バーの **[保存]** をクリックします。スロットの構成が、ターゲット スロットに反映させたいとおりの構成になっていることを確認してください。

	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

	![][Autoswap2]

	>[AZURE.NOTE]Web アプリの自動スワップ機能に事前に慣れておきたい場合は、まず **[自動スワップ スロット]** で運用スロット以外のターゲット スロットを選択することで、機能をテストすることができます。

3. 展開スロットにコードをプッシュします。しばらくすると自動スワップが実行され、更新がターゲット スロットの URL に反映されます。

<a name="Rollback"></a>
## スワップ後に運用環境のアプリをロールバックするには ##
スロットのスワップ後に運用サイトでエラーが見つかった場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態にロールバックすることができます。

<a name="Delete"></a>
## 展開スロットを削除するには##

展開スロットのブレードで、コマンド バーの **[削除]** をクリックします。

![展開スロットの削除][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## 展開スロット用の Azure PowerShell コマンドレット

Azure PowerShell は、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです (Azure App Service の Web アプリ展開スロットを管理するためのサポートなど)。

- Azure PowerShell のインストールと構成、Azure サブスクリプションを使用した Azure PowerShell の認証については、「[Microsoft Azure PowerShell のインストールおよび構成方法](install-configure-powershell.md)」を参照してください。  

- PowerShell で Azure App Service に使用できるコマンドレットの一覧を表示するには、`help AzureWebsite` を呼び出してください。

----------

### Get-AzureWebsite
**Get-AzureWebsite** コマンドレットは、次の例に示すように、現在のサブスクリプションの Azure の Web アプリに関する情報を表示します。

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
**New-AzureWebsite** コマンドレットを使用し、Web アプリとスロット両方の名前を指定することで、展開スロットを作成できます。また、次の例に示すように、展開スロットの作成では Web アプリと同じリージョンを指定します。

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
次の例に示すように、コンテンツの展開には **Publish-AzureWebsiteProject** コマンドレットを使用できます。

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
コンテンツと構成の更新が新しいスロットに適用されたら、次の例に示すように、**Show-AzureWebsite** コマンドレットを使用してスロットを表示することで、更新を検証できます。

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
**Switch-AzureWebsiteSlot** コマンドレットでは、次の例に示すように、スワップ操作を実行して、更新された展開スロットを運用サイトにすることができます。運用アプリではダウン タイムは発生せず、コールド スタートが行われることもありません。

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
展開スロットが不要になった場合は、次の例に示すように、**Remove-AzureWebsite** コマンドレットを使用して削除できます。

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## 展開スロット用の Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) コマンド

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) には、Web アプリ展開スロットの管理のサポートなど、Azure を使用するためのクロス プラットフォーム コマンドが用意されています。

- Azure サブスクリプションへの xplat-cli の接続方法に関する情報など、xplat-cli のインストールと構成手順については、「[Install and Configure the Azure Cross-Platform Command-Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイスのインストールと構成)](xplat-cli.md)」を参照してください。

-  xplat-cli で Azure App Service に使用できるコマンドの一覧を表示するには、`azure site -h` を呼び出してください。

----------
### azure site list
現在のサブスクリプションの Web アプリに関する情報については、次の例に示すように、**azure site list** を呼び出します。

`azure site list webappslotstest`

----------
### azure site create
展開スロットを作成するには、次の例に示すように、**azure site create** を呼び出し、既存の Web アプリの名前と作成するスロットの名前を指定します。

`azure site create webappslotstest --slot staging`

新しいスロットのソース管理を有効にするには、次の例に示すように、**--git** オプションを使用します。

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
更新した展開スロットを運用アプリにするには、次の例に示すように、**azure site swap** コマンドを使用してスワップ操作を実行します。運用アプリではダウン タイムは発生せず、コールド スタートが行われることもありません。

`azure site swap webappslotstest`

----------
### azure site delete
不要になった展開スロットを削除するには、次の例に示すように、**azure site delete** コマンドを使用します。

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ ##
[Azure App Service Web App - 運用環境以外の展開スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 無料評価版](/pricing/free-trial/)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service および既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

<!--HONumber=54-->
