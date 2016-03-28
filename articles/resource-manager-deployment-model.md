<properties
   pageTitle="リソース マネージャーと従来のデプロイ モデルの違いを理解する"
   description="リソース マネージャーのデプロイ モデルと従来 (あるいはサービス管理) のデプロイ モデルの違いについて説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# リソース マネージャー デプロイと従来のデプロイを理解する

リソース マネージャーのデプロイ モデルは、アプリケーションを構築するサービスをデプロイし、管理するための新しい方法を提供します。この新しいモデルには従来のデプロイとの重要な違いがあり、2 つのモデルは互いにまったく互換性を持ちません。リソースのデプロイと管理を簡単にするために、新しいリソースにはリソース マネージャーを利用し、可能であれば、リソース マネージャーで既存のリソースを再デプロイすることが推奨されています。

従来のデプロイ モデルはサービス管理モデルとしても知られています。

このトピックでは、2 つのモデルの違いと、従来のモデルからリソース マネージャーに移行するときに遭遇する問題について説明します。モデルの概要は説明しますが、個々のサービスの細かな違いについては取り上げません。

多くのリソースは従来のモデルとリソース マネージャーの両方で問題なく動作します。これらのリソースは、従来のモデルで作成された場合でも、リソース マネージャーに完全対応します。何の心配もなく、特別な労力も必要とせず、リソース マネージャーに移行できます。

ただし、いくつかのリソース プロバイダーはモデル間の構造の違いに起因して 2 つのバージョンのリソースを提供します (従来のモデルに 1 つ、リソース マネージャーに 1 つ)。2 つのモデルを区別するリソース プロバイダー:

- **Compute** - 仮想マシンとオプションの可用性セットのインスタンスをサポートします。
- **Storage** - 必要とされるオペレーティング システム ディスクと追加のデータ ディスク、仮想マシンの VHD を保存するストレージ アカウントをサポートします。
- **Network** - 必要とされる NIC、仮想マシンの IP アドレス、および仮想ネットワークとオプションのロード バランサー内のサブネット、Load Balancer の IP アドレスとネットワークのセキュリティ グループをサポートします。

これらの書類のリソースの場合、サポートされる操作が異なるため、使用しているバージョンに注意してください。Compute、Storage、Networking のリソースの移行に関する詳細については、「[Azure リソース マネージャーの Azure Compute、Network および Storage プロバイダー](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)」を参照してください。

## リソース マネージャーの特性

リソース マネージャーで作成したリソースには次の特性があります。

- 次のいずれかのメソッドで作成:

  - [Azure ポータル](https://portal.azure.com/)。

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        For Compute, Storage, and Networking resources, you have the option of using either Resource Manager or Classic deployment. Select **Resource Manager**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Azure PowerShell 1.0 より前のバージョンでは、**AzureResourceManager** モードでコマンドが実行されます。

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - Azure PowerShell 1.0 では、リソース マネージャー版のコマンドを使用してください。これらのコマンドは、次に示すように *Verb-AzureRmNoun* 形式となります。

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [Azure リソース マネージャー REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) (REST 操作の場合)。
  - Azure CLI コマンドは **arm** モードで実行されます。

            azure config mode arm

- リソース タイプの名前には **(classic)** は含まれません。下の画像ではタイプとして "**ストレージ アカウント**" が表示されています。

    ![Web アプリ](./media/resource-manager-deployment-model/resource-manager-type.png)

次の図のアプリケーションは、リソース マネージャーでデプロイしたリソースを 1 つのリソース グループに含める方法を示しています。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

さらに、リソース プロバイダー内にはリソース間の関係があります。

- 仮想マシンは BLOB ストレージにそのディスクを保存するための SRP で定義されている特定のストレージ アカウントに依存します。
- 仮想マシンは、NRP (必須) と、CRP で定義されている可用性セット (オプション) で定義されている、特定の NIC を参照します。
- NIC は、仮想マシンに割り当てられた IP アドレス (必須)、仮想ネットワークのサブネット(必須)、ネットワークのセキュリティ グループ (オプション) を参照します。
- 仮想ネットワーク内のサブネットは、ネットワークセキュリティ グループ (オプション) を参照します。
- Load Balancer のインスタンスは仮想マシンの NIC を含め(オプション)、Load Balancer パブリックまたはプライベート IP アドレス (オプション) を参照している IP アドレスのバックエンドプールを参照します。

## 従来のデプロイの特性

Azure サービス管理では、仮想マシンをホストするためのコンピューティング、ストレージ、またはネットワークは次によって提供されています:

- 仮想マシンをホストするためのコンテナーとして機能する必須のクラウド サービス (コンピューティング) 仮想マシンにはネットワーク インターフェイス カード (NIC) が自動的に提供され、IP アドレスは Azure によって割り当てられます。さらに、クラウド サービスには、外部のロード バランサーのインスタンス、パブリック IP アドレス、および Windows ベースのバーチャル マシンのリモート デスクトップとリモート PowerShell トラフィックと Linux ベースのバーチャル マシン用の Secure Shell (SSH) トラフィックを許可する既定のエンドポイントが含まれています。
- オペレーティング システム、一時、および追加のデータ ディスク (ストレージ) を含む、仮想マシンの VHD を格納するのに必要なストレージ アカウント。
- サブネット化された構造を作成できる、また仮想マシンが配置されているサブネットを指定することができる、追加のコンテナーとして機能する、省略可能な仮想ネットワーク (ネットワーク)。

従来のデプロイで作成されたリソースには次の特性があります。

- 次のいずれかのメソッドで作成:

  - [クラシック ポータル](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the Azure portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Azure PowerShell 1.0 より前のバージョンでは、**AzureServiceManagement** モードでコマンドが実行されます (これは既定のモードであり、AzureResourceManager に明示的に切り替えない場合、AzureServiceManagement モードで実行されます)。

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - Azure PowerShell 1.0 では、サービス管理版のコマンドを使用してください。これらのコマンド名は、次に示すように *Verb-AzureNoun* 形式となります。

            PS C:\> Get-AzureDeployment

  - REST 操作のための[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)
  - Azure CLI コマンドは **asm** または既定のモードで実行されます。
- リソース タイプの名前に **(classic)** が含まれます。下の画像ではタイプとして "**ストレージ アカウント (クラシック)**" が表示されています。

    ![従来のタイプ](./media/resource-manager-deployment-model/classic-type.png)

Azure ポータルを利用し、従来のデプロイで作成されたリソースを引き続き管理できます。

コンポーネントと Azure サービス管理のための関係を示します。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## リソース マネージャーとリソース グループを使用する利点

リソース マネージャーはリソース グループの概念を加えます。リソース マネージャーで作成したリソースはすべてリソース グループ内に存在します。リソース マネージャーのデプロイ モデルにはいくつかの利点があります。

- ソリューションのサービスを個別に処理するのではなく、すべてのサービスをデプロイ、管理、監視できます。
- アプリケーションをアプリのライフサイクルを通して繰り返しデプロイできます。常にリソースが一貫した状態でデプロイされます。
- 宣言型のテンプレートを利用し、デプロイを定義できます。
- 正しい順序でデプロイされるようにリソース間の依存性を定義できます。
- ロールベースの Access Control (RBAC) が管理プラットフォームにネイティブ統合されるため、リソース グループのすべてのリソースにアクセス制御を適用できます。
- タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。


リソース マネージャーの導入前は、従来のデプロイで作成されたリソースはリソース グループ内に存在しませんでした。リソース マネージャーが追加されたとき、すべてのリソースが遡及的に既定のリソース グループに追加されました。今、従来のデプロイでリソースを作成すると、デプロイ時にリソース グループを指定していなくても、リソースはそのサービスの既定のリソース グループ内に自動的に作成されます。ただし、リソース グループ内に存在するだけでは、リソースがリソース マネージャー モデルに変換されたことになりません。Virtual Machines、Storage、および Virtual Networks の場合、リソースが従来のデプロイで作成された場合、引き続き従来の操作でそれを操作する必要があります。

リソースを異なるリソース グループに移動したり、新しいリソースを既存のリソース グループに追加したりできます。そのため、リソース グループにはリソース マネージャーで作成されたリソースと従来のデプロイで作成されたリソースが混在します。リソースが同じ操作に対応しないため、そのような混在が予想外の結果を生むことがあります。

場合によっては、宣言型のテンプレートを利用し、デプロイのためにスクリプトを簡略化できます。サービス管理からリソース マネージャーに既存のスクリプトを変換する代わりに、デプロイ戦略を作り直し、テンプレートのインフラストラクチャと構成を定義することの利点を生かすことを検討してください。

## タグの使用

タグを利用すれば、リソースを論理的に整理できます。リソース マネージャーで作成したリソースだけがタグに対応しています。従来のリソースにタグを適用することはできません。

リソース マネージャーのタグの利用に関する詳細については、「[タグを利用して Azure リソースを整理する](resource-group-using-tags.md)」を参照してください。

## デプロイ モデルで対応している操作

従来のデプロイ モデルで作成したリソースはリソース マネージャーの操作に対応していません。場合によっては、リソース マネージャー コマンドで従来のデプロイで作成したリソースに関する情報を取得できます。あるいは、従来のリソースを別のリソース グループに移動するなどの管理タスクを実行できますが、この種類がリソース マネージャーの操作に対応するという印象を与えるべきではありません。たとえば、あるリソース グループにリソース マネージャーと従来のデプロイで作成された Virtual Machines が含まれるとします。次の PowerShell コマンドを実行すると、すべての Virtual Machines が表示されます。

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

しかしながら、Get-AzureRmVM コマンドを実行すると、リソース マネージャーで作成した Virtual Machines のみが表示されます。

    PS C:\> Get-AzureRmVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

一般的に、従来のデプロイで作成したリソースはリソース マネージャーのコマンドと連動しないと考えてください。

リソース マネージャーで作成したリソースを使用するとき、リソース マネージャーの操作を利用し、サービス管理の操作には切り替えないでください。

## Virtual Machines に関する考慮事項

Virtual Machines を使用するときには、重要な考慮事項がいくつかあります。

- 従来のデプロイ モデルで作成された仮想マシンはリソース マネージャーで作成された仮想ネットワークに含めることができません。
- リソース マネージャーのデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要があります。
- 従来のデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要がありません。

Virtual Machines のダウンロードに十分な時間を費やすことができる場合は、クラシック デプロイから [ASM2ARM PowerShell スクリプト](https://github.com/fullscale180/asm2arm)を使用するリソース マネージャーに移行できます。

従来のデプロイからリソース マネージャーに移行するときの同等の Azure CLI コマンドの一覧については、「[VM 操作のためのリソース マネージャーとサービス管理の同等コマンド](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md)」を参照してください。

Compute、Storage、Networking のリソースの移行に関する詳細については、「[Azure リソース マネージャーの Azure Compute、Network および Storage プロバイダー](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)」を参照してください。

さまざまなデプロイ モデルから仮想ネットワークを接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](./virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。

## 次のステップ

- 宣言型デプロイ テンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- テンプレートをデプロイするためのコマンドについては、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0316_2016-->