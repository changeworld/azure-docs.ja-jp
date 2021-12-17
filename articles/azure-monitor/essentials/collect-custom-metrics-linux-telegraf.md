---
title: InfluxData Telegraf エージェントを使用して Linux VM のカスタム メトリックを収集する
description: InfluxData Telegraf エージェントを Azure 内の Linux VM にデプロイし、Azure Monitor にメトリックを発行するように構成する手順。
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 2d30630e9c860f3a6cb5ea7808822f1c1ac850ab
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290573"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する

Azure Monitor を使用すると、アプリケーション テレメトリ、Azure リソース上で実行されるエージェント、またはアウトサイドイン型の監視システムによって、カスタム メトリックを収集できます。 さらに、それらを Azure Monitor に直接送信できます。 この記事では、[InfluxData](https://www.influxdata.com/) Telegraf エージェントを Azure 内の Linux VM にデプロイし、Azure Monitor にメトリックを発行するように構成する手順について説明します。 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf エージェント 

[Telegraf](https://docs.influxdata.com/telegraf/)は、150 を超えるさまざまなソースからのメトリックの収集を可能にする、プラグイン駆動型エージェントです。 VM 上で実行するワークロードに応じて、メトリックを収集するための特別な入力プラグインを利用するようにエージェントを構成できます。 たとえば、MySQL、NGINX、および Apache があります。 出力プラグインを使用することで、エージェントは、選択した変換先に記述できます。 Telegraf エージェントは Azure Monitor のカスタム メトリック REST API と直接統合されます。 また、Azure Monitor 出力プラグインをサポートします。 プラグインを使用することで、エージェントは、Linux VM でワークロード固有のメトリックを収集し、それらをカスタム メトリックとして Azure Monitor に送信できます。 

 ![Telegraph エージェントの概要](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> カスタム メトリックは、すべてのリージョンでサポートされているわけではありません。 サポートされているリージョンについては、[こちら](./metrics-custom-overview.md#supported-regions)の一覧を参照してください


 
## <a name="connect-to-the-vm"></a>VM に接続します 

VM との SSH 接続を作成します。 VM の概要ページの **[接続]** ボタンを選択します。 

![Telegraf VM の概要ページ](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

**[Connect to virtual machine]\(仮想マシンへの接続\)** ページで、ポート 22 を介して DNS 名で接続する既定のオプションをそのまま使用します。 **[VM ローカル アカウントを使用してログインする]** に、接続コマンドが表示されます。 ボタンをクリックして、このコマンドをコピーします。 SSH 接続コマンドの例を次に示します。 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Azure Cloud Shell や Bash on Ubuntu on Windows などのシェルに SSH 接続コマンドを貼り付けるか、または任意の SSH クライアントを使用して接続を作成します。 

## <a name="install-and-configure-telegraf"></a>Telegraf をインストールして構成する 

VM に Telegraf Debian パッケージをインストールするには、SSH セッションから次のコマンドを実行します。 

```bash
# download the package to the VM 
curl -s https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```

Telegraf の構成ファイルは、Telegraf の動作を定義します。 既定では、パス **/etc/telegraf/telegraf.conf** にサンプル構成ファイルがインストールされます。 このサンプル構成ファイルには、すべての可能な入力プラグインと出力プラグインが一覧表示されています。ただし、これからカスタム構成ファイルを作成し、次のコマンドを実行することで、エージェントがそのファイルを使用するようにします。 

```bash
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> 上記のコードでは、**cpu** と **mem** という 2 つの入力プラグインのみが使用可能になります。 コンピューター上で実行されるワークロードに応じて、さらに多くの入力プラグインを追加できます。 たとえば、Docker、MySQL、および NGINX です。 入力プラグインの完全な一覧については、「**追加構成**」セクションを参照してください。 

最後に、エージェントが新しい構成を使用して開始するように、次のコマンドを実行してエージェントを強制的に停止し、再び開始します。 

```bash
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
これで、エージェントは、指定された各入力プラグインからメトリックを収集して、Azure Monitor に出力するようになります。 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Azure portal で Telegraf メトリックをグラフ化する 

1. [Azure Portal](https://portal.azure.com)を開きます。 

1. 新しい **[監視]** タブに移動します。次に、 **[メトリック]** を選択します。  

     ![[モニター] - [メトリック] (プレビュー)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. リソース セレクターで VM を選択します。

     ![メトリック グラフ](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. **[Telegraf/CPU]** 名前空間を選択し、 **[usage_system]** メトリックを選択します。 このメトリックをディメンションによってフィルター処理したり、分割したりすることを選択できます。  

     ![名前空間とメトリックを選択する](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>追加構成 

上記のチュートリアルは、少数の基本的な入力プラグインからメトリックを収集するように Telegraf エージェントを構成する方法に関する情報を示しています。Telegraf エージェントは 150 を超える入力プラグインをサポートしており、その一部は追加構成オプションをサポートしています。 InfluxData では、[サポートしているプラグインの一覧](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/)と[それらの構成方法](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/)に関する手順を公開しています。  

さらに、このチュートリアルでは、Telegraf エージェントを使用して、エージェントがデプロイされている VM に関するメトリックを出力しました。 Telegraf エージェントは、他のリソースのメトリックのコレクターとフォワーダーとして使用することもできます。 他の Azure リソースのメトリックを出力するようにエージェントを構成する方法については、「[Azure Monitor Custom Metrics Output for Telegraf](https://github.com/influxdata/telegraf/blob/4b2e2c5263bb8bd030d2ae101438810c1af61945/plugins/outputs/azure_monitor/README.md)」(Telegraf による Azure Monitor へのカスタム メトリックの出力) を参照してください。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする 

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 そのためには、仮想マシンのリソース グループを選択し、 **[削除]** を選択します。 削除するリソース グループの名前を確認します。 

## <a name="next-steps"></a>次のステップ
- [カスタム メトリック](./metrics-custom-overview.md)の詳細を確認します。
