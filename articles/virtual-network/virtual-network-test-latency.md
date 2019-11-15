---
title: Azure Virtual Network で Azure Virtual Machines のネットワーク待機時間をテストする |Microsoft Docs
description: 仮想ネットワーク上の Azure 仮想マシン間のネットワーク待機時間をテストする方法について学ぶ
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587084"
---
# <a name="testing-network-latency"></a>ネットワーク待機時間をテストする

最も正確にネットワーク待機時間を測定するのは、測定用に設計されたツールを使用する方法です。 SockPerf (Linux 用) や Latte (Windows 用) などの一般で利用されているツールは、アプリケーションの待機時間など、他の種類の待機時間を除外しながら、ネットワーク待機時間を分離し、測定するツールの例です。 これらのツールは、アプリケーションのパフォーマンスに影響するネットワークトラフィックの種類 (TCP および UDP) に重点を置いています。 ping などのその他の一般的な接続ツールが待機時間の測定に使用されることがありますが、実際のワークロードで使用されるネットワーク トラフィックを表すものではない可能性があります。 これらのツールのほとんどは、アプリケーション トラフィックとは異なる方法で ICMP プロトコルを使用するため、TCP と UDP を使用するワークロードに結果が適用されない可能性があります。 ほとんどのアプリケーションで使用されるプロトコルの正確なネットワーク待機時間をテストするには、SockPerf (Linux 用) および Latte (Windows 用) を利用することで、最も関連性の高い結果が生成されます。 このドキュメントでは、どちらのツールについても説明します。

## <a name="overview"></a>概要

2 つの VM (1 つは送信側、もう 1 つは受信側) を使用して双方向の通信チャネルを作成し、ラウンド トリップ時間 (RTT) を測定するために双方向でパケットを送受信します。

これらの手順は、2 つの Virtual Machines (VM) 間または 2 台の物理コンピューター間のネットワーク待機時間を測定するために使用できます。 待機時間の測定は、次のシナリオで役に立ちます。

- デプロイされた VM 間のネットワーク待機時間のベンチマークを確立する
- 関連する変更が以下の項目に対して行われた後のネットワーク待機時間の変更の影響を比較する
  - 構成の変更を含む OS またはネットワーク スタック ソフトウェア
  - VM のデプロイ方法 (ゾーンまたは PPG へのデプロイなど)
  - 高速ネットワークやサイズ変更などの VM のプロパティ
  - 仮想ネットワーク (ルーティングやフィルターの変更など)

### <a name="tools-for-testing"></a>テスト用のツール
待機時間を測定するには、2 つの異なるオプションがあります。1つは Windows ベースのシステム用、もう 1 つは Linux ベースのシステム用です。

Windows の場合: latte.exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Linux の場合:SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

これらのツールを使用すると、アプリケーションによって使用されておらず、パフォーマンスに影響を与えない ICMP (Ping) や他の種類のパケットではなく、TCP または UDP ペイロードの配信時間だけが測定されます。

### <a name="tips-for-an-optimal-vm-configuration"></a>最適な VM 構成のヒント:

- 最新バージョンの Windows または Linux を使用する
- 最良の結果を得るため、高速ネットワークを有効にする
- [Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を使用して VM をデプロイする
- 通常、大規模な VM は小規模の VM よりもパフォーマンスが優れています

### <a name="tips-for-analysis"></a>分析に関するヒント

- デプロイ、構成、最適化が完了したらすぐにベースラインを確立する
- 変更を制御し、新しい結果を常にベースラインと比較するか、テストごとに比較する
- 変更が見つかった場合、または計画されるたびにテストを繰り返す


## <a name="testing-vms-running-windows"></a>Windows を実行している VM のテスト

## <a name="get-latteexe-onto-the-vms"></a>Latte.exe を VM に読み込む

最新バージョンをダウンロードする: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Latte.exe は、別のフォルダー (c:\tools など) に入れることを検討してください

## <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe が Windows ファイアウォールを通過することを許可する

"受信側" で、Windows ファイアウォールの許可ルールを作成して、Latte.exe トラフィックの到着を許可します。 特定の TCP ポートの受信を許可するのではなく、名前によって Latte.exe プログラム全体を許可する方が簡単です。

次のように Latte.exe が Windows ファイアウォールを通過することを許可します。

netsh advfirewall firewall add rule program=\<PATH\>\\latte.exe name=&quot;Latte&quot; protocol=any dir=in action=allow enable=yes profile=ANY


たとえば、latte.exe を &quot;c:\\tools&quot; フォルダーにコピーした場合、このコマンドは次のようになります。

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>待機時間テストを実行する

受信側で latte.exe を開始します (PowerShell からではなく CMD から実行します)。

latte -a &lt;受信側の IP アドレス&gt;:&lt;ポート&gt; -i &lt;イテレーション&gt;

65k 前後のイテレーションが、代表的な結果を返すのに十分な長さです。

使用可能な任意のポート番号で構いません。

VM に IP アドレス 10.0.0.4 がある場合、次のようになります。

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

送信側で latte.exe を開始します (PowerShell からではなく CMD から実行します)。

latte -c -a \<受信側の IP アドレス\>:\<ポート\> -i \<イテレーション\>


結果として得られるコマンドは、これが &quot;クライアント&quot; (送信側) であることを示す &quot;-c&quot; が追加されている点を除き、受信側と同じです

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

結果を待ちます。 VM の距離によっては、完了までに数分かかる場合があります。 長いテストを実行する前に、成功についてテストするために少数のイテレーションで開始することを検討してください。



## <a name="testing-vms-running-linux"></a>Linux を実行している VM のテスト

SockPerf を使用します。 これは [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf) で取得できます。

### <a name="install-sockperf-on-the-vms"></a>SockPerf を VM にインストールする

Linux VM (送信側と受信側の両方) で、次のコマンドを実行して、VM 上に SockPerf を準備します。 一般向けのディストリビューションのためのコマンドが用意されています。

#### <a name="for-rhel--centos-follow-these-steps"></a>RHEL/CentOS の場合は、以下の手順に従います。
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Ubuntu の場合は、以下の手順に従います。
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>すべてのディストリビューションについて、次の手順に従って SockPerf をコピーし、コンパイルしてインストールします。
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf を VM で実行する

SockPerf のインストールが完了すると、VM は待機時間テストを実行する準備が整います。 

まず、"受信側" で SockPerf を起動します。

使用可能な任意のポート番号で構いません。 この例では、ポート 12345 を使用します。
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
サーバーがリッスンしているので、クライアントは、リッスンしているポート (この場合は 12345) でサーバーへのパケットの送信を開始できます。

次の例に示すように、代表的な結果が返されるには約 100 秒あれば十分です。
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

結果を待ちます。 VM 同士がどれだけ離れているかによって、イテレーションの数は異なります。 長いテストを実行する前に、まずは約 5 秒の短いテストから開始して成功するかどうかをテストすることを検討してください。

この SockPerf の例では、一般的な平均サイズのパケットの 350 バイトのメッセージサイズが使用されています。 メッセージのサイズは、VM で実行されるワークロードをより正確に表すために、より高いまたは低いサイズに調整できます。


## <a name="next-steps"></a>次の手順
* [Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を使用して待機時間を改善する
* シナリオに合わせて [VM のネットワークを最適化](../virtual-network/virtual-network-optimize-network-bandwidth.md)する方法について学ぶ
* [仮想マシンに帯域幅が割り当てられる方法](../virtual-network/virtual-machine-network-throughput.md)に関するページを参照してください
* 「[Azure Virtual Network についてよく寄せられる質問 (FAQ)](../virtual-network/virtual-networks-faq.md)」を参照してください。