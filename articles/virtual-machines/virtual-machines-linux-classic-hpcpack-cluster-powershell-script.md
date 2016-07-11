<properties
   pageTitle="PowerShell スクリプトで Linux HPC クラスターをデプロイする | Microsoft Azure"
   description="PowerShell スクリプトを実行し、Azure インフラストラクチャ サービスで Linux HPC Pack クラスターをデプロイする"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# HPC Pack IaaS デプロイ スクリプトを使用し、Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成する

クライアント コンピューターで HPC Pack IaaS デプロイ PowerShell スクリプトを実行し、Azure インフラストラクチャ サービス (IaaS) で Linux ワークロード用に完全な HPC クラスターをデプロイします。Windows ワークロード用に Azure で HPC Pack クラスターをデプロイする必要がある場合は「[HPC Pack IaaS デプロイ スクリプトを使用し、Windows VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)」を参照してください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## サンプル構成ファイル

### 例 1

次の構成ファイルでは、新しいドメイン フォレストが作成され、ローカル データベースを持つ 1 つのヘッド ノードと 20 の Linux コンピューティング ノードを含む HPC Pack クラスターがデプロイされます。すべてのクラウド サービスは東アジアの場所に直接作成されます。Linux コンピューティング ノードは、4 つのクラウド サービスと 4 つのストレージ アカウント (_MyLnxCNService01_ と _mylnxstorage01_ の _MyLnxCN-0001_ ～ _MyLnxCN-0005_、_MyLnxCNService02_ と _mylnxstorage02_ の _MyLnxCN-0006_ ～ _MyLnxCN-0010_、_MyLnxCNService03_ と _mylnxstorage03_ の _MyLnxCN-0011_ ～ _MyLnxCN-0015_、_MyLnxCNService04_ と _mylnxstorage04_ の _MyLnxCN-0016_ ～ _MyLnxCN-0020_) で作成されます。コンピューティング ノードは OpenLogic CentOS バージョン 7.0 Linux イメージから作成されます。

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
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## トラブルシューティング

* **「VNet が存在しない」エラー** - 1 つサブスクリプションの下で HPC Pack IaaS デプロイ スクリプトを実行して Azure で複数のクラスターを同時にデプロイするとき、「VNet *VNet\_Name* が存在しない」というエラーで 1 つまたは複数のデプロイが失敗することがあります。このエラーが発生した場合、失敗したデプロイに対してスクリプトを再実行してください。

* **Azure virtual network からインターネットにアクセスできない** - デプロイ スクリプトを利用し、新しいドメイン コントローラーを含む HPC Pack クラスターを作成する場合、あるいは手動でヘッド ノード VM をドメイン コントローラーに昇格する場合、Azure virtual network の VM をインターネットに接続できないことがあります。この問題は、フォワーダー DNS サーバーがドメイン コントローラーで自動的に構成されるとき、このフォワーダー DNS サーバーが適切に解決しない場合に発生することがあります。

    この問題を回避するには、ドメイン コントローラーにログオンし、フォワーダー構成設定を削除するか、有効なフォワーダー DNS サーバーを構成します。これを行うには、サーバー マネージャーで、**[ツール]** > **[DNS]** をクリックして DNS マネージャーを開き、**[フォワーダー]** をダブルクリックします。
    
## 次のステップ

* スクリプトを使用してクラスターを作成し、Linux HPC ワークロードを実行するチュートリアルについては、「[Azure の Linux コンピューティング ノード上で Microsoft HPC Packを使用して NAMD を実行する](virtual-machines-linux-classic-hpcpack-cluster-namd.md)」または「[Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)」を参照してください。

* Azure リソース マネージャーのテンプレートを使用して HPC Pack クラスターをデプロイすることもできます。具体例については、「[Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)」を参照してください。

<!---HONumber=AcomDC_0629_2016-->