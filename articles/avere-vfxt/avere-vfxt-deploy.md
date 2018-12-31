---
title: Avere vFXT for Azure をデプロイする
description: Azure で Avere vFXT クラスターをデプロイする手順
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 8e265f2bed480f7b40476e09ab8f442aedcc9dd4
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999450"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT クラスターのデプロイ

Azure で vFXT クラスターを作成する最も簡単な方法は、クラスター コントローラーを使用することです。 クラスター コントローラーは、vFXT クラスターを作成して管理するために必要なスクリプト、テンプレート、ソフトウェア インフラストラクチャを含む VM です。

新しい vFXT クラスターをデプロイするには、次の手順を行います。

1. [クラスター コントローラーを作成します](#create-the-cluster-controller-vm)。
1. Azure Blob Storage を使用する場合は、仮想ネットワークで[ストレージ エンドポイントを作成します](#create-a-storage-endpoint-if-using-azure-blob)。
1. [クラスター コントローラーに接続します](#access-the-controller)。 残りの手順はクラスター コントローラー VM から行います。 
1. クラスター ノードの[アクセス ロールを作成します](#create-the-cluster-node-access-role)。 プロトタイプが提供されます。
1. 作成する vFXT クラスターの種類に合わせて、[クラスターの作成スクリプトをカスタマイズします](#edit-the-deployment-script)。
1. [クラスターの作成スクリプトを実行します](#run-the-script)。

クラスターのデプロイ手順と計画の詳細については、「[Avere vFXT システムの計画](avere-vfxt-deploy-plan.md)」と、[デプロイの概要](avere-vfxt-deploy-overview.md)に関するページを参照してください。 

このドキュメントの指示に従うことで、以下の図に示されているように、仮想ネットワーク、サブネット、コントローラー、vFXT クラスターを準備することができます。

![省略可能な BLOB ストレージ、および vFXT ノード/vFXT クラスターというラベルの付いた 3 つのグループ化された VM とクラスター コントローラーというラベルの付いた 1 つの VM を含むサブネットがある vnet を示す図](media/avere-vfxt-deployment.png)

始める前に、以下の前提条件への対応が済んでいることを確認します。  

1. [新しいサブスクリプション](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [サブスクリプション所有者のアクセス許可](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT クラスターのクォータ](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

必要に応じて、クラスター コントローラーを作成する[前に](avere-vfxt-pre-role.md)クラスター ノード ロールを作成できますが、後で行うほうが簡単です。

## <a name="create-the-cluster-controller-vm"></a>クラスター コントローラー VM を作成する

最初の手順では、vFXT クラスター ノードを作成して構成する仮想マシンを作成します。 

クラスター コントローラーは、Avere vFXT クラスターの作成ソフトウェアとスクリプトがプレインストールされている Linux VM です。 大量の処理能力や記憶域スペースを必要としないため、低コストのオプションを選択できます。 この VM は、vFXT クラスターの有効期間にわたって使用されることがあります。

クラスター コントローラー VM を作成する方法は 2 つあります。 プロセスを簡素化するために[以下の](#create-controller---arm-template) [Azure Resource Manager テンプレート](#create-controller---arm-template)が提供されますが、[Azure Marketplace イメージ](#create-controller---azure-marketplace-image)からコントローラーを作成することもできます。 

コントローラーの作成の一環として、新しいリソース グループを作成することができます。

> [!TIP]
>
> クラスター コントローラーでパブリック IP アドレスを使用するかどうかを決定します。 パブリック IP アドレスを使用することで、vFXT クラスターに簡単にアクセスできますが、わずかながらセキュリティ リスクが生じます。
>
>  * クラスター コントローラーのパブリック IP アドレスは、プライベート サブネット外から Avere vFXT クラスターに接続するジャンプ ホストとして使用できます。
>  * コントローラーでパブリック IP アドレスを設定しない場合は、クラスターにアクセスするために別のジャンプ ホスト、VPN 接続、または ExpressRoute を使用する必要があります。 たとえば、VPN 接続が構成されている仮想ネットワーク内にコントローラーを作成します。
>  * パブリック IP アドレスを使ってコントローラーを作成する場合、ネットワーク セキュリティ グループでコントローラー VM を保護する必要があります。 ポート 22 経由でのみインターネットにアクセスできるようにします。

### <a name="create-controller---resource-manager-template"></a>コントローラーを作成する - Resource Manager テンプレート

ポータルからクラスター コントローラー ノードを作成するには、下の [Azure へのデプロイ] ボタンをクリックします。 このデプロイ テンプレートでは、Avere vFXT クラスターを作成して管理する VM が作成されます。

[![コントローラーを作成するためのボタン](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

次の情報を指定します。

**[Basic]** セクション内:  

* クラスターの**サブスクリプション**
* クラスターの**リソース グループ** 
* **場所** 

**[設定]** セクション内:

* 新しい仮想ネットワークを作成するかどうか

  * 新しい vnet を作成する場合、クラスター コントローラーにパブリック IP アドレスが割り当てられるため、それに到達できます。 受信トラフィックをポート 22 のみに制限するこの vnet に対して、ネットワーク セキュリティ グループも作成されます。
  * ExpressRoute または VPN を使用して、クラスター コントローラーに接続する場合は、この値を `false` に設定し、残りのフィールドでは既存の vnet を指定します。 クラスター コントローラーでは、ネットワーク通信にその vnet を使用します。 

* 仮想ネットワークのリソース グループ、名前、およびサブネット名 - 既存のリソースの名前を入力します (既存の vnet を使用する場合)。新しい vnet を作成する場合は、新しい名前を入力します
* **コントローラー名** - コントローラー VM の名前を設定します
* コントローラーの管理者ユーザー名 - 既定値は `azureuser` です
* SSH キー - 管理者ユーザー名に関連付けるために公開キーを貼り付けます。 ヘルプが必要な場合は、[SSH キーを作成して使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。

**[使用条件]** で: 

* サービス使用条件を読み、条件に同意するチェック ボックスをクリックします。 

  > [!NOTE] 
  > サブスクリプションの所有者でない場合は、所有者に、「[Accept software terms in advance](avere-vfxt-prereqs.md#accept-software-terms-in-advance)」 (事前にソフトウェア条件に同意する) に示されている前提条件の手順に従って、条件に同意してもらいます。 


完了したら、**[購入]** をクリックします。 5、6 分後に、コントローラー ノードが稼働します。

出力ページに移動し、クラスターの作成に必要なコントローラーの情報を収集します。 詳しくは、「[クラスターの作成に必要な情報](#information-needed-to-create-the-cluster)」をご覧ください。

### <a name="create-controller---azure-marketplace-image"></a>コントローラーを作成する - Azure Marketplace イメージ

Azure Marketplace で ``Avere`` という名前を検索して、コントローラー テンプレートを見つけます。 **Avere vFXT for Azure コントローラー**のテンプレートを選択します。

条件に同意し、[プログラムによるデプロイについて] リンクをクリックして、Marketplace イメージのプログラムによるアクセスを有効にします (まだこの作業を行っていない場合)。 リンクは、**[作成]** ボタンの下にあります。

![[作成] ボタンの下にある、プログラムによるアクセスへのリンクのスクリーンショット](media/avere-vfxt-deploy-programmatically.png)

**[有効化]** ボタンをクリックして、設定を保存します。

![プログラムによるアクセスを有効にするためのマウス クリックを示すスクリーンショット](media/avere-vfxt-enable-program.png)

**Avere vFXT for Azure コントローラー**のテンプレートのメイン ページに戻り、**[作成]** をクリックします。 

最初のパネルで、以下の基本的なオプションを入力するか確認します。

* **サブスクリプション**
* **リソース グループ** (新しいグループを作成する場合は、新しい名前を入力します)。
* **仮想マシン名** - コントローラーの名前
* **[リージョン]**
* **可用性オプション** - 冗長性は必要ありません
* **イメージ** - Avere vFXT コントローラー ノードのイメージ
* **サイズ** - 既定値のままにするか、別の低コストの種類を選択します
* **管理者アカウント** - クラスター コントローラーにサインインする方法を設定します。 
  * [ユーザー名/パスワード] または [SSH 公開キー] (推奨) を選択します。
  
    > [!TIP] 
    > SSH キーのほうが安全です。 ヘルプが必要な場合は、[SSH キーを作成して使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。 
  * ユーザー名を指定します 
  * SSH キーを貼り付けるか、パスワードを入力して確認します
* **受信ポートの規則** - パブリック IP アドレスを使用する場合は、ポート 22 (SSH) を開きます

**[次へ]** をクリックして、ディスク オプションを設定します。

* **OS ディスクの種類** - HDD の既定値で十分です
* **非管理対象ディスクの使用** - 必要ありません
* **データ ディスク** - 使用しないでください

**[次へ]** をクリックして、ネットワーク オプションを設定します。

* **仮想ネットワーク** - コントローラーの vnet を選択するか、名前を入力して新しい vnet を作成します。 コントローラーでパブリック IP アドレスを使用しない場合は、ExpressRoute または別のアクセス方法が既に設定されている vnet 内で検索することを検討してください。
* **サブネット** - vnet 内のサブネットを選択します (省略可能)。 新しい vnet を作成する場合は、同時に新しいサブネットを作成できます。
* **パブリック IP** - パブリック IP アドレスを使用する場合は、ここで指定できます。 
* **ネットワーク セキュリティ グループ** - 既定の設定 (**Basic**) のままにしておきます 
* **パブリック受信ポート** - パブリック IP アドレスを使用する場合は、このコントロールを使って、SSH トラフィックからのアクセスを許可します。 
* **高速ネットワーク**は、この VM では使用できません。

**[次へ]** をクリックして、管理オプションを設定します。

* **ブート診断** - **[オフ]** に変更します
* **OS のゲスト診断** - 無効のままにしておきます
* **診断ストレージ アカウント** - 必要に応じて、選択するか、新しいアカウントを指定して診断情報を保持します。
* **マネージド サービス ID** - このオプションを **[オン]** に変更します。これにより、クラスター コントローラーの Azure AD サービス プリンシパルが作成されます。
* **自動シャットダウン** - オフのままにしておきます 

この時点で、インスタンス タグを使用しない場合は **[確認および作成]** をクリックできます。 それ以外の場合は、**[次へ]** を 2 回クリックして **[ゲストの構成]** ページをスキップし、タグ ページに移動します。 完了したら、**[確認および作成]** をクリックします。 

選択内容が確認されたら、**[作成]** ボタンをクリックします。  

作成には 5、6 分かかります。

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>ストレージ エンドポイントを作成する (Azure BLOB を使用する場合)

バックエンド データ ストレージに Azure Blob Storage を使用する場合は、仮想ネットワークでストレージ サービス エンドポイントを作成する必要があります。 この[サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)では、インターネット経由でルーティングするのではなく、Azure BLOB トラフィックをローカルで保持します。

1. ポータルで、左側にある **[仮想ネットワーク]** をクリックします。
1. コントローラーの vnet を選択します。 
1. 左側にある **[サービス エンドポイント]** をクリックします。
1. 上部にある **[追加]** をクリックします。
1. サービスは ``Microsoft.Storage`` のままにして、コントローラーのサブネットを選択します。
1. 下部にある **[追加]** をクリックします。

  ![サービス エンドポイントを作成する手順に関する注釈を示す Azure portal のスクリーンショット](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>クラスターの作成に必要な情報

クラスター コントローラーを作成した後、次の手順に必要な情報があることを確認します。 

コントローラーに接続するために必要な情報: 

* コントローラーのユーザー名と SSH キー (またはパスワード)
* コントローラー IP アドレス、またはコントローラー VM に接続するための他の方法

クラスターに必要な情報: 

* リソース グループ名
* Azure の場所 
* 仮想ネットワーク名
* サブネット名
* クラスター ノードのロール名 - この名前はロールを作成するときに設定されます ([後述](#create-the-cluster-node-access-role))
* BLOB コンテナーを作成する場合は、ストレージ アカウント名

Resource Manager テンプレートを使用してコントローラー ノードを作成した場合は、[テンプレートの出力から情報を取得](#find-template-output)できます。 

Azure Marketplace イメージを使用してコントローラーを作成した場合、これらの項目のほとんどは直接提供してあります。 

コントローラー VM の情報ページを参照して、欠落している情報を見つけます。 たとえば、**[すべてのリソース]** をクリックして、コントローラー名を検索し、そのコントローラー名をクリックして詳細を表示します。

### <a name="find-template-output"></a>テンプレートの出力を見つける

Resource Manager テンプレートの出力からこの情報を見つけるには、次の手順に従います。

1. クラスター コントローラー用のリソース グループに移動します。

1. 左側にある **[デプロイ]** をクリックしてから、**[Microsoft.Template]** をクリックします。

   ![左側に [デプロイ] が選択され、デプロイ名の下の表に Microsoft.Template が示されているリソース グループ ポータル ページ](media/avere-vfxt-deployment-template.png)

1. 左側にある **[出力]** をクリックします。 各フィールドの値をコピーします。 

   ![ラベルの右側のフィールドに SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK、SUBNET、および SUBNET_ID が表示されている出力ページ](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>コントローラーにアクセスする

残りのデプロイ手順を行うには、クラスター コントローラーに接続する必要があります。

1. クラスター コントローラーに接続する方法は、設定内容によって異なります。

   * コントローラーにパブリック IP アドレスがある場合は、設定した管理者ユーザー名として、コントローラーの IP に SSH 接続します (例: ``ssh azureuser@40.117.136.91``)。
   * コントローラーにパブリック IP がない場合は、vnet への VPN または [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 接続を使用します。

1. コントローラーにログインした後、`az login` を実行して認証します。 シェルに指定されている認証コードをコピーし、Web ブラウザーを使用して [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) を読み込み、Microsoft システムで認証します。 シェルに戻って確認します。

   ![ブラウザー リンクと認証コードが表示されている 'AZ login' コマンドのコマンド ライン出力](media/avere-vfxt-azlogin.png)

1. サブスクリプション ID を使用して以下のコマンドを実行し、サブスクリプションを追加します。```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>クラスター ノードのアクセス ロールを作成する

> [!NOTE] 
> * サブスクリプション所有者ではなく、ロールがまだ作成されていない場合は、サブスクリプション所有者にこれらの手順を行うよう依頼するか、「[コントローラーなしで Avere vFXT クラスター ランタイム アクセス ロールを作成する](avere-vfxt-pre-role.md)」の手順を使用します。
> 
> * Microsoft の内部ユーザーは、作成する代わりに "Avere Cluster Runtime Operator" という名前の既存のロールを使用する必要があります。 

[ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) により、vFXT クラスター ノードに対して、必要なタスクを実行する承認が与えられます。  

通常の vFXT クラスター操作の一環として、個々の vFXT ノードでは、Azure リソース プロパティの読み取り、ストレージの管理、他のノードのネットワーク インターフェイス設定の制御などを行う必要があります。 

1. コントローラーで、``/avere-cluster.json`` ファイルをエディターで開きます。

   ![list コマンドと "vi /avere-cluster.json" を示すコンソール](media/avere-vfxt-open-role.png)

1. ファイルを編集し、サブスクリプション ID を含めて、その上の行を削除します。 ファイルを ``avere-cluster.json``という名前で保存します。

   ![サブスクリプション ID と、削除対象として "この行を削除する" ように指示する行が選択されているコンソール テキスト エディター](media/avere-vfxt-edit-role.png)

1. 以下のコマンドを使用して、ロールを作成します。  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

ロールの名前は、次の手順でクラスターの作成スクリプトに渡します。 

## <a name="create-nodes-and-configure-the-cluster"></a>ノードを作成してクラスターを構成する

Avere vFXT クラスターを作成するには、コントローラーに含まれるサンプル スクリプトのいずれかを編集し、そこで実行します。 サンプル スクリプトは、クラスター コントローラーのルート ディレクトリ (`/`) にあります。

* Avere vFXT のバックエンド ストレージ システムとして使用する BLOB コンテナーを作成する場合は、``create-cloudbacked-cluster`` スクリプトを使用します。

* 後でストレージを追加する場合は、``create-minimal-cluster`` スクリプトを使用します。

> [!TIP]
> ノードを追加して vFXT クラスターを破棄するためのプロトタイプ スクリプトも、クラスター コントローラー VM の `/` ディレクトリに含まれています。

### <a name="edit-the-deployment-script"></a>デプロイ スクリプトを編集する

エディターでサンプル スクリプトを開きます。 元のサンプルが上書きされないように、カスタマイズしたスクリプトを別の名前で保存することができます。

以下のスクリプト変数の値を入力します。

* リソース グループ名

  * 別のリソース グループ内にあるネットワークまたはストレージ コンポーネントを使用する場合は、変数のコメントを解除し、それらの名前も指定します。 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* 場所の名前
* 仮想ネットワーク名
* サブネット名
* Azure AD ランタイムのロール名 - 「[クラスター ノードのアクセス ロールを作成する](#create-the-cluster-node-access-role)」の例に従った場合は、``avere-cluster`` を使用します。 
* ストレージ アカウント名 (新しい BLOB コンテナーを作成する場合)
* クラスター名 - 同じリソース グループで同じ名前の 2 つの vFXT クラスターを使用することはできません。 ベスト プラクティスとして、各クラスターに一意の名前を付けます。
* 管理パスワード - クラスターを監視および管理するためのセキュリティで保護されたパスワードを選択します。 このパスワードは、``admin`` ユーザーに割り当てられます。 
* ノード インスタンスの種類 - 詳細については、「[vFXT ノードのサイズ](avere-vfxt-deploy-plan.md#vfxt-node-sizes)」 を参照してください
* ノードのキャッシュ サイズ - 詳細については、「[vFXT ノードのサイズ](avere-vfxt-deploy-plan.md#vfxt-node-sizes)」を参照してください

ファイルを保存して終了します。

### <a name="run-the-script"></a>スクリプトを実行する

作成したファイル名を入力して、スクリプトを実行します  (例: `./create-cloudbacked-cluster-west1`)。  

> [!TIP]
> 接続が失われた場合は、`screen` や `tmux` などの[ターミナル マルチプレクサー](http://linuxcommand.org/lc3_adv_termmux.php)内でこのコマンドを実行することを検討してください。  

出力は `~/vfxt.log` にも記録されます。

スクリプトが完了したら、クラスターの管理に必要な、管理 IP アドレスをコピーします。

![末尾付近に管理 IP アドレスが表示されているスクリプトのコマンド ライン出力](media/avere-vfxt-mgmt-ip.png)

> [!IMPORTANT] 
> 新しい BLOB コンテナーを作成した場合は、クラスターの外部で保存されていない既定のキーを使用して暗号化されている可能性があります。 データをコンテナーに格納する前に、キー回復ファイルをダウンロードするか独自の暗号化キーを作成して、その回復ファイルを永続的な場所に保存する必要があります。 
> 
> 回復ファイルをダウンロードせずに既定のキーを使用すると、vFXT クラスターが破棄されたり失われたりした場合に、BLOB コア ファイラー内の暗号化されたデータへのアクセスが失われる可能性があります。
>
> スクリプトによって、下のスクリーンショットで囲まれている部分のような `WARNING` メッセージが表示される場合は、[ストレージの構成](avere-vfxt-add-storage.md)に関する記事の手順に従って、キー ファイルをダウンロードするか BLOB コンテナー用の新しいキーを作成します。 クラスター構成ツールである Avere コントロール パネルを使用します。

![新しい暗号化キーの作成に関する警告メッセージが表示されるスクリプトのコマンド ライン出力](media/avere-vfxt-key-warning.png)

## <a name="next-step"></a>次のステップ

これでクラスターが実行され、その管理 IP アドレスがわかったので、[クラスター構成ツールに接続](avere-vfxt-cluster-gui.md)してサポートを有効にする、必要に応じてストレージを追加する、または新しい Blob Storage 上の既定の暗号化キーを指定することができます。
