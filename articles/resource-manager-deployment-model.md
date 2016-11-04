---
title: Resource Manager とクラシック デプロイ | Microsoft Docs
description: Resource Manager のデプロイ モデルと従来 (あるいはサービス管理) のデプロイ モデルの違いについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: tomfitz

---
# Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について
このトピックでは、Azure Resource Manager と従来のデプロイ モデルについて、また、リソースの状態や、各リソースのデプロイにどちらの方法を使用したかについて説明します。Resource Manager デプロイ モデルには従来のデプロイとの重要な違いがあり、2 つのモデルは互いにまったく互換性を持ちません。リソースのデプロイと管理を簡単にするために、新しいリソースには Resource Manager を利用し、可能であれば、Resource Manager で既存のリソースを再デプロイすることが推奨されています。

これまでに Resource Manager を使用したことがない場合は、まず「[Azure Resource Manager の概要](resource-group-overview.md)」で定義されている用語をご確認ください。

## デプロイメント モデルの歴史
Azure では当初、クラシック デプロイメント モデルのみ用意されていました。このモデルでは、各リソースが独立して存在していたため、関連リソースをまとめてグループ化する方法がありませんでした。それどころか、ソリューションまたはアプリケーションを構成するリソースを手動で追跡し、うまくバランスを取りながら管理する必要がありました。ソリューションをデプロイするには、クラシック ポータル経由で各リソースを個別に作成するか、すべてのリソースを正しい順序でデプロイするスクリプトを作成する必要がありました。ソリューションを削除するには、各リソースを個別に削除するほかありませんでした。関連リソースのアクセス制御ポリシーの適用と更新は、簡単ではありませんでした。そのうえ、タグをリソースに適用し、リソースの監視と請求の管理に役立つ単語を使ってこれらのリソースにラベル付けすることもできませんでした。

2014 年、Azure に Resource Manager が導入され、リソース グループの概念が追加されました。リソース グループとは、共通のライフサイクルが共有されるリソースのコンテナーです。Resource Manager のデプロイ モデルにはいくつかの利点があります。

* ソリューションのサービスを個別に処理するのではなく、すべてのサービスをデプロイ、管理、監視できます。
* ソリューションをそのライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。
* リソース グループに属するすべてのリソースにアクセス制御を適用できます。これらのポリシーは、リソース グループに新しいリソースが追加されたときに自動的に適用されます。
* タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。
* JavaScript Object Notation (JSON) を使用してソリューションのインフラストラクチャを定義できます。JSON ファイルは Resource Manager テンプレートと呼ばれます。
* 正しい順序でデプロイされるようにリソース間の依存性を定義できます。

Resource Manager が追加されたとき、すべてのリソースが遡及的に既定のリソース グループに追加されました。今、従来のデプロイでリソースを作成すると、デプロイ時にリソース グループを指定していなくても、リソースはそのサービスの既定のリソース グループ内に自動的に作成されます。ただし、リソース グループ内に存在するだけでは、リソースが Resource Manager モデルに変換されたことになりません。次のセクションでは、各サービスにおける 2 つのデプロイメント モデルの扱われ方を見ていきます。

## モデルのサポートについて
リソースで使用するデプロイメント モデルを決定する際には、注意が必要なシナリオが 3 つあります。

1. サービスが Resource Manager をサポートしており、単一の種類にのみ対応している。
2. サービスが Resource Manager をサポートしているが、2 つの種類に対応している (一方は Resource Manager、もう一方はクラシック)。このシナリオは、仮想マシン、ストレージ アカウント、仮想ネットワークにのみ該当します。
3. サービスが Resource Manager をサポートしていない。

サービスが Resource Manager をサポートしているかどうかを確認するには、[Resource Manager でサポートされるプロバイダー](resource-manager-supported-services.md)についてのページを参照してください。

使用したいサービスが Resource Manager をサポートしていない場合、クラシック デプロイメントの使用を継続する必要があります。

サービスが Resource Manager をサポートしており、なおかつ仮想マシン、ストレージ アカウント、仮想ネットワークのいずれでも**ない**場合は、問題なく Resource Manager を使用できます。

仮想マシン、ストレージ アカウント、仮想ネットワークについては、クラシック デプロイメントでリソースが作成された場合、クラシックの操作でそのリソースを操作し続ける必要があります。仮想マシン、ストレージ アカウント、または仮想ネットワークが Resource Manager デプロイメントで作成された場合は、Resource Manager の操作を使用し続ける必要があります。このような区別があるため、Resource Manager デプロイメントで作成されたリソースとクラシック デプロイメントで作成されたリソースがサブスクリプション内に混在する場合は特に、混乱を招くおそれがあります。リソースが同じ操作に対応しないため、そのような混在が予想外の結果を生むことがあります。

場合によっては、Resource Manager コマンドを使用することで、クラシック デプロイメントで作成したリソースに関する情報を取得したり、クラシック リソースを別のリソース グループに移動するなどの管理タスクを実行したりできます。しかしこれらのケースから、この種類が Resource Manager の操作に対応しているという印象を持たないようにしてください。たとえば、クラシック デプロイメントで作成された仮想マシンを含むリソース グループがあるとします。例として、次の Resource Manager PowerShell コマンドを実行したとします。

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

すると、仮想マシンが返されます。

    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

ただし、Resource Manager コマンドレット **Get-AzureRmVM** を実行した場合は、Resource Manager でデプロイされた仮想マシンのみが返されます。次のコマンドでは、クラシック デプロイメントで作成された仮想マシンは返されません。

    Get-AzureRmVM -ResourceGroupName ExampleGroup

仮想マシンを使用するときには、その他にも重要な考慮事項がいくつかあります。

* 従来のデプロイ モデルで作成された仮想マシンは Resource Manager で作成された仮想ネットワークに含めることができません。
* Resource Manager のデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要があります。
* 従来のデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要がありません。

さまざまなデプロイ モデルから仮想ネットワークを接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](./virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。

Resource Manager で作成したリソースだけがタグに対応しています。従来のリソースにタグを適用することはできません。リソース マネージャーのタグの利用に関する詳細については、「[タグを利用して Azure リソースを整理する](resource-group-using-tags.md)」を参照してください。

## Resource Manager の特性
2 つのモデルをよく理解できるように、Resource Manager の種類の特性を確認してみましょう。

* [Azure ポータル](https://portal.azure.com/)を使用した作成。
  
     ![Azure ポータル](./media/resource-manager-deployment-model/portal.png)
  
     Compute、Storage、Networking のリソースについては、Resource Manager と従来のデプロイのどちらかを使用できます。**[リソース マネージャー]** を選択します。
  
     ![Resource Manager のデプロイ](./media/resource-manager-deployment-model/select-resource-manager.png)
* Resource Manager バージョンの Azure PowerShell コマンドレットを使用した作成。これらのコマンドは、次に示すように *Verb-AzureRmNoun* の形式となります。
  
        New-AzureRmResourceGroupDeployment
* REST 操作のための [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) を使用した作成。
* **arm** モードで実行される Azure CLI コマンドを使用した作成。
  
        azure config mode arm
        azure group deployment create 
* リソース タイプの名前には **(classic)** は含まれません。下の画像ではタイプとして "**ストレージ アカウント**" が表示されています。
  
    ![Web アプリ](./media/resource-manager-deployment-model/resource-manager-type.png)

次の図のアプリケーションは、Resource Manager でデプロイしたリソースを 1 つのリソース グループに含める方法を示しています。

  ![Resource Manager architecture](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

さらに、リソース プロバイダー内にはリソース間の関係があります。

* 仮想マシンは BLOB ストレージにそのディスクを保存するための SRP で定義されている特定のストレージ アカウントに依存します。
* 仮想マシンは、NRP (必須) と、CRP で定義されている可用性セット (オプション) で定義されている、特定の NIC を参照します。
* NIC は、仮想マシンに割り当てられた IP アドレス (必須)、仮想ネットワークのサブネット(必須)、ネットワークのセキュリティ グループ (オプション) を参照します。
* 仮想ネットワーク内のサブネットは、ネットワークセキュリティ グループ (オプション) を参照します。
* Load Balancer のインスタンスは仮想マシンの NIC を含め(オプション)、Load Balancer パブリックまたはプライベート IP アドレス (オプション) を参照している IP アドレスのバックエンドプールを参照します。

## 従来のデプロイの特性
従来のデプロイ モデルはサービス管理モデルとしても知られています。

従来のデプロイで作成されたリソースには次の特性があります。

* [クラシック ポータル](https://manage.windowsazure.com)を使用した作成。
  
     ![クラシック ポータル](./media/resource-manager-deployment-model/classic-portal.png)
  
     または、Azure ポータルを使い、**クラシック** デプロイメントを指定します (Compute、Storage、Networking の場合)。
  
     ![従来のデプロイ](./media/resource-manager-deployment-model/select-classic.png)
* サービス管理バージョンの Azure PowerShell コマンドレットを使用した作成。これらのコマンド名は、次に示すように *Verb-AzureNoun* の形式となります。
  
        New-AzureVM 
* REST 操作のための[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) を使用した作成。
* **asm** モードで実行される Azure CLI コマンドを使用した作成。
  
        azure config mode asm
        azure vm create 
* リソース タイプの名前に **(classic)** が含まれます。下の画像ではタイプとして "**ストレージ アカウント (クラシック)**" が表示されています。
  
    ![従来のタイプ](./media/resource-manager-deployment-model/classic-type.png)

Azure ポータルを利用し、従来のデプロイで作成されたリソースを引き続き管理できます。

Azure サービス管理では、仮想マシンをホストするためのコンピューティング、ストレージ、またはネットワークは次によって提供されています:

* 仮想マシンをホストするためのコンテナーとして機能する必須のクラウド サービス (コンピューティング) 仮想マシンにはネットワーク インターフェイス カード (NIC) が自動的に提供され、IP アドレスは Azure によって割り当てられます。さらに、クラウド サービスには、外部のロード バランサーのインスタンス、パブリック IP アドレス、および Windows ベースのバーチャル マシンのリモート デスクトップとリモート PowerShell トラフィックと Linux ベースのバーチャル マシン用の Secure Shell (SSH) トラフィックを許可する既定のエンドポイントが含まれています。
* オペレーティング システム、一時、および追加のデータ ディスク (ストレージ) を含む、仮想マシンの VHD を格納するのに必要なストレージ アカウント。
* サブネット化された構造を作成できる、また仮想マシンが配置されているサブネットを指定することができる、追加のコンテナーとして機能する、省略可能な仮想ネットワーク (ネットワーク)。

コンポーネントと Azure サービス管理のための関係を示します。

  ![classic architecture](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## クラシックから Resource Manager への移行
クラシック デプロイメントから Resource Manager デプロイメントにリソースを移行する準備ができたら、次のページを参照してください。

1. [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## 次のステップ
* 仮想マシン、ストレージ アカウント、仮想ネットワークを定義するテンプレートの作成に関するチュートリアルについては、「[Resource Manager テンプレートのチュートリアル](resource-manager-template-walkthrough.md)」を参照してください。
* Resource Manager テンプレートの構造について詳しくは、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* テンプレートをデプロイするためのコマンドについては、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->