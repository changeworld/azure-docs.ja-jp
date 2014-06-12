<properties linkid="web-sites-staged-publishing" urlDisplayName="Microsoft Azure のサイトのステージング方法" pageTitle="Microsoft Azure の Web サイトのステージングされた展開" metaKeywords="Microsoft Azure の Web サイト, ステージングされた展開, サイト スロット" description="Microsoft Azure の Web サイトでステージングされた発行を使用する方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="" title="Microsoft Azure の Web サイトのステージングされた展開" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

#Microsoft Azure の Web サイトのステージングされた展開#

## 目次##
- [概要](#Overview)
- [展開スロットを Web サイトに追加するには](#Add)
- [展開スロットの構成について](#AboutConfiguration)
- [展開スロットをスワップするには](#Swap)
- [運用サイトをステージング サイトにロールバックするには](#Rollback)
- [サイト スロットを削除するには](#Delete)
- [サイト スロットに対する Azure PowerShell コマンドレット](#PowerShell)
- [サイト スロットに対する Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) のコマンド](#CLI)

<a name="Overview"></a>
##概要##
Microsoft Azure の Web サイトで実行されている標準モードのサイト用にサイト スロットを作成することで、ステージングされた展開ワークフローが有効になります。既定の各運用サイトに展開またはステージング サイト スロットを作成し (この時点で運用サイト スロットになります)、これらのスロットをダウン タイムなしでスワップします。ステージングされた展開は次のシナリオに重要です。

- **展開前の検証** - ステージング サイト スロットにコンテンツや構成を展開した後、変更を検証してから、それらの変更を運用サイトにスワップできます。

- **サイト コンテンツのビルドと統合** - コンテンツの更新をステージング展開スロットに段階的に追加し、更新が完了したら、展開スロットを運用サイトにスワップできます。

- **運用サイトのロールバック** - 運用サイトにスワップした変更が想定どおりでない場合は、元のコンテンツを運用サイトにすぐにスワップして戻すことができます。

Microsoft Azure では、運用サイトへのスワップ前にソース サイト スロットのすべてのインスタンスが準備されるため、コンテンツの展開時のコールド スタートは必要なくなります。トラフィックのリダイレクトはシームレスであるため、スワップ操作によりドロップされる要求はありません。現在、標準モードの Web サイトあたり、既定の運用サイト スロットに加えて、1 つの展開スロットのみがサポートされています。

<a name="Add"></a>
##展開スロットを Web サイトに追加するには##

Web サイトに対してサイト スロットの作成を有効にするには、そのサイトを標準モードで実行する必要があります。

1. [クイック スタート] ページ、または Web サイトの [ダッシュボード] ページの [概要] セクションで、**[新しい展開スロットの追加]** をクリックします。
	
	![新しい展開スロットの追加][QGAddNewDeploymentSlot]
	
	> [WACOM.NOTE]
	> Web サイトが標準モードになっていない場合は、"**ステージングされた発行を有効にするには、標準モードになっている必要があります**" というメッセージが表示されます。この時点で、**[アップグレード]** を選択して Web サイトの **[スケールの設定]** タブに移動してから、操作を続行することもできます。
	
2. **[新しい展開スロットの追加]** ダイアログが表示されます。
	
	![[新しい展開スロットの追加] ダイアログ][AddNewDeploymentSlotDialog]
	
	展開スロットの名前を指定します。名前は 60 文字以内の英数字で指定してください。特殊文字または空白文字は使用できません。
	
3. Web サイトの一覧で、Web サイト名の左にあるマークを展開して、展開スロットを表示します。運用サイトの名前の後に、指定した展開スロット名がかっこ内に表示されます。
	
	![サイトの一覧で展開スロットが表示されたところ][SiteListWithStagedSite]
	
4. 展開サイト スロットの名前を選択すると、他の Web サイトと同様に、一連のタブのあるページが開きます。"<strong><i>Web サイト名</i>(<i>展開スロット名</i>)</strong>" がポータル ページの上部に示され、展開サイト スロットが表示されていることがわかります。
	
	![展開スロットのタイトル][StagingTitle]
	
これで、展開サイト スロットのコンテンツと構成を更新できます。コンテンツの更新には、展開サイト スロットに関連付けられた発行プロファイルまたは展開資格情報を使用してください。

<a name="AboutConfiguration"></a>
##展開スロットの構成について##
展開スロットが作成されると、既定ではそのスロットの構成は運用サイト スロットから複製されます。すべてのサイト スロットの構成は編集可能です。

**スロットのスワップ時に変更される構成**:

- 全般設定
- 接続文字列
- ハンドラー マッピング
- 監視と診断の設定

**スロットのスワップ時に変更されない構成**:

- 発行エンドポイント
- カスタム ドメイン名
- SSL 証明書とバインド
- スケールの設定

**注**:

- 展開スロットは標準モードのサイトにのみ使用できます。

- サイトを無料モード、共有モード、または基本モードに変更すると、スワップできなくなります。

- 運用環境にスワップする展開スロットと運用サイトの構成は一致している必要があります。

- 既定では、展開スロットは運用サイトと同じデータベースを指定します。ただし、展開スロットのデータベース接続文字列を変更することで、代替データベースを指定するように展開スロットを構成できます。その後、運用環境にスワップする前に、展開スロットで元のデータベース接続文字列を復元できます。


<a name="Swap"></a>
##展開スロットをスワップするには##

1. 展開スロットをスワップするには、Web サイトの一覧で展開スロットを選択し、コマンド バーで **[スワップ]** ボタンをクリックします。
	
	![[スワップ] ボタン][SwapButtonBar]
	
2. [展開のスワップ] ダイアログが表示されます。このダイアログでは、スワップ元およびスワップ先となるサイト スロットを選択できます。
	
	![[展開のスワップ] ダイアログ][SwapDeploymentsDialog]
	
3. チェックマークをクリックして操作を完了します。操作が完了すると、サイト スロットはスワップされています。


<a name="Rollback"></a>
##運用サイトをステージング サイトにロールバックするには##
運用サイトにスワップしたコンテンツや構成でエラーが見つかった場合は、展開スロット (以前の運用サイト スロット) を運用サイトに簡単にスワップして戻し、ステージング モードでサイトの新しいバージョンに修正を加えることができます。

> [WACOM.NOTE]
> ロールバックが成功するには、展開サイト スロットに、以前の運用サイトの変更されていないコンテンツと構成が残っている必要があります。

<a name="Delete"></a>
##サイト スロットを削除するには##

Azure の Web サイトのポータル ページの下部にあるコマンド バーで、**[削除]** をクリックします。Web サイトとすべての展開スロットを削除するか、展開スロットのみを削除することができます。

![サイト スロットの削除][DeleteStagingSiteButton]

確認メッセージで **[はい]** をクリックすると、選択したオプションに応じて 1 つまたはすべてのスロットが削除されます。

**注**:

- スケーリングは運用サイト スロットにのみ使用できます。

- リンク済みリソースの管理は運用サイト スロットでのみサポートされています。

- 必要に応じて運用サイト スロットに直接発行することもできます。

- 現在、展開スロット (サイト) と運用スロット (サイト) は同じリソースを共有し、同じ VM 上で実行されます。ステージング スロットで負荷テストを実行すると、運用環境にも同等の負荷がかかります。

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##サイト スロットに対する Azure PowerShell コマンドレット

Azure PowerShell は、Azure の Web サイトの展開スロットの管理のサポートを含む、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです。

- Azure PowerShell のインストールと構成、Windows Azure サブスクリプションを使用した Azure PowerShell の認証については、「[Windows Azure PowerShell のインストールおよび構成方法](http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell)」を参照してください。

- PowerShell で Azure の Web サイトに使用できるコマンドレットの一覧を表示するには、`help AzureWebsite` を呼び出してください。

----------

###Get-AzureWebsite
**Get-AzureWebsite** コマンドレットは、次の例に示すように、現在のサブスクリプションの Azure の Web サイトに関する情報を表示します。

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
**New-AzureWebsite** コマンドレットを使用し、サイトとスロット両方の名前を指定することで、標準モードの任意の Web サイトにサイト スロットを作成できます。また、次の例に示すように、展開スロットの作成ではサイトと同じリージョンを指定します。

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

----------

###Publish-AzureWebsiteProject
次の例に示すように、コンテンツの展開には **Publish-AzureWebsiteProject** コマンドレットを使用できます。

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
コンテンツと構成の更新が新しいスロットに適用されたら、次の例に示すように、**Show-AzureWebsite** コマンドレットを使用してスロットを表示することで、更新を検証できます。

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
**Switch-AzureWebsiteSlot** コマンドレットでは、次の例に示すように、スワップ操作を実行して、更新された展開スロットを運用サイトにすることができます。運用サイトではダウン タイムは発生せず、コールド スタートが行われることもありません。

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
展開スロットが不要になった場合は、次の例に示すように、**Remove-AzureWebsite** コマンドレットを使用して削除できます。

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##サイト スロットに対する Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) のコマンド

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) には、Azure の Web サイトでの展開スロットの管理のサポートなど、Azure を使用するためのクロス プラットフォーム コマンドが用意されています。

- Azure サブスクリプションへの xplat-cli の接続方法に関する情報など、xplat-cli のインストールと構成手順については、「[Install and Configure the Azure Cross-Platform Command-Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイスのインストールと構成)](http://www.windowsazure.com/ja-jp/documentation/articles/xplat-cli)」を参照してください。

- xplat-cli で Azure の Web サイトに使用できるコマンドの一覧を表示するには、`azure site -h` を呼び出してください。

----------
###azure site list
現在のサブスクリプションの Azure の Web サイトに関する情報については、次の例に示すように、**azure site list** を呼び出します。
 
`azure site list siteslotstest`

----------
###azure site create
標準モードの任意の Web サイトにサイト スロットを作成するには、次の例に示すように、**azure site create** を呼び出し、既存のサイトの名前と作成するスロットの名前を指定します。

`azure site create siteslotstest --slot staging`

新しいスロットのソース管理を有効にするには、次の例に示すように、**--git** オプションを使用します。
 
`azure site create â€“-git siteslotstest --slot staging`

----------
###azure site swap
更新した展開スロットを運用サイトにするには、次の例に示すように、**azure site swap** コマンドを使用してスワップ操作を実行します。運用サイトではダウン タイムは発生せず、コールド スタートが行われることもありません。

`azure site swap siteslotstest`

----------
###azure site delete
不要になった展開スロットを削除するには、次の例に示すように、**azure site delete** コマンドを使用します。

`azure site delete siteslotstest --slot staging`

----------


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png

