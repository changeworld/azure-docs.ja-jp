<properties urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="Windows Azure 多要素認証とは" metaKeywords="" description="Azure Multi-Factor Authentication (複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法) について説明します。" metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="Ruby を使用して Azure の仮想マシンを管理する方法" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Ruby を使用して Azure の仮想マシンを管理する方法

このガイドでは、Azure の仮想マシンの一般的な管理タスク (VM の作成と構成、データ ディスクの追加など) をプログラムで実行する方法について説明します。Azure SDK for Ruby は、Azure の仮想マシンを含め、さまざまな Azure サービスのサービス管理機能へのアクセスを提供します。

## 目次

-   [サービス管理とは][サービス管理とは]
-   [概念][概念]
-   [管理証明書の作成][管理証明書の作成]
-   [Ruby アプリケーションの作成][Ruby アプリケーションの作成]
-   [SDK を使用するようにアプリケーションを構成する][SDK を使用するようにアプリケーションを構成する]
-   [Azure 管理接続の設定][Azure 管理接続の設定]
-   [方法: 仮想マシンの操作][方法: 仮想マシンの操作]
-   [方法: イメージとディスクの操作][方法: イメージとディスクの操作]
-   [方法: クラウド サービスの操作][方法: クラウド サービスの操作]
-   [方法: ストレージ サービスの操作][方法: ストレージ サービスの操作]
-   [次のステップ][次のステップ]

## <a name="what-is"> </a>サービス管理とは

Azure には、Azure の仮想マシンの管理を含め、[サービス管理操作のための REST API][サービス管理操作のための REST API] が用意されています。Azure SDK for Ruby は、**Azure::VirtualMachineSerivce** クラスを通じて仮想マシンの管理操作を公開します。[Azure の管理ポータル][Azure の管理ポータル]を通じて使用できる仮想マシン管理機能の大半は、このクラスを使用してアクセスできます。

サービス管理 API を使用して、Azure にホストされたさまざまなサービスを管理することもできますが、このドキュメントでは、Azure の仮想マシンの管理に関する詳細のみを扱います。

## <a name="concepts"> </a>概念

Azure の仮想マシンは、クラウド サービス内では "ロール" として実装されます。各クラウド サービスには、デプロイへと論理的にグループ化される 1 つ以上のロールを含めることができます。ロールでは、使用可能なメモリの量、CPU コアの数など、VM の物理的な特性全体が定義されます。

各 VM には OS ディスクもあり、これには起動可能なオペレーティング システムが含まれます。VM には 1 つ以上のデータ ディスクを含めることができます。これは、アプリケーション データの格納に使用する追加ディスクです。ディスクは、Azure BLOB ストレージ内に格納される仮想ハード ドライブ (VHD) として実装されます。また、VHD は "イメージ" として公開されます。これは、VM の作成時に、VM によって使用されるディスクを作成するためのテンプレートです。たとえば、Ubuntu イメージを使用する新しい VM を作成すると、Ubuntu イメージから新しい OS ディスクが作成されます。

ほとんどのイメージは Microsoft またはパートナーから提供されますが、独自のイメージを作成することも、Azure にホストされた VM からイメージを作成することもできます。

## <a name="setup-certificate"> </a>Azure の管理証明書の作成

**Azure::VirtualMachineService** クラスによって公開されるものなど、サービス管理操作を実行するときは、Azure サブスクリプション ID とサブスクリプションの管理証明書を含むファイルを指定する必要があります。いずれも Azure REST API に対する認証を行う際に SDK によって使用されます。

サブスクリプション ID と管理証明書を取得するには、Azure クロスプラットフォーム コマンド ライン インターフェイス (xplat-cli) を使用します。xplat-cli のインストールと構成については、[Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成に関するページ][Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成に関するページ]を参照してください。

xplat-cli を構成した後で、次の手順で Azure サブスクリプション ID を取得し、管理証明書をエクスポートできます。

1.  サブスクリプション ID を取得するには、次のコマンドを使用します。

        azure account list

2.  管理証明書をエクスポートするには、次のコマンドを使用します。

        azure account cert export

    コマンドが完了すると、証明書は \<azure-subscription-name\>.pem という名前のファイルにエクスポートされます。たとえば、サブスクリプションが **mygreatsubscription** という名前の場合は、**mygreatsubscription.pem** という名前のファイルが作成されます。

サブスクリプション ID とエクスポートされた証明書を含む PEM ファイルの場所をメモしてください。このドキュメントの後の手順で使用します。

## <a name="create-app"></a>Ruby アプリケーションの作成

新しい Ruby アプリケーションを作成します。このドキュメントで使用するサンプルは、単一の **.rb** ファイルで実装できます。

## <a name="configure-access"></a>アプリケーションの構成

Azure サービスを管理するには、Azure SDK for Ruby が含まれる Azure gem をダウンロードして使用する必要があります。

### gem コマンドを使用してパッケージをインストールする

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (UNIX) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  次のコマンドを使用して Azure gem をインストールします。

        gem install azure

    次のような出力が表示されます。

        Fetching: mini_portile-0.5.1.gem (100%)
        Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
        Fetching: mime-types-1.25.gem (100%)
        Fetching: systemu-2.5.2.gem (100%)
        Fetching: macaddr-1.6.1.gem (100%)
        Fetching: uuid-2.3.7.gem (100%)
        Fetching: azure-0.5.0.gem (100%)
        Successfully installed mini_portile-0.5.1
        Successfully installed nokogiri-1.6.0-x86-mingw32
        Successfully installed mime-types-1.25
        Successfully installed systemu-2.5.2
        Successfully installed macaddr-1.6.1
        Successfully installed uuid-2.3.7
        Successfully installed azure-0.5.0
        7 gems installed

    <div class="dev-callout">
    <strong>注</strong>
    <p>アクセス許可関連のエラーが発生した場合は、代わりに <code>sudo gem install azure</code> を使用します。</p>
    </div>

### gem を要求する

テキスト エディターを使用して、Ruby アプリケーション ファイルの先頭に次の内容を追加します。これにより、Azure gem が読み込まれ、アプリケーションで Azure SDK for Ruby を使用できるようになります。

    require 'azure'

## <a name="setup-connection"> </a>方法: サービス管理への接続

Azure でサービス管理操作を正しく実行するには、「[Azure の管理証明書の作成][管理証明書の作成]」セクションで取得したサブスクリプション ID と証明書を指定する必要があります。その最も簡単な方法は、次の環境変数を使用して ID と証明書ファイルのパスを指定する方法です。

-   AZURE\_MANAGEMENT\_CERTIFICATE - 管理証明書を含む .PEM ファイルのパス。

-   AZURE\_SUBSCRIPTION\_ID - 使用している Azure サブスクリプションに対応するサブスクリプション ID。

これらの値は、アプリケーションでプログラムを使って設定することもできます。それには次のコマンドを使用します。

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

## <a name="virtual-machine"> </a>方法: 仮想マシンの操作

Azure の仮想マシンの管理操作は、**Azure::VirtualMachineService** クラスを使用して実行します。

### 方法: 新しい仮想マシンの作成

新しい仮想マシンを作成するには、**create\_virtual\_machine** メソッドを使用します。このメソッドは、次のパラメーターを含むハッシュを受け入れ、作成された VM を示す **Azure::VirtualMachineManagement::VirtualMachine** インスタンスを返します。

**パラメーター**

-   **:vm\_name** - 仮想マシンの名前。

-   **:vm\_user** - root または admin ユーザー名。

-   **:password** - root または admin ユーザーに使用するパスワード。

-   **:image** - この VM の OS ディスクの作成に使用する OS イメージ。OS ディスクは、BLOB ストレージ内に作成された VHD に格納されます。

-   **:location** - VM を作成するリージョン。この VM で使用するディスクを含むストレージ アカウントと同じリージョンである必要があります。

以下は、これらのパラメーターを使用して新しい仮想マシンを作成する例です。

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-ja-jp-30GB',
      :location = 'East US'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params)

    puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
    puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
    puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**オプション**

VM の作成の既定の動作をオーバーライドできるオプション パラメーターのハッシュを指定できます (新しいストレージ アカウントを作成せずに、既存のストレージ アカウントを指定するなど)。

**create\_virtual\_machine** メソッドを使用するときに、次のオプションを使用できます。

-   **:storage\_account\_name** - ディスク イメージの格納に使用するストレージ アカウントの名前。ストレージ アカウントが存在しない場合は、新たに作成されます。このオプションを省略した場合は、vm\_name パラメーターに基づいた名前で新しいストレージ アカウントが作成されます。

-   **:cloud\_service\_name** - 仮想マシンのホスティングに使用するクラウド サービスの名前。クラウド サービスが存在しない場合は、新たに作成されます。このオプションを省略した場合は、vm\_name パラメーターに基づいた名前で新しいクラウド サービス アカウントが作成されます。

-   **:deployment\_name** - 仮想マシンの構成をデプロイするときに使用するデプロイの名前。

-   **:tcp\_endpoints** - この VM で公開する TCP ポート。SSH エンドポイント (Linux ベースの VM) と WinRM エンドポイント (Windows ベースの VM) を指定する必要はありません。自動的に作成されます。コンマで区切って複数のポートを指定できます。異なるポート番号を使用して内部ポートをパブリック ポートに関連付ける場合は、**public port:internal port** という形式を使用します。たとえば、80:8080 と指定すると、内部ポート 8080 がパブリック ポート 80 として公開されます。

-   **:service\_location** - VM 上のターゲット証明書ストアの場所。Windows ベースの VM にのみ適用されます。

-   **:ssh\_private\_key\_file** - プライベート キーを含むファイル。Linux ベースの VM への SSH アクセスを保護するために使用されます。HTTPS トランスポートが選択されている場合は、WinRM の保護に使用する証明書を指定するためにも使用されます。**:ssh\_private\_key\_file** と **:ssh\_certificate\_file** を省略した場合、SSH ではパスワード認証のみが使用されます。

-   **:ssh\_certificate\_file** - 証明書ファイルを含むファイル。Linux ベースの VM への SSH アクセスを保護するために使用されます。HTTPS トランスポートが選択されている場合は、WinRM の保護に使用する証明書を指定するためにも使用されます。**:ssh\_private\_key\_file** と **:ssh\_certificate\_file** を省略した場合、SSH ではパスワード認証のみが使用されます。

-   **:ssh\_port** - SSH 通信に使用されるパブリック ポート。省略した場合は、SSH ポートは既定で 22 になります。

-   **:vm\_size** - VM のサイズ。これにより、メモリ サイズ、コアの数、帯域幅など、VM の物理的な特性が決まります。利用できるサイズと物理的な特性については、「[Azure の仮想マシンおよびクラウド サービスのサイズ][Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。

-   **:winrm\_transport** - WinRM で使用できるトランスポートの配列。有効なトランスポートは "http" と "https" です。トランスポートとして "https" を指定した場合は、**:ssh\_private\_key\_file** と **:ssh\_certificate\_file** を使用して、HTTPS 通信を保護するための証明書を指定する必要があります。

次に示すのは、S コンピューティング インスタンスを使用する新しい仮想マシンの作成例です。HTTP トラフィック (ローカル ポート 8080、パブリック ポート 80) と HTTPS トラフィック (443) 用にポートを公開し、指定の証明書ファイルを使用して SSH セッションに対して証明書認証を有効にします。

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-ja-jp-30GB',
      :location = 'East US'
    }

    vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

### 方法: 仮想マシンの一覧の表示

Azure サブスクリプションにおける既存の仮想マシンの一覧を表示するには、**list\_virtual\_machines** メソッドを使用します。このメソッドは、**Azure::VirtualMachineManagement::VirtualMachine** オブジェクトの配列を返します。

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### 方法: 仮想マシンの情報の取得

特定の仮想マシンに関する **Azure::VirtualMachineManagement::VirtualMachine** のインスタンスを取得するには、**get\_virtual\_machine** メソッドを使用し、仮想マシンとクラウド サービスの名前を指定します。

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### 方法: 仮想マシンの削除

仮想マシンを削除するには、**delete\_virtual\_machine** メソッドを使用し、仮想マシンとクラウド サービスの名前を指定します。

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

<div class="dev-callout">

**警告**
**delete\_virtual\_machine** メソッドは、仮想マシンに関連付けられているすべてのディスクとクラウド サービスを削除します。

</div>

### 方法: 仮想マシンのシャットダウン

仮想マシンをシャットダウンするには、**shutdown\_virtual\_machine** メソッドを使用し、仮想マシンとクラウド サービスの名前を指定します。

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### 方法: 仮想マシンの起動

仮想マシンを起動するには、**start\_virtual\_machine** メソッドを使用し、仮想マシンとクラウド サービスの名前を指定します。

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

## <a name="vm-images"> </a>方法: 仮想マシン イメージとディスクの操作

仮想マシン イメージに対する操作は、**Azure::VirtualMachineImageService** クラスを使用して実行します。ディスクに対する操作は、**Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService** クラスを使用して実行します。

### 方法: 仮想マシン イメージの一覧の表示

利用できる仮想マシン イメージの一覧を表示するには、**list\_virtual\_machine\_images** メソッドを使用します。このメソッドは、**Azure::VirtualMachineImageService** オブジェクトの配列を返します。

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### 方法: ディスクの一覧の表示

Azure サブスクリプションに対応するディスクの一覧を表示するには、**list\_virtual\_machine\_disks** メソッドを使用します。このメソッドは、**Azure::VirtualMachineImageManagement::VirtualMachineDisk** オブジェクトの配列を返します。

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### 方法: ディスクの削除

ディスクを削除するには、**delete\_virtual\_machine\_disk** メソッドを使用し、削除対象のディスクの名前を指定します。

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

## <a name="cloud-services"> </a>方法: クラウド サービスの操作

Azure クラウド サービスの管理操作は、**Azure::CloudService** クラスを使用して実行します。

### 方法: クラウド サービスの作成

新しいクラウド サービスを作成するには、**create\_cloud\_service** メソッドを使用し、オプションの名前とハッシュを指定します。有効なオプションは次のとおりです。

-   **:location** - *必須*。クラウド サービスの作成先リージョンです。

-   **:description** - クラウド サービスの説明。

以下のコマンドは、米国東部リージョンで新しいクラウド サービスを作成します。

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### 方法: クラウド サービスの一覧の表示

Azure サブスクリプションのクラウド サービスの一覧を表示するには、**list\_cloud\_services** メソッドを使用します。このメソッドは、**Azure::CloudServiceManagement::CloudService** オブジェクトの配列を返します。

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### 方法: クラウド サービスの有無の確認

特定のクラウド サービスが存在するかどうかを確認するには、**get\_cloud\_service** メソッドを使用し、クラウド サービスの名前を指定します。指定した名前のクラウド サービスが存在する場合は、**true** が返されます。それ以外の場合は、**false** が返されます。

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### 方法: クラウド サービスの削除

クラウド サービスを削除するには、**delete\_cloud\_service** メソッドを使用し、クラウド サービスの名前を指定します。

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### 方法: デプロイの削除

クラウド サービスのデプロイを削除するには、**delete\_cloud\_service\_deployment** メソッドを使用し、クラウド サービスの名前を指定します。

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

## <a name="storage-services"> </a>方法: ストレージ サービスの操作

Azure クラウド サービスの管理操作は、**Azure::StorageService** クラスを使用して実行します。

### 方法: ストレージ アカウントの作成

新しいストレージ アカウントを作成するには、**create\_storage\_account** メソッドを使用し、オプションの名前とハッシュを指定します。有効なオプションは次のとおりです。

-   **:location** - *必須*。ストレージ アカウントの作成先リージョンです。

-   **:description** - ストレージ アカウントの説明。

以下のコマンドは、"米国東部" リージョンで新しいストレージ アカウントを作成します。

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### 方法: ストレージ アカウントの一覧の表示

Azure サブスクリプションのストレージ アカウントの一覧を取得するには、**list\_storage\_accounts** メソッドを使用します。このメソッドは、**Azure::StorageManagement::StorageAccount** オブジェクトの配列を返します。

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### 方法: ストレージ アカウントの有無の確認

特定のストレージ アカウントが存在するかどうかを確認するには、**get\_storage\_account** メソッドを使用し、ストレージ アカウントの名前を指定します。ストレージ アカウントが存在する場合は **true** が返され、それ以外の場合は **false** が返されます。

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### 方法: ストレージ アカウントの削除

ストレージ アカウントを削除するには、**delete\_storage\_account** メソッドを使用し、ストレージ アカウントの名前を指定します。

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

## <a name="next-steps"> </a> 次のステップ

以上で、Azure の仮想マシンをプログラムで作成する方法の基本を説明しました。VM の操作方法については、次のリンク先を参照してください。

-   [仮想マシン][仮想マシン]の機能に関するページを参照
-   MSDN リファレンス: [仮想マシン][1]
-   [Ruby on Rails アプリケーションを仮想マシンで][Ruby on Rails アプリケーションを仮想マシンで]ホストする方法を確認

  [サービス管理とは]: #what-is
  [概念]: #concepts
  [管理証明書の作成]: #setup-certificate
  [Ruby アプリケーションの作成]: #create-app
  [SDK を使用するようにアプリケーションを構成する]: #configure-access
  [Azure 管理接続の設定]: #setup-connection
  [方法: 仮想マシンの操作]: #virtual-machine
  [方法: イメージとディスクの操作]: #vm-images
  [方法: クラウド サービスの操作]: #cloud-services
  [方法: ストレージ サービスの操作]: #storage-services
  [次のステップ]: #next-steps
  [サービス管理操作のための REST API]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460799.aspx
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成に関するページ]: http://www.windowsazure.com/ja-jp/manage/install-and-configure-cli/
  [Azure の仮想マシンおよびクラウド サービスのサイズ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn197896.aspx
  [仮想マシン]: http://www.windowsazure.com/ja-jp/documentation/services/virtual-machines/
  [1]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156003.aspx
  [Ruby on Rails アプリケーションを仮想マシンで]: http://www.windowsazure.com/ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/
