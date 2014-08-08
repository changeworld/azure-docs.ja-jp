<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Linux エージェント ガイド" pageTitle="Azure Linux エージェント ユーザー ガイド" metaKeywords="" description="Azure ファブリック コントローラーと仮想マシンとの相互動作を管理するために、Linux エージェント (waagent) をインストールして構成する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure Linux エージェント ユーザー ガイド" authors="" solutions="" manager="" editor="" />





#Azure Linux エージェント ユーザー ガイド

##はじめに

Azure Linux エージェント (waagent) は仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。Linux IaaS 展開用に次の機能が用意されています。

* **イメージのプロビジョニング**
  - ユーザー アカウントの作成
  - SSH 認証の種類の構成
  - SSH 公開キーおよびキー ペアの展開
  - ホスト名の設定
  - プラットフォーム DNS へのホスト名の発行
  - プラットフォームへの SSH ホスト キーの指紋のレポート
  - リソース ディスクの管理
  - リソース ディスクのフォーマットとマウント
  - スワップ領域の構成
* **ネットワーク**
  - プラットフォーム DHCP サーバーとの互換性を向上させるためのルートの管理
  - ネットワーク インターフェイス名の安定性の保持
* **カーネル**
  - 仮想 NUMA の構成
  - /dev/random の Hyper-V エントロピの使用
  - root デバイス (リモート デバイス) の SCSI タイムアウトの構成
* **Diagnostics**
  - コンソールからシリアル ポートへのリダイレクト
* **SCVMM の展開**
    - システムで実行されているときに Linux 用の VMM エージェントを検出およびブートストラップ
      Center Virtual Machine Manager 2012 R2 環境


プラットフォームからエージェントへの情報の流れは 2 つのチャンネルを経由します。

* 起動時に接続される IaaS 展開用 DVD。この DVD に、OVF に準拠した構成ファイルが収録されており、このファイルに、実際の SSH キー ペア以外のすべてのプロビジョニング情報が保存されています。

* 展開とトポロジの構成を取得するために使用する REST API を公開する TCP エンドポイント。

###Linux エージェントの入手
最新の Linux エージェントは次のいずれかから直接入手できます。

- [Azure 上での動作保証済みのさまざまな Linux ディストリビューションのプロバイダー](http://support.microsoft.com/kb/2805216)
- [GitHub の Azure Linux エージェント用オープン ソース リポジトリ](https://github.com/WindowsAzure/WALinuxAgent)


###サポートされている Linux ディストリビューション
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

サポートされるその他のシステム:

* FreeBSD 9+ (WALinuxAgent v2.0.0+)


###要件

Waagent が正しく機能するには次の該当するシステム パッケージが必要です。

* Python 2.5+
* Openssl 1.0+
* Openssh 5.3+
* ファイルシステム ユーティリティ: sfdisk、fdisk、mkfs
* パスワード ツール: chpasswd、sudo
* テキスト処理ツール: sed、grep
* ネットワーク ツール: ip-route

##インストール

お使いのディストリビューションのパッケージのリポジトリから RPM または DEB パッケージを使用してインストールする方法は、Windows Azure Linux Azure のインストールおよびアップグレードとしてお勧めする方法です。

手動のインストールでは、次のコマンドを実行して、waagent を /usr/sbin/waagent にコピーおよびインストールする必要があります。

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

エージェントのログ ファイルは /var/log/waagent.log に記録されます。


##コマンド ライン オプション

###フラグ

- verbose: 指定したコマンドのメッセージの詳細度を上げます。
- force: 一部のコマンドの対話形式の確認をスキップします。

###コマンド

- help: サポートされているコマンドとフラグを一覧表示します。

- install: エージェントを手動でインストールします。
 システムに対してインストールに必須の依存関係かあるかどうかを確認します。*

 *SysV の init スクリプト (/etc/init.d/waagent) と logrotate 構成ファイル (/etc/logrotate.d/waagent) を作成します。起動時に init スクリプトを実行するようにイメージを設定します。

 * サンプル構成ファイルを /etc/waagent.conf に書き込みます。

 * 既存の構成ファイルは、/etc/waagent.conf.old に移動されます。

 * カーネルのバージョンを検出し、必要に応じて VNUMA 回避策を適用します。

 * ネットワークを妨げる可能性のある udev ルール (/lib/udev/rules.d/75-persistent-net-generator.rules、/etc/udev/rules.d/70-persistent-net.rules) を /var/lib/waagent/ に移動します。

- uninstall: waagent と関連するファイルを削除します。
 * システムから init スクリプトを登録解除して削除します。

 * /etc/waagent.conf 内の logrotate 構成および waagent 構成ファイルを削除します。

 * インストール中に移動された udev ルールをすべて復元します。

 * VNUMA 回避策の自動復帰はサポートされていません。必要に応じて、手動で GRUB の構成ファイルを編集して、NUMA を再度有効にしてください。

- deprovision: システムをクリーンアップし、再プロビジョニングに適した状態にしようとします。この操作によって次のものが削除されます。
 * すべての SSH ホスト キー (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)

 * /etc/resolv.conf 内のネームサーバー構成

 * /etc/shadow の root パスワード (構成ファイルで Provisioning.DeleteRootPassword が 'y' の場合)

 * キャッシュされた DHCP クライアントのリース

 * ホスト名を localhost.localdomain にリセット

 **警告:** プロビジョニング解除により、イメージからすべての機密情報が削除され、イメージが再配布に適した状態になることが保証されるわけではありません。

- deprovision+user: -deprovision の場合のすべての対象 (上記参照) を実行するほか、前回プロビジョニングされたユーザー アカウント (/var/lib/waagent から取得) および関連付けられたデータも削除します。このパラメーターは、Azure で先にプロビジョニングしたイメージのプロビジョニングを解除するため、取得して再使用できます。

- version: waagent のバージョンを表示します。

- serialconsole: GRUB で ttyS0 (最初のシリアル ポート) がブート コンソールに
   なるように構成します。これにより、カーネルの起動ログがシリアル ポートに送信され、
   デバッグに使用できるようになります。

- daemon: プラットフォームとの相互動作を管理するデーモンとして waagent を実行します。
   この引数には waagent init スクリプト内で waagent が指定されます。

##Configuration

構成ファイル (/etc/waagent.conf) を使用して waagent の動作を制御します。
サンプル構成ファイルを次に示します。
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

さまざまな構成オプションについて次に詳述します。
構成オプションには、ブール、文字列、整数の 3 つの型があります。
ブール型の構成オプションは "y" または "n" として指定できます。
特別なキーワード "None" は、次に詳述しているように、一部の文字列型の構成オプションに使用できます。

**Role.StateConsumer:**

型: 文字列  
既定値: None


実行可能プログラムへのパスを指定した場合は、waagent がイメージのプロビジョニングを完了し、"Ready" 状態がファブリックにレポートされようとするときに呼び出されます。プログラムに指定された引数が "Ready" になります。エージェントはプログラムから制御が返るのを待たずに処理を続行します。

**Role.ConfigurationConsumer:**

型: 文字列  
既定値: None

実行可能プログラムへのパスを指定した場合、構成ファイルが仮想マシンに使用可能であることがファブリックによって通知されると、そのプログラムは呼び出されます。XML 構成ファイルへのパスは引数として実行可能プログラムに渡されます。このプログラムは、構成ファイルが変更されるたびに複数回呼び出すことができます。「付録」にサンプル ファイルを示しています。このファイルの現在のパスは /var/lib/waagent/HostingEnvironmentConfig.xml です。

**Role.TopologyConsumer:**

型: 文字列  
既定値: None

実行可能プログラムへのパスを指定した場合、新しいネットワーク トポロジ レイアウトが仮想マシンに使用可能であることがファブリックによって通知されると、そのプログラムは呼び出されます。XML 構成ファイルへのパスは引数として実行可能プログラムに渡されます。このプログラムは、ネットワーク トポロジが変更されるたびに (サービス復旧のためなど) 複数回呼び出すことができます。「付録」にサンプル ファイルを示しています。このファイルの現在の場所は /var/lib/waagent/SharedConfig.xml です。

**Provisioning.Enabled:**

型: ブール  
既定: y

エージェントのプロビジョニング機能を有効または無効にすることができます。有効な値は "y" または "n" です。プロビジョニングを無効にした場合、イメージ上の SSH ホストとユーザー キーが保持され、Azure プロビジョニング API で指定した構成はすべて無視されます。

**Provisioning.DeleteRootPassword:**

型: ブール  
既定: n

設定した場合、/etc/shadow 内の root パスワードがプロビジョニング プロセス中に消去されます。

**Provisioning.RegenerateSshHostKeyPair:**

型: ブール  
既定: y

設定した場合、すべての SSH ホスト キー ペア (ecdsa、dsa、rsa) がプロビジョニング プロセス中に /etc/ssh/ から削除されます。1 つの新しいキー ペアが生成されます。

新しいキー ペアの暗号化の種類は Provisioning.SshHostKeyPairType オプションで構成できます。一部のディストリビューションでは、SSH デーモンの再起動時 (再起動プロセス中など)、不足している暗号化の種類用に SSH キー ペアが再作成されることに注意してください。

**Provisioning.SshHostKeyPairType:**

型: 文字列  
既定: rsa

仮想マシンの SSH デーモンによってサポートされている暗号化アルゴリズムの種類を設定できます。一般的にサポートされている値は "rsa"、"dsa"、"ecdsa" です。Windows の "putty.exe" は "ecdsa" をサポートしていないことに注意してください。そのため、Windows の putty.exe を使用して Linux の展開に接続する場合は、"rsa" または "dsa" を使用してください。

**Provisioning.MonitorHostName:**

型: ブール  
既定: y

設定した場合、waagent は Linux 仮想マシンに対してホスト名の変更があるかどうか ("hostname" コマンドによって返される値) を監視し、変更があればイメージ上のネットワーク構成を自動的に更新して反映します。DNS サーバーに名前の変更をプッシュするために、仮想マシンでネットワークが再起動されます。これによりインターネット接続は短時間失われます。

**ResourceDisk.Format:**

型: ブール  
既定: y

設定した場合、"ResourceDisk.Filesystem" でユーザーによって要求されたファイル システムの種類が "ntfs" 以外であると、プラットフォームに用意されたリソース ディスクが waagent によってフォーマットされてマウントされます。ファイルシステムの種類が Linux (83) の 1 つのパーティションがディスク上で使用可能になります。このパーティションは、正常にマウント可能な場合、フォーマットされないことに注意してください。

**ResourceDisk.Filesystem:**

型: 文字列  
既定: ext4

リソース ディスクのファイルシステムの種類を指定します。サポートされている値は Linux ディストリビューションによって異なります。文字列が X の場合、mkfs.X は Linux イメージ上に存在する必要があります。SLES 11 イメージでは通常は 'ext3' を使用する必要があります。FreeBSD イメージではここで 'ufs2' を使用する必要があります。

**ResourceDisk.MountPoint:**

型: 文字列  
既定: /mnt/resource

リソース ディスクがマウントされるパスを指定します。

**ResourceDisk.EnableSwap:**

型: ブール  
既定: n

設定した場合、スワップ ファイル (/swapfile) がリソース ディスク上に作成され、システムのスワップ領域に追加されます。

**ResourceDisk.SwapSizeMB:**

型: 整数  
既定: 0

スワップ ファイルのサイズを MB 単位で指定します。

**LBProbeResponder:**

型: ブール  
既定: y

設定した場合、waagent はプラットフォームのロード バランサー プローブ (存在する場合) に応答します。

**Logs.Verbose:**

型: ブール  
既定: n

設定した場合、ログの詳細度が上がります。Waagent は /var/log/waagent.log にログを記録し、システムの logrotate 機能を利用してログをローテーションさせます。

**OS.RootDeviceScsiTimeout:**

型: 整数  
既定: 300

OS ディスクおよびデータ ドライブの SCSI タイムアウトを秒単位で構成します。設定しない場合、システムの既定値が使用されます。

**OS.OpensslPath:**

型: 文字列  
既定値: None

暗号化処理に使用する openssl バイナリの代替パスを指定します。

##付録

###サンプル ロール構成ファイル

	<?xml version="1.0" encoding="utf-8"?>
	<HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
	  <StoredCertificates>
	    <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
	  </StoredCertificates>
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
	  <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
	    <CAS mode="full" />
	    <PrivilegeLevel mode="max" />
	    <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
	    <ApplicationSettings>
	      <Setting name="__ModelData" value="&lt;m role=&quot;LinuxVM&quot; xmlns=&quot;urn:azure:m:v1&quot;>&lt;r name=&quot;LinuxVM&quot;>&lt;e name=&quot;HTTP&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput&quot; />&lt;e name=&quot;SSH&quot; />&lt;/r>&lt;/m>" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
	      <Setting name="startpage" value="Hello World!" />
	      <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
	    </ApplicationSettings>
	    <ResourceReferences>
	      <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore\" disableQuota="false" />
	    </ResourceReferences>
	  </HostingEnvironmentConfig>

###サンプル ロール トポロジ ファイル

	<?xml version="1.0" encoding="utf-8"?>
	<SharedConfig version="1.0.0.0" goalStateIncarnation="2">
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
	  <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
	    <Probes>
	      <Probe name="LinuxVM" />
	      <Probe name="03F7F19398C4358108B7ED059966EEBD" />
	      <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
	    </Probes>
	  </LoadBalancerSettings>
	  <OutputEndpoints>
	    <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
	      <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	    </Endpoint>
	  </OutputEndpoints>
	  <Instances>
	    <Instance id="LinuxVM_IN_1" address="10.115.38.202">
	      <FaultDomains randomId="1" updateId="1" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="80" to="80" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	          <RemoteInstances>
	            <RemoteInstance instance="LinuxVM_IN_0" />
	            <RemoteInstance instance="LinuxVM_IN_2" />
	          </RemoteInstances>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="20000" to="20000" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="22" to="22" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_0" address="10.115.58.82">
	      <FaultDomains randomId="0" updateId="0" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_2" address="10.115.58.148">
	      <FaultDomains randomId="0" updateId="2" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	  </Instances>
	</SharedConfig>

