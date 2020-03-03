---
title: クイックスタート - Chef を使用して Azure で Windows 仮想マシンを構成する
description: このクイックスタートでは、Chef を使用して Azure に Windows 仮想マシンをデプロイおよび構成する方法について説明します
keywords: Chef, Azure, DevOps, 仮想マシン
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589496"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>クイックスタート - Chef を使用して Azure で Windows 仮想マシンを構成する

Chef を使用すると、自動化と望ましい状態の構成を実現することが可能になります。

Chef は最新のクラウド API リリースで Azure とのシームレスな統合を提供しており、1 つのコマンドで構成状態をプロビジョニングしてデプロイできます。

この記事では、Chef 環境を設定し、Azure 仮想マシンをプロビジョニングした後、ポリシー (クックブック) を作成して、そのクックブックを Azure 仮想マシンにデプロイします。

## <a name="chef-basics"></a>Chef の基礎

この記事を開始する前に、[Chef の基本的な概念を確認しておきます](https://www.chef.io/chef)。

次の図は、大まかな Chef アーキテクチャを示しています。

![Chef アーキテクチャ](media/chef-automation/chef-architecure.png)

Chef には、 
- Chef サーバー - 管理ポイントです。Chef サーバーにはホスト型ソリューションとオンプレミス ソリューションの 2 つのオプションがあります。
- Chef クライアント (ノード) - 管理対象のサーバーに置かれるエージェントです。
- Chef ワークステーション - ポリシーを作成したり、管理コマンドを実行したりする管理者用ワークステーションと Chef ツールのソフトウェア パッケージの両方を指す名前です。

通常、**ワークステーション**はコマンドを実行する場所、**Chef ワークステーション**はソフトウェア パッケージを表すと見なします。

たとえば、**Chef ワークステーション**の一部として knife コマンドをダウンロードしますが、knife コマンドはインフラストラクチャを管理するために**ワークステーション**から実行します。

また、Chef では "*クックブック*" と "*レシピ*" という概念も使用します。 これらの用語はそれぞれ、サーバーに対して定義および適用されるポリシーです。

## <a name="preparing-your-workstation"></a>ワークステーションを準備する

まず、Chef 構成ファイルと Cookbook を保存するディレクトリを作成して、ワークステーションを準備します。

`C:\Chef` という名前のディレクトリを作成します。

お使いのワークステーション上に、最新バージョンの [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をダウンロードしてインストールします。

## <a name="configure-azure-service-principal"></a>Azure サービス プリンシパルを構成する

ここでは、サービス プリンシパルを使用して、Chef ワークステーションから Azure リソースを作成します。  必要なアクセス許可を利用して関連するサービス プリンシパルを作成するには、PowerShell 内で次のコマンドを実行します。
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

SubscriptionID、TenantID、ClientID、およびクライアント シークレット (このチュートリアル内で前に設定したパスワード) をメモしておいてください。これらの値は後で必要になります。 

## <a name="setup-chef-server"></a>Chef サーバーの設定

このガイドでは、ホスト型の Chef にサインアップすることを前提としています。

Chef サーバーを使用していない場合は、次の手順を実行できます。

* [Hosted Chef](https://manage.chef.io/signup) をサインアップします。これは、Chef を開始する最も簡単な方法です。
* [Chef ドキュメント](https://docs.chef.io/)の[インストール手順](https://docs.chef.io/install_server.html)に従って、Linux ベースのマシンにスタンドアロン Chef サーバーをインストールします。

### <a name="creating-a-hosted-chef-account"></a>Hosted Chef アカウントの作成

ホストされる Chef アカウントに[ここ](https://manage.chef.io/signup)からサインアップします。

サインアップ プロセス中、新しい組織を作成するように尋ねられます。

![[Create organization]\(組織の作成\) ウィンドウ](media/chef-automation/create-organization.png)

組織を作成したら、スターター キットをダウンロードします。

![Chef の構成](media/chef-automation/configure-chef.png)

> [!NOTE]
> キーがリセットされるという警告が表示されても、構成済みのインフラストラクチャがまだないため、そのまま進んでかまいません。
>

このスターター キットの zip ファイルでは、`.chef` ディレクトリに組織の構成ファイルとユーザー キーが含まれています。

`organization-validator.pem` は秘密キーであり、秘密キーは Chef サーバーに保存するべきではないため、別途ダウンロードする必要があります。 [Chef Manage](https://manage.chef.io/) から、[Administration]\(管理\) セクションに移動して、[Reset Validation Key]\(検証キーのリセット\) を選択します。これにより、別途ダウンロードするファイルが提供されます。 ファイルを c:\chef に保存します。

### <a name="configuring-your-chef-workstation"></a>Chef ワークステーションの構成

`chef-starter.zip` の内容を `c:\chef` に抽出します。

`chef-starter\chef-repo\.chef` の下にあるすべてのファイルを `c:\chef` ディレクトリにコピーします。

`organization-validator.pem` ファイルが `c:\Downloads` に保存されている場合は、そのファイルを `c:\chef` にコピーします。

これでディレクトリは次の例のようになります。

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

c:\chef のルートには、5 つのファイルと 4 つのディレクトリがあるはずです (空の chef-repo ディレクトリを含む)。

### <a name="edit-kniferb"></a>knife.rb の編集

PEM ファイルには組織と、通信用の管理者の秘密キーが含まれ、knife.rb ファイルには knife 構成が含まれています。 knife.rb ファイルを編集する必要があります。

適当なエディターで knife.rb ファイルを開きます。 変更されていないファイルは次のようになります。

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

次の情報を knife.rb に追加し、プレースホルダーを実際の情報で置き換えます。

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

これらの行により、`c:\chef\cookbooks` の下にあるクックブック ディレクトリが Knife によって参照されるようになります。

これで、`knife.rb` ファイルは次の例のようになります。

![Knife ファイルの例](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef ワークステーションのインストール

次に、[Chef ワークステーションをダウンロードして、インストールします](https://downloads.chef.io/chef-workstation/)。

既定の場所に Chef ワークステーションをインストールします。

デスクトップに、CW PowerShell が表示されます。 このツールは、Chef 製品とのやり取りに使用します。 CW PowerShell により、`chef-run` などの新しいコマンドと、Chef CLI コマンド (`chef` など) が使用できるようになります。 `chef -v` を使用して、Chef ワークステーションと Chef ツールのインストールされているバージョンを確認します。 Chef ワークステーション アプリで **[About Chef Workstation]\(Chef ワークステーションについて\)** を選択して、ワークステーションのバージョンを確認することもできます。

`chef --version` は、次のように返します。

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> パスの順序が重要です。 opscode パスの順序が正しくない場合は、問題が発生します。
>

続行する前にワークステーションを再起動します。

### <a name="install-knife-azure"></a>Knife Azure のインストール

このチュートリアルでは、Azure Resource Manager を使用して、仮想マシンとやり取りすることを想定しています。

Azure プラグインを含む、Knife Azure 拡張機能をインストールします。

次のコマンドを実行します。

    chef gem install knife-azure ––pre

> [!NOTE]
> `–-pre` 引数により、最新の API セットへのアクセスを提供する Knife Azure プラグインの最新 RC バージョンを確実に受け取ることができます。
>
>

さまざまな依存関係も同時にインストールされる場合があります。

![knife-azure のインストールからの出力](./media/chef-automation/install-knife-azure.png)

すべてが正しく構成されたことを確認するには、次のコマンドを実行します。

    knife azurerm server list

すべてが正しく構成されていると、利用できる Azure イメージの一覧がスクロール表示されます。

おめでとうございます。 これで、ワークステーションがセットアップされました。

## <a name="creating-a-cookbook"></a>クックブックの作成

クックブックはマネージド クライアント上で実行する一連のコマンドを定義するために Chef によって使用されます。 クックブックの作成は簡単で、`chef generate cookbook` コマンドを使用するだけでクックブック テンプレートを生成できます。 この cookbook は、IIS を自動的にデプロイさせる Web サーバー用です。

`C:\Chef directory` の下で、次のコマンドを実行します。

    chef generate cookbook webserver

このコマンドによって、ディレクトリ C:\Chef\cookbooks\webserver に一連のファイルが生成されます。 次に、マネージド仮想マシンで Chef クライアントが実行する一連のコマンドを定義します。

コマンドはファイル default.rb に保存されています。 このファイルでは、IIS をインストールして起動し、テンプレート ファイルを `wwwroot` フォルダーにコピーする一連のコマンドを定義します。

C:\chef\cookbooks\webserver\recipes\default.rb ファイルを変更し、次の行を追加します。

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

完了したらファイルを保存します。

## <a name="creating-a-template"></a>テンプレートの作成

この手順では、`default.html` ページとして使用するテンプレート ファイルを生成します。

次のコマンドを実行してテンプレートを生成します。

    chef generate template webserver Default.htm

`C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` ファイルに移動します。 シンプルな *Hello World* HTML コードを追加してファイルを編集した後、このファイルを保存します。

## <a name="upload-the-cookbook-to-the-chef-server"></a>Chef サーバーにクックブックをアップロードする

この手順では、ローカル コンピューターで作成したクックブックのコピーを作成し、Chef がホストするサーバーにこれをアップロードします。 アップロードが完了すると、クックブックが **[Policy]\(ポリシー\)** タブに表示されます。

    knife cookbook upload webserver

![Chef サーバーにクックブックをインストールした結果](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Knife Azure で仮想マシンをデプロイする

Azure 仮想マシンをデプロイし、`knife` コマンドを使用して `Webserver` クックブックを適用します。

`knife` コマンドを実行すると、IIS Web サービスと既定の Web ページもインストールされます。

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

`knife` コマンドの例では、Windows Server 2016 がインストールされた *Standard_DS2_v2* 仮想マシンが米国西部リージョン内に作成されます。 アプリのニーズに応じて、これらの値を変更します。

コマンドを実行したら、Azure portal に移動し、ご使用のマシンのプロビジョニングが開始されることを確認します。

![プロビジョニング中の仮想マシン](./media/chef-automation/virtual-machine-being-provisioned.png)

次にコマンド プロンプトが表示されます。

![仮想マシン作成時の Knife 出力](./media/chef-automation/knife-output-when-creating-vm.png)

デプロイが完了すると、新しい仮想マシンのパブリック IP アドレスが表示されます。 新しい Web サイトを表示するには、この値を Web ブラウザーに貼り付けます。 仮想マシンをデプロイしたときに、ポート 80 を開いたので、外部で利用可能になっているはずです。   

![仮想マシンのテスト](./media/chef-automation/testing-the-virtual-machine.png)

この例では、クリエイティブ HTML コードを使用します。

また、[Chef Manage](https://manage.chef.io/) 上でノードのステータスも閲覧できます。 

![ノードの状態の表示](./media/chef-automation/viewing-node-status.png)

また、ポート 3389 を経由して Azure portal から RDP セッションを通じて接続できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [Azure 上の Chef](/azure/chef/)