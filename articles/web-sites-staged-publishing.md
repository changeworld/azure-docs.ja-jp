<properties urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Microsoft Azure Websites でのステージングされたデプロイメント" metaKeywords="Microsoft Azure Web サイト, ステージングされたデプロイメント, サイト スロット" description="Microsoft Azure Websites でステージングされた発行を使用する方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Websites" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="9/9/2014" ms.author="cephalin" />

<a name="Overview"></a>
#Microsoft Azure Websites でのステージングされたデプロイメント#
アプリケーションを Azure Websites にデプロイする際、既定の運用スロットではなく、別のデプロイメント スロットにデプロイすることができます。この場合、独自のホスト名を持つ複数のライブ サイトを使用することになります。このオプションは、**標準**の Web ホスティング プランで使用できます。さらには、運用スロットを含む 2 つの展開スロット間でサイトとサイト構成をスワップすることができます。デプロイメント スロットにアプリケーションをデプロイすることには、次のメリットがあります。

- ステージング展開スロットで Web サイトの変更を検証した後に、運用スロットにスワップできます。

- スワップ後も、以前のステージング サイト スロットに元の運用サイトが残っているため、運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。 
 
- スロットにサイトをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。これにより、サイトを展開する際のダウンタイムがなくなります。トラフィックのリダイレクトはシームレスであるため、スワップ操作によりドロップされる要求はありません。 

**標準**プランでは、各 Web サイトに対し、運用スロットに加えて 4 つのデプロイメント スロットがサポートされています。 

## 目次 ##
- [展開スロットを Web サイトに追加するには](#Add)
- [展開スロットの構成について](#AboutConfiguration)
- [展開スロットをスワップするには](#Swap)
- [運用サイトをステージング サイトにロールバックするには](#Rollback)
- [サイト スロットを削除するには](#Delete)
- [サイト スロットに対する Azure PowerShell コマンドレット](#PowerShell)
- [サイト スロットに対する Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) のコマンド](#CLI)

<a name="Add"></a>
##デプロイメント スロットを Web サイトに追加するには##

複数のデプロイメント スロットを有効にするには、Web サイトを**標準**ホスティング プランで実行する必要があります。 

1. [クイック スタート] ページ、または Web サイトの [ダッシュボード] ページの [概要] セクションで、**[新しい展開スロットの追加]** をクリックします。 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [WACOM.NOTE]
	> Web サイトが**標準**モードになっていない場合は、"**ステージングされた発行を有効にするには、標準モードになっている必要があります**" というメッセージが表示されます。この時点で、**[アップグレード]** を選択して Web サイトの **[スケール]** タブに移動してから、操作を続行することもできます。
	
2. **[新しい展開スロットの追加]** ダイアログで、スロット名を設定し、別の既存のデプロイメント スロットから Web サイト構成を複製するかどうかを選択します。チェック マークをクリックして続行します。 
	
	![Configuration Source][ConfigurationSource1]
	
	スロットを初めて作成する場合は、既定の運用サイトのスロットから構成を複製するか、まったく複製を行わないかを選択します。 
	
	複数のスロットを作成すると、運用サイトのスロット以外のスロットから構成を複製できるようになります。
	
	![Configuration sources][MultipleConfigurationSources]

5. Web サイトの一覧で、Web サイト名の左にあるマークを展開して、デプロイメント スロットを表示します。Web サイトの名前の後に、展開スロット名が表示されます。 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. デプロイメント サイト スロットの名前をクリックすると、他の Web サイトと同様に、一連のタブのあるページが開きます。"<strong><i>Web サイト名</i>(<i>デプロイメント スロット名</i>)</strong>" がポータル ページの上部に示され、デプロイメント サイト スロットが表示されていることがわかります。
	
	![Deployment Slot Title][StagingTitle]
	
5. ダッシュボード ビューでサイトの URL をクリックします。展開スロットが独自のホスト名を持つライブサイトであることに注意してください。デプロイメント サイトへのパブリック アクセスを制限する方法については、「[Azure Web Sites - block web access to non-production deployment slots (Azure Web サイト - 運用以外のデプロイメント スロットへの Web アクセスの禁止)](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/) を参照してください。

	-	 

このスロットにコンテンツはありません。スロットには、異なるリポジトリ分岐、またはまったく異なるリポジトリから展開できます。またスロットの構成を変更することもできます。コンテンツの更新には、展開スロットに関連付けられた発行プロファイルまたは展開資格情報を使用してください。たとえば、[git](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-publish-source-control/) を使用してこのスロットに発行することができます。

<a name="AboutConfiguration"></a>
##デプロイメント スロットの構成について##
別のデプロイメント スロットから構成を複製する場合、複製された構成を編集することができます。次の一覧では、スロットのスワップ時に変更される構成を示します。

**スロットのスワップ時に変更される構成**:

- 全般設定
- Connection strings
- ハンドラー マッピング
- 監視と診断の設定

**スロットのスワップ時に変更されない構成**:

- 発行エンドポイント
- カスタム ドメイン名
- SSL 証明書とバインド
- スケールの設定

**注**:

- 複数のデプロイメント スロットは**標準** Web ホスティング プランのサイトにのみ使用できます。

- 複数のスロットがある場合、ホスティング プランの変更はできません。

- 運用環境にスワップするスロットと運用サイトの構成は一致している必要があります。

- 既定では、展開スロットは運用サイトと同じデータベースを指定します。ただし、展開スロットのデータベース接続文字列を変更することで、代替データベースを指定するように展開スロットを構成できます。その後、運用環境にスワップする前に、デプロイメント スロットで元のデータベース接続文字列を復元できます。


<a name="Swap"></a>
##デプロイメント スロットをスワップするには##

1. デプロイメント スロットをスワップするには、Web サイトの一覧でスワップするデプロイメント スロットを選択し、コマンド バーで **[スワップ]** ボタンをクリックします。 
	
	![Swap Button][SwapButtonBar]
	
2. [展開のスワップ] ダイアログが表示されます。このダイアログでは、スワップ元およびスワップ先となるサイト スロットを選択できます。
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. チェックマークをクリックして操作を完了します。操作が完了すると、サイト スロットはスワップされています。


<a name="Rollback"></a>
##運用サイトをステージング サイトにロールバックするには##
スロットのスワップ後に運用サイトでエラーが見つかった場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態にロールバックすることができます。 

<a name="Delete"></a>
##サイト スロットを削除するには##

Azure Websites ポータル ページの下部にあるコマンド バーで、**[削除]** をクリックします。Web サイトとすべての展開スロットを削除するか、展開スロットのみを削除することができます。 

![Delete a Site Slot][DeleteStagingSiteButton]

**注**:

- スケーリングは、運用以外のスロットには使用できません。運用スロットにのみ使用できます。

- リンク済みリソースの管理は運用スロットでのみサポートされています。 

- 必要に応じて運用サイト スロットに直接発行することもできます。

- 既定では、デプロイメント スロット (サイト) と運用スロット (サイト) は同じリソースを共有し、同じ VM 上で実行されます。ステージング スロットで負荷テストを実行すると、運用環境にも同等の負荷がかかります。 
	
	> [WACOM.NOTE][Azure プレビュー ポータル](https://portal.azure.com)を使用している場合のみ、運用以外のスロットを異なる Web ホスティング プランに変更することで、この運用スロットに対する影響を回避することができます。ただし、テスト スロットを運用環境にスワップするには、その前にテスト スロットと運用スロットを同じ Web ホスティング プランに戻す必要があります。

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##サイト スロットに対する Azure PowerShell コマンドレット 

Azure PowerShell は、Azure Websites のデプロイメント スロットの管理サポートを含む、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです。 

- Azure PowerShell のインストールと構成、Windows Azure サブスクリプションを使用した Azure PowerShell の認証については、「[Windows Azure PowerShell のインストールおよび構成方法](http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell)」を参照してください。  

- PowerShell で Azure Websites に使用できるコマンドレットの一覧を表示するには、`help AzureWebsite` を呼び出してください。 

----------

###Get-AzureWebsite
**Get-AzureWebsite** コマンドレットは、次の例に示すように、現在のサブスクリプションの Azure Web サイトに関する情報を表示します。 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
**New-AzureWebsite** コマンドレットを使用し、サイトとスロット両方の名前を指定することで、標準モードの任意の Web サイトにサイト スロットを作成できます。また、次の例に示すように、展開スロットの作成ではサイトと同じリージョンを指定します。 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
次の例に示すように、コンテンツのデプロイには **Publish-AzureWebsiteProject** コマンドレットを使用できます。 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
コンテンツと構成の更新が新しいスロットに適用されたら、次の例に示すように、**Show-AzureWebsite** コマンドレットを使用してスロットを表示することで、更新を検証できます。

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
**Switch-AzureWebsiteSlot** コマンドレットでは、次の例に示すように、スワップ操作を実行して、更新されたデプロイメント スロットを運用サイトにすることができます。運用サイトではダウン タイムは発生せず、コールド スタートが行われることもありません。 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
デプロイメント スロットが不要になった場合は、次の例に示すように、**Remove-AzureWebsite** コマンドレットを使用して削除できます。

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##サイト スロットに対する Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) のコマンド

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) には、Azure Websites でのデプロイメント スロットの管理のサポートなど、Azure を使用するためのクロス プラットフォーム コマンドが用意されています。 

- Azure サブスクリプションへの xplat-cli の接続方法に関する情報など、xplat-cli のインストールと構成手順については、「[Install and Configure the Azure Cross-Platform Command-Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイスのインストールと構成)](http://www.windowsazure.com/ja-jp/documentation/articles/xplat-cli)」を参照してください。 

-  xplat-cli で Azure Websites に使用できるコマンドの一覧を表示するには、`azure site -h` を呼び出してください。 

----------
###azure site list
現在のサブスクリプションの Azure Web サイトに関する情報については、次の例に示すように、**azure site list** を呼び出します。
 
`azure site list siteslotstest`

----------
###azure site create
標準モードの任意の Web サイトにサイト スロットを作成するには、次の例に示すように、**azure site create** を呼び出し、既存のサイトの名前と作成するスロットの名前を指定します。

`azure site create siteslotstest --slot staging`

新しいスロットのソース管理を有効にするには、次の例に示すように、**--git** オプションを使用します。
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
更新したデプロイメント スロットを運用サイトにするには、次の例に示すように、**azure site swap** コマンドを使用してスワップ操作を実行します。運用サイトではダウン タイムは発生せず、コールド スタートが行われることもありません。 

`azure site swap siteslotstest`

----------
###azure site delete
不要になったデプロイメント スロットを削除するには、次の例に示すように、**azure site delete** コマンドを使用します。

`azure site delete siteslotstest --slot staging`

----------
## 次のステップ ##
[Azure Web Sites - block web access to non-production deployment slots (Azure Web サイト - 運用環境以外の展開スロットへの Web アクセスの禁止)](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 無料評価版](http://azure.microsoft.com/ja-jp/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
