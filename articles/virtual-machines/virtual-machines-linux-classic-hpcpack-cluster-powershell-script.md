---
title: "PowerShell スクリプトで Linux HPC クラスターをデプロイする | Microsoft Docs"
description: "PowerShell スクリプトを実行し、Azure 仮想マシンで Linux HPC Pack クラスターをデプロイします。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 5dcbadd1e7f855f72539a9b6cd619ed3f014119c


---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>HPC Pack IaaS デプロイ スクリプトを使用し、Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成する
HPC Pack IaaS デプロイ PowerShell スクリプトを実行し、Linux ワークロード用に完全な HPC クラスターを Azure 仮想マシンにデプロイします。 このクラスターは、Windows Server と Microsoft HPC Pack を実行する Active Directory に参加するヘッド ノードと、HPC Pack でサポートされるいずれかの Linux ディストリビューションを実行するコンピューティング ノードとから成ります。 Windows ワークロード用に Azure で HPC Pack クラスターをデプロイする必要がある場合は「 [HPC Pack IaaS デプロイ スクリプトを使用し、Windows VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」をご覧ください。 Azure リソース マネージャーのテンプレートを使用して HPC Pack クラスターをデプロイすることもできます。 具体例については、「 [Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)」を参照してください。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>構成ファイルの例
次の構成ファイルでは、新しいドメイン コントローラーとドメイン フォレストが作成され、ローカル データベースを持つ 1 つのヘッド ノードと 10 の Linux コンピューティング ノードを含む HPC Pack クラスターがデプロイされます。 すべてのクラウド サービスは東アジアの場所に直接作成されます。 Linux コンピューティング ノードは、2 つのクラウド サービスと 2 つのストレージ アカウント (*MyLnxCNService01* と *mylnxstorage01* の *MyLnxCN-0001* ～ *MyLnxCN-0005*、*MyLnxCNService02* と *mylnxstorage02* の *MyLnxCN-0006* ～ *MyLnxCN-0010*) で作成されます。 コンピューティング ノードは OpenLogic CentOS バージョン 7.0 Linux イメージから作成されます。 

サブスクリプション名、アカウント名、サービス名には、実際の値を使用してください。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>トラブルシューティング
* **“VNet が存在しない” エラー** - 1 つサブスクリプションの下で HPC Pack IaaS デプロイ スクリプトを実行して Azure で複数のクラスターを同時にデプロイするとき、“VNet *VNet\_Name* が存在しない” というエラーで 1 つまたは複数のデプロイが失敗することがあります。
  このエラーが発生した場合、失敗したデプロイに対してスクリプトを再実行してください。
* **Azure Virtual Network からインターネットにアクセスできない** - デプロイ スクリプトを利用し、新しいドメイン コントローラーを含む HPC Pack クラスターを作成する場合、あるいは手動でヘッド ノード VM をドメイン コントローラーに昇格する場合、Azure Virtual Network の VM をインターネットに接続できないことがあります。 この問題は、フォワーダー DNS サーバーがドメイン コントローラーで自動的に構成されるとき、このフォワーダー DNS サーバーが適切に解決しない場合に発生することがあります。
  
    この問題を回避するには、ドメイン コントローラーにログオンし、フォワーダー構成設定を削除するか、有効なフォワーダー DNS サーバーを構成します。 これを行うには、サーバー マネージャーで、**[ツール]** >
    **[DNS]** をクリックして DNS マネージャーを開き、**[フォワーダー]** をダブルクリックします。

## <a name="next-steps"></a>次のステップ
* Linux コンピューティング ノードから成る HPC Pack クラスターにおける Linux ディストリビューションのサポート、データの移動、ジョブの送信については、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。
* クラスターの作成と Linux HPC ワークロードの実行をスクリプトで行うチュートリアルについては、以下のページを参照してください。
  * [Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して STAR-CCM+ を実行する](virtual-machines-linux-classic-hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


