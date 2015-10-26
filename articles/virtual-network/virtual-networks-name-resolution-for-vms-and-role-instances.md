<properties 
   pageTitle="VM とロール インスタンスの解決"
   description="Azure IaaS、ハイブリッド ソリューション、異なるクラウド サービス間、Active Directory、および独自 DNS サーバーの使用に関係する名前解決シナリオです"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="joaoma" />

# VM とロール インスタンスの名前解決

Azure を使用して IaaS、PaaS、ハイブリッド ソリューションをホストする方法によっては、作成する VM とロール インスタンスが互いに通信するように許可することが必要になる場合があります。IP アドレスを使用してこの通信を行うこともできますが、簡単に記憶できて変更されない名前を使用する方が、はるかに簡単です。

Azure でホストされているロール インスタンスと VM がドメイン名を内部 IP アドレスに解決する必要がある場合、次の 2 つの方法のいずれかを使用できます。

- [Azure で提供される名前解決](#azure-provided-name-resolution)

- [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)

どちらの名前解決方法を使用するかは、VM とロール インスタンスが互いに通信するために必要な方法によって決まります。

**次の表では、シナリオと対応する名前解決の方法を示します。**

| **シナリオ** | **名前解決の方法:** | **詳細情報:** |
|--------------|----------------------------------|-------------------------------|
| 同じクラウド サービス内に存在するロール インスタンスまたは VM の間での名前解決 | Azure で提供される名前解決 | [Azure で提供される名前解決](#azure-provided-name-resolution)|
| 同じ仮想ネットワーク内に存在する VM およびロール インスタンス間での名前解決 | Azure で提供される名前解決 (ARM ベースのデプロイのみ) または独自の DNS サーバーを使用した名前解決 | [Azure で提供される名前解決](#azure-provided-name-resolution)、[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)、および [DNS サーバーの要件](#dns-server-requirements) |
| 異なる仮想ネットワーク内に存在する VM およびロール インスタンス間での名前解決 | 独自 DNS サーバー使用の名前解決| [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)および [DNS サーバーの要件](#dns-server-requirements)|
| クロスプレミス: Azure 内のロール インスタンスまたは VM とオンプレミス コンピューターの間の名前解決| 独自 DNS サーバー使用の名前解決| [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)および [DNS サーバーの要件](#dns-server-requirements)|
| 内部 IP の逆引き参照| 独自 DNS サーバー使用の名前解決| [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)および [DNS サーバーの要件](#dns-server-requirements)|
| パブリックでないドメインの名前解決 (例: Active Directory ドメイン)| 独自 DNS サーバー使用の名前解決| [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)および [DNS サーバーの要件](#dns-server-requirements)|
| 仮想ネットワーク内ではなく、異なるクラウド サービスに存在するロール インスタンス間での名前解決| 適用不可。異なるクラウド サービス内にある VM とロール インスタンス間の接続は、仮想ネットワークの外側ではサポートされません。| 適用不可。|


## Azure で提供される名前解決

Azure では、パブリック DNS 名の解決と共に、同じ仮想ネットワークまたはクラウド サービス内に存在する VM とロール インスタンス用に内部の名前解決が提供されます。クラウド サービス内の VM やインスタンスは同じ DNS サフィックスを共有するため、ホスト名だけで十分です。従来の仮想ネットワークでは、異なるクラウド サービスでは異なる DNS サフィックスを使用しているため、FQDN が必要です。ARM ベースの仮想ネットワークでは、DNS サフィックスは仮想ネットワーク間で共通であるため、FQDN は必要ありません。また、DNS 名は、NIC または仮想マシンに割り当てることができます。Azure 提供の名前解決ではどのような構成も不要ありませんが、前の表で示すように、すべてのデプロイ シナリオに適しているわけではありません。

> [AZURE.NOTE]Web ロールおよび worker ロールの場合は、Azure Service Management REST API を使用して、ロール名とインスタンス番号に基づいてロール インスタンスの内部 IP アドレスにアクセスすることもできます。詳細については、「[サービス管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)」を参照してください。

### 機能と注意事項

**機能:**

- 使いやすさ: Azure 提供の名前解決を使用するための構成は必要ありません。

- Azure で提供される名前解決サービスは高可用性を備えているため、独自の DNS サーバーのクラスターを作成、管理する必要はありません。

- 同じクラウド サービス内に存在するロール インスタンスまたは VM の間で、FQDN を必要としない名前解決が提供されます。

- ARM ベースの仮想ネットワーク内の VM 間では FQDN を必要としない名前解決が提供されます。従来の仮想ネットワークでは、異なるクラウド サービスで名前を解決するときには FQDN が必要です。

- 自動生成される名前を使用するのではなく、デプロイの内容がよくわかるホスト名を作成できます。

**考慮事項:**

- 仮想ネットワークの間や、Azure とオンプレミスのマシンの間では、名前解決は使用できません。

- Azure によって作成される DNS サフィックスは変更できません。

- 独自のレコードを手動で登録することはできません。

- WINS と NetBIOS はサポートされません (Windows エクスプローラーのネットワーク ブラウザーで仮想マシンを一覧表示することはできません)。

- ホスト名は DNS 互換である必要があります (使用できる文字は 0 ～ 9、a ～ z、'-' のみであり、最初または最後の文字として '-' は使用できません。RFC 3696 セクション 2 を参照してください)。

- DNS クエリ トラフィックは VM ごとに調整されます。このことは、ほとんどのアプリケーションには影響がありません。要求の調整が発生した場合は、クライアント側のキャッシュが有効になっていることを確認します。詳細については、「[Azure で提供される名前解決から最大限の効果を得る](#Getting-the-most-from-Azure-provided-name-resolution)」を参照してください。

- 最初の 180 のクラウド サービス内の VM だけが、従来の各仮想ネットワークに対して登録されます。このことは、ARM ベースの仮想ネットワークには当てはまりません。


### Azure で提供される名前解決から最大限の効果を得る
**クライアント側のキャッシュ**:

DNS クエリには、ネットワーク経由で送信する必要がないものもあります。クライアント側のキャッシュは、ローカル キャッシュから繰り返される DNS クエリを解決することで、待機時間を短縮し、ネットワークの停止に対する復元性を高めることができます。DNS レコードには、有効期限 (TTL) が含まれています。有効期限により、キャッシュは、レコードの鮮度に影響を与えずに、可能な限り長い時間レコードを格納できます。よって、クライアント側のキャッシュはほとんどの状況に適しています。

既定の Windows DNS クライアントには、組み込みの DNS キャッシュがあります。Linux ディストリビューションの一部では、既定でキャッシュは含まれていません。キャッシュを各 Linux VM に追加することをお勧めします。dnsmasq など、多様な DNS キャッシュ パッケージを利用できます。ここでは、最も一般的なディストリビューションに dnsmasq をインストールする手順を示します。

- **Ubuntu (resolvconf を使用)**:
	- dnsmasq パッケージをインストールする ("sudo apt-get install dnsmasq")
- **SUSE (netconf を使用)**:
	- dnsmasq パッケージをインストールする ("sudo zypper install dnsmasq") 
	- dnsmasq サービスを有効にする ("systemctl enable dnsmasq.service") 
	- dnsmasq サービスを開始する ("systemctl start dnsmasq.service") 
	- "/etc/sysconfig/network/config" を編集し、NETCONFIG\_DNS\_FORWARDER="" を "dnsmasq" に変更する
	- キャッシュをローカル DNS リゾルバーとして設定するよう resolv.conf を更新する ("netconfig update")
- **OpenLogic (NetworkManager を使用)**:
	- dnsmasq パッケージをインストールする ("sudo yum install dnsmasq")
	- dnsmasq サービスを有効にする ("systemctl enable dnsmasq.service")
	- dnsmasq サービスを開始する ("systemctl start dnsmasq.service")
	- "prepend domain-name-servers 127.0.0.1;" を "/etc/dhclient-eth0.conf" に追加する
	- ネットワーク サービスを再起動し ("service network restart")、ローカル DNS リゾルバーとしてキャッシュを設定する

[AZURE.NOTE]\: 'dnsmasq' パッケージは、Linux で使用可能な多くの DNS キャッシュの 1 つにすぎません。使用する前に、目的とするニーズに適合するかどうかと、その他のキャッシュがインストールされていないことを確認してください。

**クライアント側の再試行**

DNS では、主に UDP プロトコルが使用されます。UDP プロトコルでは、メッセージの配信が保証されないため、再試行ロジックは、DNS プロトコル自体で処理されます。各 DNS クライアント (オペレーティング システム) では、作成者の選択に応じて、再試行ロジックが異なる場合があります。

 - Windows オペレーティング システムでは、1 秒後に再試行されます。その後、2 秒後、4 秒後に再試行され、さらにもう一度その 4 秒後に再試行されます。 
 - 既定の Linux の設定では、5 秒後に再試行されます。1 秒間隔で 5 回再試行するよう、この設定を変更することをお勧めします。  

Linux VM の現在の設定を確認するには、次に示す 'cat/etc/resolv.conf' の 'options' 行を確認します。

	options timeout:1 attempts:5

resolv.conf ファイルは、通常は自動生成され、編集する必要はありません。'options' 行を追加する具体的な手順は、ディストリビューションによって異なります。

- **Ubuntu** (resolvconf を使用):
	- options 行を '/etc/resolveconf/resolv.conf.d/head' に追加する 
	- ' resolvconf -u' を実行して更新する
- **SUSE** (netconf を使用):
	- 'timeout:1 attempts:5' を '/etc/sysconfig/network/config' の NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" パラメーターに追加する 
	- 'netconfig update' を実行して更新する
- **OpenLogic** (NetworkManager を使用):
	- 'echo "options timeout:1 attempts:5"' を '/etc/NetworkManager/dispatcher.d/11-dhclient' に追加する 
	- 'service network restart' を実行して更新する

## 独自 DNS サーバー使用の名前解決

Azure によって提供される機能では名前解決の要件に対応できない場合は、独自の DNS サーバーを使用できます。独自の DNS サーバーを使用する場合は、DNS レコードを管理する必要があります。

> [AZURE.NOTE]デプロイ シナリオで必要な場合を除き、外部 DNS サーバーは使用しないことをお勧めします。

## DNS サーバーの要件

Azure によって提供されない名前解決を使用する場合は、指定する DNS サーバーが以下の要件をサポートしている必要があります。

- DNS サーバーが動的 DNS (DDNS) プロトコルによる動的な DNS 登録を受け付ける必要があります。または、必要なレコードを自分で作成する必要があります。

- 動的 DNS を使用する場合は、DNS サーバーではレコードの清掃をオフにする必要があります。Azure では、IP アドレスの DHCP リース期間が長く、清掃中に DNS サーバーからレコードが削除される可能性があります。

- DNS サーバーでは、再帰を有効にして外部ドメイン名の解決を可能にする必要があります。

- DNS サーバーには、名前解決を要求するクライアント、および名前を登録するサービスと仮想マシンから、(TCP/UDP ポート 53 で) アクセスできる必要があります。

- また、多くのボットが開いている再帰 DNS リゾルバーをスキャンしているので、インターネットからのアクセスに対して DNS サーバーをセキュリティで保護することをお勧めします。

- 最適なパフォーマンスを得るには、DNS サーバーとして Azure VM を使用する場合、IPv6 を無効にして、[インスタンスレベル パブリック IP](virtual-networks-instance-level-public-ip.mp) を各 DNS サーバーの VM に割り当てる必要があります。

## DNS サーバーの指定

VM およびロール インスタンスで使用するように複数の DNS サーバーを指定できます。DNS クエリごとに、クライアントはまず優先 DNS サーバーを試した後、優先サーバーが応答しない場合にのみ代替サーバーを試します。つまり、この場合は DNS クエリが複数の DNS サーバー上で負荷分散されていません。このため、環境に適した正しい順序で DNS サーバーがリストされていることを確認してください。

> [AZURE.NOTE]既にデプロイされている仮想ネットワークのネットワーク構成ファイルで DNS の設定を変更した場合、変更を有効にするには各 VM を再起動する必要があります。

### 管理ポータルでの DNS サーバーの指定

管理ポータルで仮想ネットワークを作成するとき、使用する DNS サーバーの IP アドレスと名前を指定できます。仮想ネットワークが作成されると、仮想ネットワークにデプロイした仮想マシンとロール インスタンスは、指定した DNS 設定で自動的に構成されます。特定のクラウド サービス (Azure クラシック) またはネットワーク インターフェイス カード (ARM ベースのデプロイ) に対して指定した DNS サーバーは、仮想ネットワークに対して指定した DNS サーバーよりも優先されます。「[管理ポータルでの仮想ネットワークの構成](virtual-networks-settings.md)」を参照してください。

### 構成ファイルを使用した DNS サーバーの指定 (Azure クラシック)

従来の仮想ネットワークの場合、*ネットワーク構成*ファイルおよび*サービス構成*ファイルの 2 種類の構成ファイルを使用して、DNS 設定を指定できます。

ネットワーク構成ファイルには、サブスクリプションの仮想ネットワークについて記述されます。クラウド サービス独自の DNS サーバーが指定されている場合を除き、ロール インスタンスまたは VM を仮想ネットワークのクラウド サービスに追加すると、ネットワーク構成ファイルの DNS 設定がロール インスタンスまたは VM に適用されます。

サービス構成ファイルは、Azure に追加するクラウド サービスごとに作成されます。ロール インスタンスまたは VM をクラウド サービスに追加すると、サービス構成ファイルの DNS 設定が、各ロール インスタンスまたは VM に適用されます。

> [AZURE.NOTE]サービス構成ファイル内の DNS サーバーは、ネットワーク構成ファイル内の設定より優先されます。


## 次のステップ

[Azure サービス構成スキーマ](https://msdn.microsoft.com/library/azure/ee758710)

[仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100)

[管理ポータルでの仮想ネットワークの設定について](virtual-networks-settings.md)

[ネットワーク構成ファイルを使用した仮想ネットワークの構成](virtual-networks-using-network-configuration-file.md)

<!---HONumber=Oct15_HO3-->