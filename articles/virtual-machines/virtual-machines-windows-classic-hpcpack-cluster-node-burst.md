<properties
 pageTitle="HPC Pack クラスターへのバースト ノードの追加 |Microsoft Azure"
 description="クラウド サービスで実行される worker ロール インスタンスを追加して、HPC Pack クラスターの容量をオンデマンドで拡張する方法について説明します"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/15/2016"
 ms.author="danlep"/>

# Azure での HPC Pack クラスターへのオンデマンドの "バースト" ノードの追加



この記事では、Azure の「バースト」ノード (クラウド サービスで実行されている worker ロール インスタンス) をコンピューティング リソースとして Azure の既存 HPC Pack ヘッド ノードに追加する方法について説明します。バースト ノードにより、事前構成された一連のコンピューティング ノード VMを保持しなくても、Azure の HPC クラスターの処理能力をオンデマンドでスケールアップまたはスケールダウンできます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![ノードをバーストする][burst]

この記事の手順を実行すれば、テストまたは概念実証のデプロイのために、クラウドベース HPC Pack ヘッド ノード VM に Azure ノードをすばやく追加できます。手順は基本的にオンプレミスの HPC Pack クラスターにクラウド コンピューティング能力を追加する「Azure へのバースト」の手順と同じです。チュートリアルが必要な場合、「[Microsoft HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)」を参照してください。詳細なガイドと本稼働デプロイにおける考慮事項については、「[Microsoft HPC Pack を使用した Azure へのバースト](https://technet.microsoft.com/library/gg481749.aspx)」を参照してください。

バースト ノードにコンピューティング集中型インスタンス サイズを使用する場合の考慮事項については、「[H シリーズとコンピューティング集中型 A シリーズのインスタンスについて](virtual-machines-windows-a8-a9-a10-a11-specs.md)」をご覧ください。

## 前提条件

* **HPC Pack ヘッド ノードを Azure VM にデプロイ済みである** -スタンドアロンのヘッド ノード VM または大規模なクラスターの一部であるヘッド ノード VM を使用することができます。スタンドアロンのヘッド ノードを作成するには、[Azure VM での HPC Pack ヘッド ノードのデプロイ](virtual-machines-windows-hpcpack-cluster-headnode.md)に関するページをご覧ください。自動化された HPC Pack クラスター デプロイ オプションについては、「[Microsoft HPC Pack を使用して Azure で Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプション](virtual-machines-windows-hpcpack-cluster-options.md)」をご覧ください。

    >[AZURE.TIP] [HPC Pack IaaS デプロイ スクリプト](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)を使用し、Azure でクラスターを作成する場合、自動化されたデプロイに Azure バーストを追加できます。この記事の例を参照してください。

* **Azure サブスクリプション** - Azure ノードを追加するには、ヘッド ノード VM をデプロイするための同じサブスクリプションを選択するか、別のサブスクリプションを選択します。

* **コア クォータ** - 場合によっては、コアのクォータを増やす必要があります。特に、マルチコア サイズの Azure ノードをいくつかデプロイする場合に必要となる可能性があります。クォータを増やすには、[オンライン カスタマー サポートに申請](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

## 手順 1: Azure ノード用のクラウド サービスとストレージ アカウントを作成する

Azure クラシック ポータルまたは同様のツールを利用し、Azure ノードをデプロイするために必要な次のリソースを構成します。

* 新しい Azure クラウド サービス
* 新しい Azure ストレージ アカウント

>[AZURE.NOTE] サブスクリプションでは既存のクラウド サービスを再利用しないでください。

**考慮事項**

* 作成する予定の Azure ノード テンプレートごとに別個のクラウド サービスを構成します。ただし、複数のノード テンプレートに同じストレージ アカウントを使用できます。

* デプロイ用のクラウド サービスとストレージ アカウントを同じ Azure リージョンに配置することをお勧めします。




## 手順 2: Azure 管理証明書の作成

Azure ノードをコンピューティング リソースとして追加するには、ヘッド ノードに管理証明書を用意し、デプロイに使用される Azure サブスクリプションに該当証明書をアップロードする必要があります。

このシナリオの場合、HPC Pack によりヘッド ノードに自動的にインストールされ、構成される**既定の HPC Azure 管理証明書**を選択できます。この証明書はテスト目的と概念実証のデプロイで役立ちます。この証明書を使用するには、ヘッド ノード VM からサブスクリプションに C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer ファイルをアップロードします。[Azure クラシック ポータル](https://manage.windowsazure.com)で証明書をアップロードするには、**[設定]** > **[Management Certificates (管理証明書)]** をクリックします。

管理証明書を構成するための追加オプションについては、「[Azure バースト デプロイの Azure 管理証明書を構成するためのシナリオ](http://technet.microsoft.com/library/gg481759.aspx)」を参照してください。

## 手順 3: クラスターに Azure ノードをデプロイする



このシナリオで Azure ノードを追加し、起動する手順は、おおむね、オンプレミス ヘッド ノードで使用される手順と同じです。詳しくは、「[Microsoft HPC Pack で Azure ノードをデプロイするための手順](https://technet.microsoft.com/library/gg481758.aspx)」の次のセクションを参照してください。

* Azure ノード テンプレートを作成する

* Azure ノードを Windows HPC クラスターに追加する

* Azure ノードを起動 (プロビジョニング) する

ノードを追加し、起動したら、ノードを利用してクラスター ジョブを実行できます。

Azure ノードのデプロイで問題が発生した場合は、「[Troubleshoot Deployments of Azure Nodes with Microsoft HPC Pack (Microsoft HPC Pack で Azure ノードをデプロイするときのトラブルシューティング)](http://technet.microsoft.com/library/jj159097.aspx)」をご覧ください。

## 次のステップ

* クラスターのワークロードに合わせて Azure コンピューティング リソースを自動的に拡大縮小する方法については、[HPC Pack クラスターでの Azure コンピューティング リソースの自動的な拡大縮小](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)に関する記事をご覧ください。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png

<!---HONumber=AcomDC_0928_2016-->