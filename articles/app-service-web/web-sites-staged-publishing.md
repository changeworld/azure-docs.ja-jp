<properties
	pageTitle="Azure App Service の Web アプリのステージング環境を設定する"
	description="Azure App Service の Web アプリのステージングされた発行を使用する方法について説明します。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="cephalin"/>

# Azure App Service の Web アプリのステージング環境を設定する
<a name="Overview"></a>

使用している [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プラン モードが **Standard** または **Premium**である場合は、Web アプリを App Service にデプロイする際、既定の運用スロットではなく、個別のデプロイ スロットにデプロイすることができます。デプロイ スロットは、実際には固有のホスト名を持つライブ Web アプリです。Web アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。デプロイ スロットにアプリケーションをデプロイすることには、次のメリットがあります。

- ステージング デプロイ スロットで Web アプリの変更を検証した後に、運用スロットにスワップできます。

- スロットに Web アプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。これにより、Web アプリをデプロイする際のダウンタイムがなくなります。トラフィックのリダイレクトはシームレスであるため、スワップ操作によりドロップされる要求はありません。このワークフロー全体は、スワップ前の検証が必要ない場合、[自動スワップ](#configure-auto-swap-for-your-web-app)を構成することで自動化できます。

- スワップ後も、以前のステージング Web アプリ スロットに元の運用 Web アプリが残っているため、運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。

サポートされるデプロイ スロット数は、App Service のプラン モードごとに異なります。使用している Web アプリのモードでサポートされるスロット数を確認するには、「[App Service 料金](/pricing/details/app-service/)」を参照してください。

- Web アプリに複数のスロットがある場合は、モードを変更することはできません。

- スケーリングは

- リンク済みリソースの管理は運用サイト スロットでのみサポートされています。[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用している場合のみ、非運用スロットを異なる App Service プラン モードに変更することで、この運用スロットに対する影響を回避することができます。なお、2 つのスロットをスワップする前には、非運用スロットと運用スロットを再度同じモードにする必要があります。


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## Web アプリにデプロイ スロットを追加する ##

複数のデプロイ スロットを有効にするには、Web アプリが **Standard** または **Premium** モードで実行されている必要があります。

1. [Azure ポータル](https://portal.azure.com/)の Web アプリのブレードを開きます。
2. **[設定]** をクリックし、**[デプロイメント スロット]** をクリックします。次に、**[デプロイ スロット]** ブレードで **[スロットの追加]** をクリックします。

	![新しいデプロイ スロットの追加][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	Web アプリがまだ **Standard** または **Premium** モードでない場合は、ステージングされた発行を有効にするためのモードを示すメッセージが表示されます。その場合は、操作を継続する前に、**[アップグレード]** を選択して Web アプリの **[スケール]** タブに移動できます。

2. **[スロットの追加]** ブレードで、スロット名を設定し、別の既存のデプロイ スロットから Web アプリ構成を複製するかどうかを選択します。チェック マークをクリックして続行します。

	![構成元][ConfigurationSource1]

	初めてスロットを追加する場合、選択肢は 2 つのみです。運用環境内の既定のスロットから構成を複製するか、または複製しないかのどちらかです。

	複数のスロットを作成すると、運用サイトのスロット以外のスロットから構成を複製できるようになります。

	![構成元][MultipleConfigurationSources]

5. **[デプロイメント スロット]** ブレードで、デプロイメント スロットをクリックしてスロットのブレードを開くと、他の Web アプリと同様、一連のメトリックと構成が表示されます。現在表示しているデプロイメント スロットが、**your-web-app-name-deployment-slot-name** という形式でブレードの上部に表示されます。

	![デプロイ スロットのタイトル][StagingTitle]

5. スロットのブレードで、アプリの URL をクリックします。デプロイメント スロットが独自のホスト名を持つライブ アプリであることに注意してください。デプロイ スロットへのパブリック アクセスを制限する方法については、「[App Service Web App - 運用環境以外のデプロイ スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)」を参照してください。

デプロイ スロットを作成した直後は、コンテンツはありません。スロットには、異なるリポジトリ分岐、またはまったく異なるリポジトリからデプロイできます。またスロットの構成を変更することもできます。コンテンツの更新には、デプロイ スロットに関連付けられた発行プロファイルまたはデプロイ資格情報を使用してください。たとえば、[Git を使用してこのスロットに発行する](app-service-deploy-local-git.md)ことができます。

<a name="AboutConfiguration"></a>
## デプロイ スロットの構成 ##
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。なお、スワップ先のコンテンツに反映される構成要素 (非スロット固有) もあれば、スワップ後にも同じスロットに維持される構成要素 (スロット固有) もあります。次の一覧では、スロットのスワップ時に変更される構成を示します。

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

アプリ設定や接続文字列をスロット固有として構成する (スワップされないようにする) には、特定のスロットの **[アプリケーション設定]** ブレードにアクセスし、スロット固有にする構成要素の **[スロット設定]** ボックスを選択します。構成要素をスロット固有としてマークすると、その Web アプリに関連付けられたすべてのデプロイ スロットにわたってその要素がスワップできなくなります。

![スロットの設定][SlotSettings]

<a name="Swap"></a>
## デプロイ スロットをスワップするには ##

>[AZURE.IMPORTANT] Web アプリをデプロイ スロットからを運用環境にスワップする前に、スロット固有でないすべての設定が、スワップ ターゲットへと反映させたいとおりに構成されていることを確認してください。

1. デプロイ スロットをスワップするには、Web アプリのコマンド バーか、またはデプロイ スロットのコマンド バーで **[スワップ]** ボタンをクリックします。スワップのソースとターゲットが正しく設定されていることを確認してください。通常、スワップ ターゲットは運用スロットになります。

	![[スワップ] ボタン][SwapButtonBar]

3. **[OK]** をクリックして操作を完了します。操作が完了すると、デプロイ スロットはスワップされています。

## Web アプリの自動スワップの構成 ##

自動スワップを使用すると、Web アプリのエンドユーザーにコールド スタートやダウンタイムを強制することなく、Web アプリを連続的にデプロイできる、効率的な DevOps シナリオを実現できます。デプロイ スロットを運用環境に自動スワップするよう構成した場合、そのスロットにコードの更新をプッシュすると、Web アプリがスロット内で起動されしだい、App Service によって自動的に運用環境へとスワップされます。

>[AZURE.IMPORTANT] 自動スワップを有効にする場合は、スロット構成がターゲット スロット (通常は運用スロット) に反映させたいとおりの構成になっていることを確認してください。

スロットの自動スワップは簡単に構成できます。次の手順に従ってください。

1. **[デプロイ スロット]** ブレードで非運用スロットを選択し、そのスロットのブレードの **[すべての設定]** をクリックします。

	![][Autoswap1]

2. **[アプリケーションの設定]** をクリックします。**[自動スワップ]** の **[オン]** を選択し、**[自動スワップ スロット]** で目的のターゲット スロットを選択して、コマンド バーの **[保存]** をクリックします。スロットの構成が、ターゲット スロットに反映させたいとおりの構成になっていることを確認してください。

	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

	![][Autoswap2]

	>[AZURE.NOTE] Web アプリの自動スワップ機能に事前に慣れておきたい場合は、まず **[自動スワップ スロット]** で運用スロット以外のターゲット スロットを選択することで、機能をテストすることができます。

3. デプロイ スロットにコードをプッシュします。しばらくすると自動スワップが実行され、更新がターゲット スロットの URL に反映されます。

<a name="Multi-Phase"></a>
## Web アプリに複数フェーズのスワップを使用する ##

複数フェーズのスワップを使用すると、スロット固有として設計された構成要素 (接続文字列など) のコンテキストで検証を簡素化できます。この場合、スワップが実際に有効になる前に、スワップ ターゲットのこのような構成要素をスワップ ソースに適用して検証すると便利です。スワップ ターゲットの構成要素をスワップ ソースに適用した後は、実行可能な操作として、スワップを完了するか、スワップ ソースの元の構成に戻します。元の構成に戻すと、スワップをキャンセルできます。複数フェーズのスワップに使用できる Azure PowerShell コマンドレットのサンプルについては、「デプロイ スロット用の Azure PowerShell コマンドレット」をご覧ください。

<a name="Rollback"></a>
## スワップ後に運用環境のアプリをロールバックするには ##

スロットのスワップ後に運用サイトでエラーが見つかった場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態にロールバックすることができます。

<a name="Warm-up"></a>
## スワップ前のカスタム ウォームアップ ##

アプリによっては、カスタム ウォームアップ アクションが必要な場合があります。web.config の applicationInitialization 構成要素を使用して、要求の受信前に実行されるカスタムの初期化アクションを指定できます。スワップ操作は、このカスタム ウォームアップが完了するまで待機します。サンプルの web.config フラグメントを次に示します。

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## デプロイ スロットを削除するには##

デプロイ スロットのブレードで、コマンド バーの **[削除]** をクリックします。

![デプロイ スロットの削除][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## デプロイ スロット用の Azure PowerShell コマンドレット

Azure PowerShell は、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです (Azure App Service の Web アプリ デプロイ スロットを管理するためのサポートなど)。

- Azure PowerShell のインストールと構成、Azure サブスクリプションを使用した Azure PowerShell の認証については、「[Microsoft Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

----------

### Web アプリの作成

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### Web アプリのデプロイ スロットを作成する

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### 複数フェーズのスワップを開始し、ターゲット スロットの構成をソース スロットに適用する

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### 複数フェーズのスワップの最初のフェーズを元に戻し、ソース スロットの構成を復元する

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### デプロイ スロットをスワップする

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### デプロイ スロットを削除する

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## デプロイ スロット用の Azure コマンド ライン インターフェイス (Azure CLI) コマンド

Azure CLI には、Web アプリ デプロイ スロットの管理のサポートなど、Azure を使用するためのクロス プラットフォーム コマンドが用意されています。

- Azure サブスクリプションへのAzure CLI の接続方法に関する情報など、Azure CLI のインストールと構成手順については、「[Azure CLI のインストールと構成](../xplat-cli-install.md)」をご覧ください。

-  Azure CLI で Azure App Service に使用できるコマンドの一覧を表示するには、`azure site -h` を呼び出してください。

----------
### azure site list
現在のサブスクリプションの Web アプリに関する情報については、次の例に示すように、**azure site list** を呼び出します。

`azure site list webappslotstest`

----------
### azure site create
デプロイ スロットを作成するには、次の例に示すように、**azure site create** を呼び出し、既存の Web アプリの名前と作成するスロットの名前を指定します。

`azure site create webappslotstest --slot staging`

新しいスロットのソース管理を有効にするには、次の例に示すように、**--git** オプションを使用します。

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
更新したデプロイ スロットを運用アプリにするには、次の例に示すように、**azure site swap** コマンドを使用してスワップ操作を実行します。運用アプリではダウン タイムは発生せず、コールド スタートが行われることもありません。

`azure site swap webappslotstest`

----------
### azure site delete
不要になったデプロイ スロットを削除するには、次の例に示すように、**azure site delete** コマンドを使用します。

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ ##
[Azure App Service Web App - 運用環境以外のデプロイ スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 無料試用版](/pricing/free-trial/)

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

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
 

<!---HONumber=AcomDC_0803_2016-->