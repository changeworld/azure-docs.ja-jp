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


Microsoft Azure SUSE Linux VM で SAP NetWeaver をテストする際に考慮すべき項目の一覧を次に示します。現時点では、SAP-Linux-Azure に対する正式な SAP サポートの発表はありません。ただし、正式な SAP サポートに頼ることはできませんが、お客様がある程度のテストやデモ、プロトタイプの作成を行うことはできます。

次の一覧は、いくつかの潜在的な落とし穴を回避し、作業をやりやすくすることに役立ちます。



## Microsoft Azure で SAP をテストするための SUSE イメージ 

Azure での SAP のテストには、SLES 11SP4 と SLES 12 のみを使用します。Azure イメージ ギャラリーには、特殊な SUSE イメージ "SLES 11 SP3 for SAP CAL" がありますが、これは一般的な使用を目的としたものではありません。これは SAP "CAL" と呼ばれる SAP Cloud Appliance Library ソリューション用に予約されています (<https://cal.sap.com/>)。パブリック ネットワークからこのイメージを非表示にするオプションがないため、使用しないでください。

Azure での新しいテストはすべて、Azure リソース マネージャーを使用して行う必要があります。Azure Powershell または CLI を使用して SUSE SLES イメージおよびバージョンを検索するには、次のコマンドを使用します。この出力は、新しい SUSE Linux VM をデプロイするために json テンプレートで OS イメージを定義することなどに使用できます。

* SUSE を含む既存の発行元の検索:

   ```
   PS  : Get-AzureVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* SUSE からの既存の提供を検索:
      
   ```
   PS  : Get-AzureVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
      
* SUSE SLES の提供を検索:
      
   ```
   PS  : Get-AzureVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```
      
* SLES sku の特定のバージョンを検索:
      
   ```
   PS  : Get-AzureVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```
     
## SUSE VM への WALinuxAgent のインストール 
 
このエージェントは、Azure ギャラリー内の SLES イメージの一部です。以下の場所で、手動インストールに関する情報を参照できます (オンプレミスから SLES OS vhd をアップロードする場合など)。

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## Azure Linux VM への Azure データ ディスクの接続

決してデバイス ID を使用して Azure データ ディスクを Azure Linux VM にマウントしないでください。代わりに UUID を使用してください。グラフィカル ツールなどを使用して Azure データ ディスクをマウントする場合には注意してください。/etc/fstab 内のエントリを再確認してください。デバイス ID の問題点は、変更する可能性があるため、Azure VM が起動プロセスで停止する場合があることです。/etc/fstab に nofail パラメーターを追加すると、問題を軽減できる可能性があります。ただし、nofail アプリケーションは、起動時に外付けの Azure データ ディスクがマウントされなかった場合には、以前と同じマウント ポイントを使用して、ルート ファイル システムに書き込む可能性があるため、注意が必要です。

## オンプレミスから Azure への SUSE VM のアップロード

次のブログでは、この手順について説明しています。 <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

最後にプロビジョニング解除手順を使用せずに VM をアップロードして、既存の SAP インストールやホスト名を保持する場合は、次の項目をチェックする必要があります。

* デバイス ID ではなく、UUID を使用して OS ディスクがマウントされていることを確認します。OS ディスクには、/etc/fstab で UUID に変更するだけでは十分ではありません。OS ディスクが以前のブート ローダーを覚えていたり、YaST 経由や /boot/grub/menu.lst の編集によりそのブート ローダーを適用したりする場合があります。
* SUSE OS ディスクに vhdx 形式を使用して、Azure にアップロードするために vhd に変換している場合、ネットワーク デバイスが eth0 から eth1 に変更される可能性が非常に高くなります。後で Azure で起動するときに、eth0 に戻さなければならない問題を回避する方法は、<https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/> を参照してください。

この記事に記載されていることに加え、以下も削除することをお勧めします。

   /lib/udev/rules.d/75-persistent-net-generator.rules

複数の NIC がある場合を除き、waagent をインストールして潜在的な問題を回避する必要もあります。

## SAP のライセンスとハードウェア キー

正式な SAP-Windows-Azure 証明書のために、SAP ライセンスに使用される SAP ハードウェア キーを計算するための新しいメカニズムが導入されました。SAP カーネルはこれを利用するように適合させる必要がありました。Linux 向けの SAP カーネルの現在のバージョンには、このコード変更が含まれていません。そのため、特定の状況 (Azure VM のサイズ変更など) では、SAP ハードウェア キーが変更され、SAP のライセンスが有効でなくなる場合があります。

## 分散 SAP インストールでの NFS 共有

たとえばデータベースと SAP アプリケーション サーバーを個別の VM にインストールしている分散インストールの場合、NFS を通じて /sapmnt ディレクトリを共有できます。/sapmnt の NFS 共有を作成した後のインストール手順で問題が発生した場合には、NFS 共有に "no\_root\_squash" が設定されているかどうかを確認します。これは内部テスト ケースでの解決策でした。


## 論理ボリューム

LVM は、Azure で完全に検証されていません。複数の Azure データ ディスクにまたがる大規模な論理ボリュームが必要な場合 (SAP データベース用など)、mdadm を使用する必要があります。mdadm を使用して Azure で Linux RAID を設定する方法を説明しているすばらしいブログを次に示します。

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## SUSE Azure リポジトリ

標準の Azure SUSE リポジトリへのアクセスに問題がある場合に、それをリセットする単純なコマンドがあります。この問題は、プライベート OS イメージを Azure のリージョンに作成した後、このイメージを別のリージョン (このプライベート OS イメージに基づいて新しい仮想マシンをデプロイするリージョン) にコピーする際に発生することがあります。VM 内で次のコマンドを実行します。

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
 
これは実際には Azure 固有のトピックではなく、全般的なものですが、理解しておくことが重要です。仮想化環境では、Linux 上の Oracle にはサポートの制約があります。つまり、Azure のようなパブリック クラウドでは、SUSE または RedHat 上の Oracle を SAP はサポートしないことを意味します。このトピックについては、お客様が直接 Oracle に問い合わせる必要があります。

<!---HONumber=AcomDC_1203_2015-->