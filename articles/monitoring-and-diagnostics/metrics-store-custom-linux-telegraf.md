---
title: Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する
description: Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990707"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する

Azure Monitor では、アプリケーション テレメトリ、Azure リソース上で実行されるエージェント、またはアウトサイドイン型の監視システムによってカスタム メトリックを収集し、それらを Azure Monitor に直接送信できます。 この記事では、[InfluxData](https://www.influxdata.com/) Telegraf エージェントを Azure 内の Linux VM にデプロイし、Azure Monitor にメトリックを発行するように構成する手順を中心に説明します。 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf エージェント 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/)は、150 を超えるさまざまなソースからのメトリックの収集を可能にする、プラグイン駆動型エージェントです。 VM で実行されているワークロード ( MySQL、NGINX、Apache など) に応じて、メトリックを収集するための特別な入力プラグインを利用するようにエージェントを構成できます。 その後、エージェントは、出力プラグインを使用して任意の宛先に書き込むことができます。 Telegraf エージェントは Azure Monitor のカスタム メトリック REST API と直接統合され、"Azure Monitor 出力プラグイン" をサポートします。 これにより、エージェントは、Linux VM でワークロード固有のメトリックを収集し、それらをカスタム メトリックとして Azure Monitor に送信できます。 

 ![Telegraph エージェントの概要](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>カスタム メトリックを送信する 

このチュートリアルでは、Ubuntu 16.04 LTS オペレーティング システムを実行する Linux VM をデプロイします。 Telegraf エージェントは、ほとんどの Linux オペレーティング システムでサポートされます。 InfluxData ダウンロード ポータルで、Debian パッケージと RPM パッケージの両方と、パッケージ化されていない Linux バイナリを入手できます。 Telegraf のインストール手順とオプションについては、このインストール ガイドを参照してください。 

[Azure Portal](https://portal.azure.com) にログインします。

新しい Linux VM を作成するには: 

1. 左側のナビゲーション ウィンドウで  **[リソースの作成]**  オプションをクリックします。 
1. 「*仮想ネットワーク*」を検索します。  
1. *[Ubuntu 16.04 LTS]* を選択し、**[作成]** をクリックします。 
1. VM 名 ( *MyTelegrafVM* など) を指定します。  
1. ディスクの種類は **[SSD]** のままにし、**[ユーザー名]**  *(azureuser など)* を指定します。 
1. " *[認証の種類]*" では  **[パスワード]** を選択し、後でこの VM に SSH するために使用するパスワードを入力します。 
1. [ **新しいリソース グループの作成**] を選択し、名前 ( *myResourceGroup* など) を指定します。  目的の [場所] を選択し、 **[OK]** を選択します。 

     ![Ubuntu VM の作成](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. VM のサイズを選択します。 [コンピューティングの種類] や [ディスクの種類] などでフィルター処理できます。 

     ![Telegraph エージェントの概要の仮想マシンのサイズ](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. [ **設定**] ページの  **[ネットワーク]** > **[ネットワーク セキュリティ グループ]** > **[Select public inbound ports]\(パブリック受信ポートの選択)** を選択し、 *[HTTP]* と  *[SSH (22)]* を選択します。 残りの部分は既定値のままにし、 **[OK]** を選択します。 

1. 概要ページで、[作成] を選択して、VM のデプロイを開始します。 

1. 対応する VM が、Azure portal のダッシュボードにピン留めされます。 デプロイが完了すると、VM の概要が自動的に表示されます。 

1. VM ブレードで、**[ID]** タブに移動し、システムによって割り当てられたVM の ID が *[オン]* になっていることを確認します。 
 
![fillin](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>VM に接続する 

VM との SSH 接続を作成します。 VM の概要ページの [接続] ボタンを選択します。 

![fillin](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

[Connect to virtual machine]\(仮想マシンへの接続\) ページで、ポート 22 を介して DNS 名で接続する既定のオプションをそのまま使用します。 [VM ローカル アカウントを使用してログインする] に、接続コマンドが表示されます。 ボタンをクリックしてこのコマンドをコピーします。 SSH 接続コマンドの例を次に示します。 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Azure Cloud Shell や Bash on Ubuntu on Windows などのシェルに SSH 接続コマンドを貼り付けるか、任意の SSH クライアントを使用して接続を作成します。 

## <a name="install-and-configure-telegraf"></a>Telegraf をインストールして構成する 

VM に Telegraf Debian パッケージをインストールするには、SSH セッションから次のコマンドを実行します。 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf の構成ファイルは、Telegraf の動作を定義します。 既定では、パス "/etc/telegraf/telegraf.conf" にサンプル構成ファイルがインストールされます。 このサンプル構成ファイルには、すべての可能な入力プラグインと出力プラグインが一覧表示されています。 ただし、これからカスタム構成ファイルを作成し、次のコマンドを実行することで、エージェントがそのファイルを使用するようにします。 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> 上記では "cpu" と "mem" という 2 つの入力プラグインのみを有効にしていますが、マシンで実行されるワークロードに応じて、さらにプラグインを追加できます (Docker、MySQL、NGINX など)。 入力プラグインの完全な一覧はここで見つけることができます。 

最後に、エージェントが新しい構成を使用して開始するように、次のコマンドを実行してエージェントを強制的に停止し、再び開始します。 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
これで、エージェントは、指定された各入力プラグインからメトリックを収集して Azure Monitor に出力するようになります。 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Azure portal で Telegraf メトリックをグラフ化する 

1. [Azure portal](https://portal.azure.com) を開きます。 

1. 新しい [監視] タブに移動し、 **[メトリック]** を選択します。  
     ![fillin](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. リソース セレクターで VM を選択します。

     ![fillin](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. *[Telegraf/CPU]* 名前空間を選択し、*[usage_system]* メトリックを選択します。 このメトリックをディメンションによってフィルター処理したり、分割したりすることを選択できます。  

     ![fillin](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>追加構成 

上のチュートリアルは、少数の基本的な入力プラグインからメトリックを収集するように Telegraf エージェントを構成する方法に関する情報を示しています。 Telegraf エージェントは 150 を超える入力プラグインをサポートしており、その一部は追加構成オプションをサポートしています。 InfluxData では、[サポートしているプラグインの一覧](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/)と[それらの構成方法](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)に関する手順を公開しています。  

さらに、このチュートリアルでは、Telegraf エージェントを使用して、エージェントがデプロイされている VM に関するメトリックを出力できるようにしています。 Telegraf エージェントは、他のリソースのメトリックのコレクターとフォワーダーとして使用することもできます。 他の Azure リソースのメトリックを出力するようにエージェントを構成する方法については、「[Azure Monitor Custom Metrics Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)」(Telegraf による Azure Monitor へのカスタム メトリックの出力) を参照してください。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 これを行うには、仮想マシンのリソース グループを選択し、[削除] を選択した後、削除するリソース グループの名前を確認します。 

## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。


