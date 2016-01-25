<properties
   pageTitle="Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト | Microsoft Azure"
   description="Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト


Microsoft Azure SUSE Linux VM で SAP NetWeaver をテストする際に考慮すべき項目の一覧を次に示します。現時点では、SAP-Linux-Azure に対する正式な SAP サポートの発表はありません。ただし、正式な SAP サポートに依存していなければ、テストやデモ、プロトタイプの作成を行うことはできます。

以下の情報は、失敗の可能性を回避するために役立ちます。



## Microsoft Azure で SAP をテストするための SUSE イメージ

Azure での SAP テストでは、SUSE Linux Enterprise Server (SLES) 11 SP4 および SLES 12 のみを使用します。Azure Marketplace には、特殊な SUSE イメージ ("SLES 11 SP3 for SAP CAL") がありますが、これは一般的な使用を目的としたものではありません。[SAP Cloud Appliance Library](https://cal.sap.com/) ソリューション用に予約されています。このイメージを一般向けには非表示にするオプションがありませんでした。使用しないでください。

Azure でのすべての新しいテストには、Azure リソース マネージャーを使用する必要があります。Azure PowerShell または Azure コマンドライン インターフェイス (CLI) を使用して SUSE SLES イメージおよびバージョンを検索するには、次のコマンドを使用します。この出力は、新しい SUSE Linux VM をデプロイするために JSON テンプレートで OS イメージを定義することなどに使用できます。以下の PowerShell コマンドは、バージョン 1.0.1 以降の Azure PowerShell に対して有効です。

* SUSE を含む既存の発行元を検索:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* SUSE からの既存の提供を検索:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* SUSE SLES の提供を検索:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* SLES SKU の特定のバージョンを検索:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## SUSE VM への WALinuxAgent のインストール

WALinuxAgent というエージェントは、Azure Marketplace の SLES イメージの一部です。以下の場所で、手動インストールに関する情報を参照できます (オンプレミスから SLES OS VHD をアップロードする場合など)。

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Azure Linux VM への Azure データ ディスクの接続

決してデバイス ID を使用して Azure データ ディスクを Azure Linux VM にマウントしないでください。代わりに UUID を使用してください。グラフィカル ツールなどを使用して Azure データ ディスクをマウントする場合には注意してください。/etc/fstab 内のエントリを再確認してください。

デバイス ID の問題点は、変更される可能性があり、Azure VM が起動プロセスで停止する場合があることです。/etc/fstab に nofail パラメーターを追加すると、問題を軽減できる可能性があります。ただし、nofail アプリケーションは、起動時に外付けの Azure データ ディスクがマウントされなかった場合には、以前と同じマウント ポイントを使用して、ルート ファイル システムに書き込む可能性があるため、注意が必要です。

## オンプレミスから Azure への SUSE VM のアップロード

[この記事](virtual-machines-linux-create-upload-vhd-suse.md)では、SUSE VM をオンプレミスから Azure にアップロードするための手順について説明しています。

最後にプロビジョニング解除手順を使用せずに VM をアップロードして、既存の SAP インストールやホスト名を保持する場合は、次の項目をチェックしてください。

* デバイス ID ではなく、UUID を使用して OS ディスクがマウントされていることを確認します。OS ディスクの場合、/etc/fstab で UUID に変更するだけでは十分ではありません。YaST を経由したり /boot/grub/menu.lst を編集したりすることによるブート ローダーの適用も、忘れずに行ってください。
* SUSE OS ディスクに VHDX 形式を使用し、Azure にアップロードするために VHD に変換した場合、ネットワーク デバイスが eth0 から eth1 に変更される可能性が非常に高くなります。後で Azure で起動するときの問題を回避するために、[この記事](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)で説明されているように、eth0 に戻してください。

この記事に記載されているものの他に、以下も削除することをお勧めします。

   /lib/udev/rules.d/75-persistent-net-generator.rules

複数の NIC がある場合を除き、Azure Linux エージェント (waagent) をインストールすると、潜在的な問題を回避するために役立ちます。

## Azure への SUSE VM のデプロイ

JSON テンプレート ファイルを使用して、新しい Azure リソース マネージャー モデルに新しい SUSE VM を作成する必要があります。JSON テンプレート ファイルが作成されると、PowerShell の代わりに次の CLI コマンドを使用して VM をデプロイできます。

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` JSON テンプレート ファイルの詳細については、[この記事](resource-group-authoring-templates.md)と[この Web ページ](https://azure.microsoft.com/documentation/templates/)を参照してください。

CLI と Azure リソース マネージャーの詳細については、[この記事](xplat-cli-azure-resource-manager.md)を参照してください。

## SAP のライセンスとハードウェア キー

正式な SAP-Windows-Azure 証明書のために、SAP ライセンスに使用される SAP ハードウェア キーを計算するための新しいメカニズムが導入されました。SAP カーネルはこれを利用するように適合させる必要がありました。Linux 向けの SAP カーネルの現在のバージョンには、このコード変更が含まれていません。そのため、特定の状況 (Azure VM のサイズ変更など) では、SAP ハードウェア キーが変更され、SAP のライセンスが有効でなくなる場合があります。

## SUSE の sapconf パッケージ

SUSE には、SAP 固有の設定のセットを処理する、"sapconf" と呼ばれるパッケージが用意されています。このパッケージの詳細 (機能、インストール方法、使用方法など) については、[この SUSE ブログ記事](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/)と[この SAP ブログ記事](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)を参照してください。

## 分散 SAP インストールでの NFS 共有

たとえばデータベースと SAP アプリケーション サーバーを個別の VM にインストールしている分散インストールの場合、ネットワーク ファイル システム (NFS) を通じて /sapmnt ディレクトリを共有できます。/sapmnt の NFS 共有を作成した後のインストール手順で問題が発生した場合には、NFS 共有に "no\_root\_squash" が設定されているかどうかを確認します。これは内部テスト ケースでの解決策でした。


## 論理ボリューム

論理ボリューム マネージャー (LVM) は、Azure で完全には検証されていません。複数の Azure データ ディスクにまたがる大規模な論理ボリュームが必要な場合 (SAP データベース用など)、mdadm を使用する必要があります。[この記事](virtual-machines-linux-configure-raid.md)では、mdadm を使用して Azure で Linux RAID をセットアップする方法について説明しています。


## Azure SUSE リポジトリ

標準の Azure SUSE リポジトリへのアクセスに問題がある場合に、それをリセットする単純なコマンドがあります。この問題は、プライベート OS イメージを Azure リージョンに作成した後、このイメージを別のリージョン (このプライベート OS イメージに基づいて新しい VM をデプロイするリージョン) にコピーする際に発生することがあります。VM 内で次のコマンドを実行します。

   ```
   service guestregister restart
   ```

## Gnome デスクトップ

単一の VM 内に、SAP GUI、ブラウザー、SAP 管理コンソールなどを含む完全な SAP デモ システムをインストールするために Gnome デスクトップを使用したい場合のために、以下に Azure SLES イメージにインストールするためのちょっとしたヒントを示します。

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## クラウド内の Linux での SAP Oracle のサポート

仮想化環境では、Linux 上の Oracle にはサポートの制約があります。これは、Azure 固有のトピックではなく、一般的なトピックです。しかし、理解しておくことが重要です。Azure のようなパブリック クラウドでは、SUSE または Red Hat 上の Oracle を SAP はサポートしません。このトピックについては、お客様が直接 Oracle に問い合わせる必要があります。

<!---HONumber=AcomDC_0114_2016-->