<properties
 pageTitle="HPC Pack クラスター コンピューティング ノードの管理 | Microsoft Azure"
 description="Azure の HPC Pack クラスター コンピューティング ノードを追加、削除、起動、停止するための PowerShell スクリプト ツールについて説明します"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Azure の HPC Pack クラスターのコンピューティング ノードの数と可用性を管理する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


Azure VM で HPC Pack クラスターを作成した場合、クラスターにたくさんのコンピューティング ノード VM を簡単に追加し、開始し (プロビジョニング)、停止 (プロビジョニング解除) できる方法があれば便利です。そのような作業を行うには、ヘッド ノード VM にインストールされている Azure PowerShell スクリプトを実行します (HPC Pack 2012 R2 更新プログラム 1 以降)。これらのスクリプトを利用すれば、HPC Pack クラスター リソースの数と可用性を制御し、コストを管理できます。

>[AZURE.NOTE]スクリプトはヘッド ノードの %CCP\_HOME%bin フォルダーにあります。各スクリプトは管理者として実行する必要があります。

## 前提条件

* **Azure VM の HPC Pack クラスター** - HPC Pack 2012 R2 更新プログラム 1 以降を利用し、従来 (サービス管理) のデプロイ モデルで HPC Pack クラスターを作成します。たとえば、Azure Marketplace の HPC Pack VM イメージや Azure PowerShell スクリプトを利用し、デプロイを自動化できます。詳細と前提条件については、「[HPC Pack IaaS デプロイ スクリプトを使用した HPC クラスターの作成](virtual-machines-hpcpack-cluster-powershell-script.md)」を参照してください。

* **Azure 発行設定ファイルまたは管理証明書** - ヘッド ノードで次のいずれかを行う必要があります。

    * **Azure 発行設定ファイルをインポートします**。これを行うには、ヘッド ノードで次の Azure PowerShell コマンドレットを実行します。

    ```
    Get-AzurePublishSettingsFile 
         
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
    
    * **ヘッド ノードで Azure 管理証明書を構成します**。.cer ファイルがある場合、それを CurrentUser\\My 証明書ストアにインポートし、Azure 環境 (either AzureCloud または AzureChinaCloud) に対して次の Azure PowerShell コマンドレットを実行します。

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## コンピューティング ノード VM の追加

**Add-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを追加します。

### 構文
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### パラメーター

* **ServiceName** - 新しいコンピューティング ノード VM が追加されるクラウド サービスの名前。

* **ImageName** - Azure VM イメージ名。これは Azure クラシック ポータルまたは Azure PowerShell コマンド **Get-AzureVMImage** から取得できます。このイメージは次の要件を満たしている必要があります。

    1. Windows オペレーティング システムをインストールする必要があります。

    2. コンピューティング ノード ロールに HPC Pack をインストールする必要があります。

    3. イメージは、公開 Azure VM イメージではなく、ユーザー カテゴリのプライベート イメージである必要があります。

* **Quantity**- 追加するコンピューティング ノード VM の数。

* **InstanceSize** - コンピューティング ノード VM のサイズ。

* **DomainUserName** - ドメイン ユーザー名。これは新しい VM をドメインに追加するときに使用されます。

* **DomainUserPassword*** ドメイン ユーザーのパスワード

* **NodeNameSeries** (省略可能) - コンピューティング ノードの命名規則パターン。&lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;% の形式にする必要があります。たとえば、｢MyCN%10%｣は MyCN11 から始まる一連のコンピューティング ノード名を意味します。指定されていない場合、HPC クラスターに構成されているノード命名規則が使用されます。

### 例

次の例では、「hpccnimage1」という VM イメージを基盤として、「hpcservice1」というクラウド サービスに大型のコンピューティング ノード VM が 20 追加されます。

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## コンピューティング ノード VM の削除

**Remove-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを削除します。

### 構文

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### パラメーター

 * **Name** - 削除するクラスター ノードの名前。ワイルドカードを利用できます。パラメーター セット名は「Name」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

* **Node** * 削除するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) から取得できます。パラメーター セット名は「Node」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

* **DeleteVHD** (省略可能) - 削除される VM の関連ディスクの削除設定。

* **Force** (省略可能) - 削除前に HPC ノードを強制的にオフラインにする設定。

* **Confirm** (省略可能) - コマンドを実行する前に確認を求めます。

* **WhatIf** - 実際にコマンドを実行せず、コマンドの実行結果を表示する設定。

### 例

次の例では、名前が「*HPCNode-CN-*」で始まるノードを強制的にオフラインにし、それからノードと関連ディスクを削除します。

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## コンピューティング ノード VM の起動

**Start-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを起動します。

### 構文

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### パラメーター

* **Name** - 起動するクラスター ノードの名前。ワイルドカードを利用できます。パラメーター セット名は「Name」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

* **Node** - 起動するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) から取得できます。パラメーター セット名は「Node」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

### 例

次の例では、名前が「*HPCNode-CN-*」で始まるノードが起動します。

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## コンピューティング ノード VM の停止

**Stop-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを停止します。

### 構文

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### パラメーター


* **Name** - 停止するクラスター ノードの名前。ワイルドカードを利用できます。パラメーター セット名は「Name」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

* **Node** - 停止するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの Get-HpcNode から取得できます。パラメーター セット名は「Node」です。「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

* **Force** (省略可能) - 停止前に HPC ノードを強制的にオフラインにする設定。

### 例

次の例では、名前が「*HPCNode-CN-*」で始まるノードを強制的にオフラインにし、それからノードを停止します。

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## 次のステップ

* クラスターのジョブやタスクの現在のワークロードに合わせ、Azure コンピューティング リソースを自動的に拡大縮小する方法については、「[HPC Pack クラスターの Azure コンピューティング リソースを拡大縮小する](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)」を参照してください。

<!---HONumber=AcomDC_1210_2015-->