<properties
 pageTitle="Azure VM で HPC Pack ヘッド ノードを作成する | Microsoft Azure"
 description="Azure ポータルとリソース マネージャーを利用し、Azure VM で Microsoft HPC Pack ヘッド ノードを作成する方法について説明します。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="05/19/2016"
 ms.author="danlep"/>

# Marketplace イメージを利用し、Azure VM で HPC Pack クラスターのヘッド ノードを作成する


Azure Marketplace の [Microsoft HPC Pack 仮想マシン イメージ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)を利用し、Azure ポータルを使用して HPC クラスターのヘッド ノードを作成します。この HPC Pack VM イメージは、HPC Pack 2012 R2 更新プログラム 3 を事前インストールした Windows Server 2012 R2 Datacenter に基づいています。このヘッド ノードを Azure の HPC Pack の概念実証デプロイとして利用し、クラスターにコンピューティング ノードを追加して HPC ワークロードを実行できます。



>[AZURE.TIP]ヘッド ノードとコンピューティング ノードが含まれる Azure での完全な HPC Pack クラスターのデプロイでは、[HPC Pack IaaS デプロイメント スクリプト](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)や、Resource Manager テンプレート (例: [Windows ワークロードの HPC Pack クラスター](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) テンプレート) などの自動化手法を利用することをお勧めします。その他のテンプレートについては、[Azure の HPC Pack クラスター オプション](virtual-machines-windows-hpcpack-cluster-options.md)に関する記事をご覧ください。


## 計画に関する考慮事項

次の図に示すように、Azure 仮想ネットワークの Active Directory ドメインにヘッド ノードをデプロイします。

![HPC Pack ヘッド ノート][headnode]

* **Active Directory ドメイン** - VM 上で HPC サービスを開始する前に、HPC Pack ヘッド ノードを Azure の Active Directory ドメインに参加させる必要があります。この記事で示すように、概念実証デプロイとして、HPC サービスを開始する前に、ヘッド ノードに作成した VM をドメイン コントローラーとして昇格できます。もう 1 つの選択肢としては、別個のドメイン コントローラー/フォレストを Azure にデプロイし、それにヘッド ノード VM を参加させます。

* **Azure Virtual Network** - この記事で示すように、Azure ポータルでリソース マネージャー デプロイ モデルを使用して HPC Pack ヘッド ノードをデプロイする場合は、Azure 仮想ネットワークを指定するか作成します。この仮想ネットワークは、後から HPC クラスターにクラスター コンピューティング ノード VM を追加するとき、または既存の Active Directory ドメインにヘッド ノードを結合する場合に使用します。

    
## ヘッド ノードの作成手順

ここでは、Azure ポータルでリソース マネージャー デプロイ モデルを使用して、HPC Pack ヘッド ノード用の Azure VM を作成する手順の概要を示します。


1. Azure で別のドメイン コントローラー VM を持つ新しい Active Directory フォレストを作成する場合は、[Resource Manager テンプレート](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/)を使用する方法があります。HPC Pack の単純な概念実証デプロイの場合は、この手順を省略してもかまいません。代わりに、この記事で後述するように、ヘッド ノード VM 自体をドメイン コントローラーとして構成します。
    
2. [Azure ポータル](https://portal.azure.com)で HPC Pack ヘッド ノードを作成するには、Azure Marketplace から HPC Pack 2012 R2 イメージを選択します。これを行うには、**[新規]** をクリックし、Marketplace で **[HPC Pack]** を検索します。**HPC Pack 2012 R2 on Windows Server 2012 R2** のイメージを選択します。

3. **HPC Pack 2012 R2 on Windows Server 2012 R2** のページで **Resource Manager** デプロイメント モデルを選択し、**[作成]** をクリックします。

    ![HPC Pack イメージ][marketplace]

4. ポータルを使用して設定を構成し、VM を作成します。Azure を初めて利用する場合は、[Azure ポータルでの Windows 仮想マシンの作成](virtual-machines-windows-hero-tutorial.md)に関するチュートリアルをご覧ください。概念実証デプロイの場合は、既定値または推奨設定を選択することをお勧めします。

    **仮想ネットワークに関する考慮事項**

   * ヘッド ノードを Azure の既存の Active Directory ドメインに参加させる場合は、ヘッド ノード VM を作成するときに、必ずそのドメイン内の仮想ネットワークを指定します。
   
   * 新しい仮想ネットワークを作成する場合は、**[設定]** でプライベート ネットワークのアドレス範囲 (例: 10.0.0.0/16) とサブネット アドレス範囲 (例: 10.0.0.0/24) を指定します。
    
4. VM を作成して実行したら、リモート デスクトップで[その VM に接続](virtual-machines-windows-connect-logon.md)します。 

5. VM を既存のドメイン フォレストに参加させるか、VM 自体に新しいドメイン フォレストを作成します。

    * 既存のドメイン フォレストを持つ Azure 仮想ネットワークで VM を作成した場合は、標準のサーバー マネージャーまたは Windows PowerShell ツールを使用して、その VM をドメイン フォレストに参加させます。それから再起動します。

    * 既存のドメイン フォレストのない新しい仮想ネットワークで VM を作成した場合は、VM をドメイン コントローラーとして昇格させます。これを行うには、標準のサーバー マネージャーまたは Windows PowerShell ツールを使用して、Active Directory ドメイン サービスのロールをインストールして構成します。詳細については、「[新しい Windows Server 2012 Active Directory フォレストのインストール](https://technet.microsoft.com/library/jj574166.aspx)」を参照してください。

5. VM が実行され、Active Directory フォレストに参加したら、ヘッド ノードで HPC Pack サービスを開始します。これを行うには、次の手順を実行します。

    a.ローカル管理者グループに属するドメイン アカウントを利用し、VM に接続します。たとえば、ヘッド ノード VM を作成したときに設定した管理者アカウントを使用します。

    b.既定のヘッド ノード構成の場合、Windows PowerShell を管理者として起動し、次を入力します。

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    HPC Pack サービスが開始するまでに数分かかることがあります。

    追加のヘッド ノード構成オプションについては、「`get-help HPCHNPrepare.ps1`」を入力します。


## 次のステップ

* これで、HPC Pack クラスターのヘッド ノードを操作できます。たとえば、HPC クラスター マネージャーを起動し、[デプロイ作業一覧](https://technet.microsoft.com/library/jj884141.aspx)の作業を完了します。
* クラウド サービスに [Azure バースト ノード](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)を追加して、オンデマンドのクラスター コンピューティング能力を向上させます。 

* クラスターでテスト ワークロードを実行してみます。たとえば、HPC Pack [ファースト ステップ ガイド](https://technet.microsoft.com/library/jj884144)を参照してください。

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0525_2016-->