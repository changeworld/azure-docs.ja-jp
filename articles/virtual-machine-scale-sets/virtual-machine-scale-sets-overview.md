---
title: "仮想マシン スケール セットの概要 | Microsoft Docs"
description: "仮想マシン スケール セットについて説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/25/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 54673cbc69e418bdbe7cb5791dfb5d20e7ebcb9f
ms.openlocfilehash: 2294587fd3f978a3f8383112ece329b47307db7a
ms.lasthandoff: 03/01/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Azure の仮想マシン スケール セットとは
仮想マシン スケール セットは、同一の VM のセットをデプロイおよび管理するために使用できる Azure 計算リソースです。 すべての VM が同じ構成になっており、VM を事前にプロビジョニングする必要がない、真の自動スケールをサポートするように設計されています。そのため、ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードなどを対象にした大規模サービスを簡単に構築できます。

コンピューティング リソースをスケール アウトしたりスケール インしたりする必要のあるアプリケーションでは、複数の障害ドメインと更新ドメインに対してスケール操作が暗黙的にバランシングされます。 VM スケール セットの概要については、 [Azure ブログでの発表](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)を参照してください。

以下のビデオで、VM スケール セットが詳しく解説されています。

* [Mark Russinovich が語る Azure スケール セット](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [仮想マシン スケール セットを Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>VM スケール セットの作成と管理
[Azure Portal](https://portal.azure.com) で VM スケール セットを作成するには、*[新規]* を選択して、検索バーに「スケール」と入力します。 検索結果として、"仮想マシン スケール セット" が表示されます。 そこから必要なフィールドを入力し、スケール セットをカスタマイズしてデプロイします。 また、ポータルには、CPU 使用率に基づいて基本的な自動スケール規則を設定するオプションも用意されています。

VM スケール セットは、個々の Azure Resource Manager VM と同様に、JSON テンプレートと [REST API](https://msdn.microsoft.com/library/mt589023.aspx) を使用して、定義およびデプロイすることもできます。 そのため、Azure リソース マネージャーの標準的なデプロイ方法を利用することができます。 テンプレートの詳細については、「 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」をご覧ください。

VM スケール セットのサンプル テンプレートは、 [こちら](https://github.com/Azure/azure-quickstart-templates) の Azure Quickstart templates GitHub リポジトリから入手できます (タイトルに *vmss* が付いているテンプレートを探してください)

これらのテンプレートの詳細ページには、ポータルのデプロイ機能にリンクしているボタンが表示されます。 VM スケール セットをデプロイするには、このボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。 リソースで大文字や大文字と小文字の混在がサポートされているかどうかが不明な場合は、常に小文字と数字のパラメーター値を使用する方が安全です。 以下のサイトで、VM スケール セット テンプレートについてやさしく解説したビデオをご覧いただけます。

[VM スケール セット テンプレートの解説](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>VM スケール セットのスケール アウトとスケール イン
VM スケール セット内の仮想マシンの数を増減するには、単に *capacity* プロパティを変更し、テンプレートを再デプロイします。 このように単純なので、Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する場合に、簡単に独自のカスタム スケール層を記述できます。

容量を変更するためにテンプレートを再デプロイする場合は、"SKU" プロパティ パケットと更新された容量だけが含まれている、非常に小さいテンプレートを定義できます。 その例は [こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

自動的にサイズを増減するスケール セットの作成手順については、「 [仮想マシン スケール セットでのマシンの自動スケール](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>VM スケール セットの監視
[Azure Portal](https://portal.azure.com) に、スケール セットの一覧が表示されます。また、各セットに含まれる VM の一覧やリソース使用量グラフなどの基本的なプロパティと操作も表示されます。 さらに詳しい情報については、 [Azure リソース エクスプローラー](https://resources.azure.com) を使用して VM スケール セットを表示してください。 VM スケール セットは、Microsoft.Compute の下のリソースなので、このサイトから以下のリンクを展開すると表示できます。

**[Subscriptions] -> 自分のサブスクリプション -> [resourceGroups] -> [providers] -> [Microsoft.Compute] -> [virtualMachineScaleSets] -> 自分の VM スケール セット-> etc.**

## <a name="vm-scale-set-scenarios"></a>VM スケール セットのシナリオ
このセクションでは、標準的な VM スケール セットのシナリオをいくつか紹介します。 一部の高レベルの Azure サービス (Batch、Service Fabric、Azure Container Service など) も、これらのシナリオを使用します。

* **VM スケール セット インスタンスへの RDP/SSH** - VM スケール セットは VNET の中に作成され、スケール セット内の個々の VM にはパブリック IP アドレスが割り当てられていません。 これは、望ましいことです。通常は、コンピューティング グリッド内のすべてのステートレス リソースに個別のパブリック IP アドレスを割り当てるための費用や管理オーバーヘッドを避けたいためです。また、これらの VM には VNET 内の他のリソース (ロード バランサー、スタンドアロン仮想マシンなど、パブリック IP アドレスを持つリソース) から簡単に接続できます。
* **NAT 規則を使用した VM への接続** - パブリック IP アドレスを作成し、ロード バランサーに割り当てることができます。そのうえで、IP アドレス上のポートを VM スケール セット内の VM 上のポートにマッピングする受信 NAT プールを定義できます。 次に例を示します。
  
  | から | 発信元ポート | 変換先 | 宛先ポート |
  | --- | --- | --- | --- |
  |  パブリック IP |ポート 50000 |vmss\_0 |ポート 22 |
  |  パブリック IP |ポート 50001 |vmss\_1 |ポート 22 |
  |  パブリック IP |ポート 50002 |vmss\_2 |ポート 22 |
  
   1 つのパブリック IP を使用しているスケール セット内のすべての VM への SSH 接続を NAT 規則で有効にする VM スケール セットの作成例は、 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   同じことを RDP と Windows で行う例は、 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **"Jumpbox" を使用して VM に接続** - VM スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM と VM スケール セット VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。 パブリック IP アドレスを作成し、スタンドアロン VM に割り当てる場合は、スタンドアロン VM に RDP または SSH 接続して、そのコンピューターから VM スケール セット インスタンスに接続できます。 これでお気付きかもしれませんが、単純な VM スケール セットは、本質的に、既定の構成のパブリック IP アドレスを持つ単純なスタンドアロン VM よりも安全です。
  
   たとえば、次のテンプレートでは、スタンドアロン VM を使用した単純なスケール セットがデプロイされます。[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **VM スケール セット インスタンスに対する負荷分散** - "ラウンド ロビン" 方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じたレイヤー&4; の負荷分散規則で Azure Load Balancer を構成できます。 指定されたプロトコル、間隔、および要求パスでポートを ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。 また Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) はスケール セットをサポートし、レイヤー&7; のより洗練された負荷分散シナリオに対応します。
  
   次のサンプルでは、Apache Web サーバーが実行される VM スケール セットを作成し、ロード バランサーを使用して各 VM が受ける負荷を分散します。[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (Microsoft.Network/loadBalancers というリソースの種類と、virtualMachineScaleSet の networkProfile および extensionProfile を確認してください)
* **PaaS クラスター マネージャーでのコンピューティング クラスターとしての VM スケール セットのデプロイ** - VM スケール セットは、次世代の worker ロールとして説明されることがあります。 有効な説明ですが、スケール セットの機能を PaaS v1 worker ロール機能と混同させる危険性があります。 ある意味で、VM スケール セットは、真の "worker ロール" または worker リソースを提供します。これにより、プラットフォーム/ランタイムに依存せず、カスタマイズ可能で、Azure Resource Manager IaaS に統合された、汎用コンピューティング リソースを利用できます。
  
   PaaS v1 worker ロールは、プラットフォーム/ランタイムのサポートの面では制限がありますが (Windows プラットフォーム イメージのみ)、VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。これらのサービスは、VM スケール セットでは "*まだ*" 利用できないか、Service Fabric などの他の高レベル PaaS サービスによって提供されます。 このような点を踏まえておけば、VM スケール セットは PaaS をサポートするインフラストラクチャであると見なすことができます。 つまり、 Service Fabric のような PaaS ソリューションや Mesos のようなクラスター マネージャーを、スケーラブルなコンピューティング層として VM スケール セット上に構築できます。
  
   このアプローチの一例として、次のテンプレートでは、コンテナー オーケストレーターを使用するスケール セットに基づいてクラスターが Azure Container Service によってデプロイされます。[https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM スケール セットのパフォーマンスとスケールのガイダンス
* 管理されていないディスクを備えたスケール セットを使用している場合は、ストレージ アカウントあたりの VM が 20 個を超えないように計画してください (*overprovision* プロパティを "false" に設定した場合を除く。設定した場合は、最大で 40 個まで可能)。 管理ディスクでは、これらのストレージ アカウントごとの制限は適用されません。
* 管理されていないディスクを備えたスケール セットを使用している場合は、各ストレージ アカウント名の最初の文字ができるだけ重複しないようにします。 [Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/) のサンプル VMSS テンプレートには、その方法の例が紹介されています。
* 管理されていないディスクを備えたカスタム VM を使用している場合は、1 つのストレージ アカウントで、VM スケール セットあたり 40 個以下の VM を計画します。 VM スケール セットのデプロイを開始する前に、イメージをストレージ アカウントに事前コピーする必要があります。 管理ディスクを備えたスケール セットは、最大 100 個のカスタム イメージの VM をサポートします。 詳細については、FAQ を参照してください。
* VNET あたり 4,096 個以下の VM を計画します。
* 作成できる VM の数は、デプロイ先のリージョンのコア クォータによって制限されます。 計算クォータの制限を緩和するには、現時点でクラウド サービスや IaaS v1 で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。 クォータを照会するには、Azure CLI コマンドの場合は `azure vm list-usage`、PowerShell コマンドの場合は `Get-AzureRmVMUsage` (1.0 以前のバージョンの PowerShell を使用している場合は `Get-AzureVMUsage`) を実行します。

## <a name="vm-scale-set-frequently-asked-questions"></a>VM スケール セットに関してよく寄せられる質問
**Q.** VM スケール セットには何個の VM を設定できますか?

**A.** 管理ディスクでは、スケール セットには、プラットフォーム イメージに基づいて 0 ～ 1,000 個の VM、またはカスタム イメージに基づいて 0 ～ 100 個の VM を含めることができます。 (独自のストレージ アカウントを定義する) 管理されていないディスクでは、プラットフォーム イメージの場合は 100 個、カスタム イメージの場合は最大 40 個の VM に制限されます (*overprovision* プロパティが "false" に設定されている場合は既定で 20 個)。これは、管理されていないディスクを備えたカスタム イメージは単一のストレージ アカウントに制限されるためです。

**Q.** VM スケール セット内でデータ ディスクはサポートされていますか?

**A.** はい。 管理ストレージが定義されたスケール セットでは、セット内のすべての VM に適用される、接続されたデータ ドライブの構成を定義できます。 管理ストレージが定義されていないスケール セットは、接続されたデータ ドライブを持ちません。 データを格納するための他のオプションを次に示します。

* Azure ファイル (SMB 共有ドライブ)
* OS ドライブ
* 一時ドライブ (ローカルで、Azure Storage にサポートされない)
* Azure データ サービス (Azure テーブル、Azure BLOB など)
* 外部データ サービス (リモート DB など)

**Q.** VM スケール セットは、どの Azure リージョンでサポートされていますか?

**A.** すべてのリージョンで VM スケール セットがサポートされています。

**Q.** カスタム イメージを使用して VM スケール セットを作成するにはどうすればよいですか?

**A.** vhdContainers プロパティを空白のままにし (または省略し)、イメージ プロパティの URI を指定します。 例: [201-vmss-windows-customimage].(https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**Q.** VM スケール セット容量を 20 から 15 に減らすと、どの VM が削除されますか?

**A.** 可用性を最大限に高めるために仮想マシンは、すべてのアップグレード ドメインと障害ドメインのスケール セットから均等に削除されます。 ID が最大の VM が最初に削除されます。

**Q.** その後、容量を 15 から 18 に増やす場合はどうなるでしょうか?

**A.** 容量を 18 に増やすと、 3 つの新しい VM が作成されます。 作成されるたびに、VM インスタンス ID は前の最大値に増分された値となります (例: 20、21、22) 。 VM は FD と UD 間に分散されます。

**Q.** VM スケール セットで複数の拡張機能を使用する場合、実行順序を強制できますか?

**A.** 直接的にではありませんが、customScript 拡張機能の場合、スクリプトで他の拡張機能が完了するまで待機できます ([たとえば、拡張機能ログを監視することによって](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)) を実行します。 拡張機能の実行順序についての詳しいガイダンスについては、ブログ記事「 [Extension Sequencing in Azure VM Scale Sets (Azure VM スケール セットにおける拡張機能の実行順序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)」を参照してください。

**Q.** VM スケール セットは、Azure 可用性セットと連携できますか?

**A.** はい。 VM スケール セットは、5 つの FD と 5 つの UD を持つ、暗黙的な可用性セットです。 virtualMachineProfile の下のものは、何も構成する必要はありません。 100 を超える VM を備えた VM スケール セットは、複数の可用性セットに相当する複数の "配置グループ" にまたがります。 VM の可用性セットは、VM のスケール セットと同じ VNET に存在できます。 一般的な構成では、(多くの場合、可用性セットに固有の構成を必要とする) 制御ノード VM とデータ ノードをスケール セットに配置します。

