---
title: Avere vFXT for Azure をデプロイする
description: Azure で Avere vFXT クラスターをデプロイする手順
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296377"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT クラスターのデプロイ

この手順では、Azure Marketplace から利用できるデプロイ ウィザードの使い方について説明します。 ウィザードを使うと、Azure Resource Manager テンプレートを使用してクラスターが自動的にデプロイされます。 フォームにパラメーターを入力して **[作成]** をクリックした後、Azure によって自動的に次の手順が実行されます。 

* クラスター コントローラーを作成します。これは、クラスターをデプロイして管理するために必要なソフトウェアを含んだ基本的な VM です。
* リソース グループと仮想ネットワーク インフラストラクチャを設定します。必要に応じて新しい要素の作成を伴います。
* クラスター ノード VM を作成し、それらを Avere クラスターとして構成します。
* 要求された場合は、新しい Azure BLOB コンテナーを作成し、それをクラスター コア ファイラーとして構成します。

このドキュメントの指示に従うことで、以下の図に示されているように、仮想ネットワーク、サブネット、コントローラー、vFXT クラスターを準備することができます。

![省略可能な BLOB ストレージ、および vFXT ノード/vFXT クラスターというラベルの付いた 3 つのグループ化された VM とクラスター コントローラーというラベルの付いた 1 つの VM を含むサブネットがある vnet を示す図](media/avere-vfxt-deployment.png)

Blob Storage を使用する場合は、クラスターの作成後、お客様の仮想ネットワークに[ストレージ エンドポイントを作成](#create-a-storage-endpoint-if-using-azure-blob)する必要があります。 

作成テンプレートを使用する前に、以下の前提条件への対応が済んでいることを確認します。  

1. [新しいサブスクリプション](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [サブスクリプション所有者のアクセス許可](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT クラスターのクォータ](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [カスタム アクセス ロール](avere-vfxt-prereqs.md#create-access-roles) - クラスター ノードに割り当てるロールベースのアクセス制御ロールを作成する必要があります。 クラスター コントローラー用のカスタム アクセス ロールを作成することもできますが、ほとんどのユーザーは既定の所有者ロールを得ることになります。これにより、コントローラーには、リソース グループの所有者に対応する特権が与えられます。 詳細については、「[Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md#owner)」を参照してください。

クラスターのデプロイ手順と計画の詳細については、「[Avere vFXT システムの計画](avere-vfxt-deploy-plan.md)」と、[デプロイの概要](avere-vfxt-deploy-overview.md)に関するページを参照してください。

## <a name="create-the-avere-vfxt-for-azure"></a>Avere vFXT for Azure の作成

Azure portal にある作成テンプレートにアクセスします。「Avere」を検索し、"Avere vFXT for Azure Deployment" を選択してください。 <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

**[作成]** をクリックして開始します。 

![Azure Marketplace (デプロイ テンプレートの先頭ページを表示したところ)](media/avere-vfxt-deploy-first.png)

テンプレートは、4 つの手順に分かれています。情報収集のための 2 つのページに加え、確認と確定の手順があります。 

* 1 ページ目では、クラスター コントローラー VM の設定が中心となります。 
* 2 ページ目では、クラスターとその関連リソース (サブネット、ストレージなど) を作成するためのパラメーターを収集します。 
* 3 ページ目では、一連の設定の概要を見て構成を確認します。 
* 4 ページ目では、ソフトウェアの利用規約が説明されます。また、クラスターの作成プロセスを開始できます。 

## <a name="page-one-parameters---cluster-controller-information"></a>1 ページ目のパラメーター - クラスター コントローラーの情報

デプロイ テンプレートの 1 ページ目では、クラスター コントローラーに関する情報が収集されます。 

![デプロイ テンプレートの 1 ページ目](media/avere-vfxt-deploy-1.png)

次の情報を入力します。

* **[Cluster controller name]\(クラスター コントローラー名\)** - クラスター コントローラー VM の名前を設定します。

* **[Controller username]\(コントローラーのユーザー名\)** - クラスター コントローラー VM のルート ユーザー名を入力します。 

* **[認証の種類]** - コントローラーへの接続に関して、パスワードまたは SSH 公開キーの認証を選択します。 SSH 公開キー方式が推奨されます。ヘルプが必要な場合は、[SSH キーを作成して使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。

* **[パスワード]** または **[SSH 公開キー]** - お客様が選択した認証の種類に応じて、後続のフィールドで RSA 公開キーまたはパスワードを指定する必要があります。 この資格情報は、先ほど指定したユーザー名と組み合わせて使用されます。

* **[Avere cluster create role ID]\(Avere クラスター作成ロール ID\)** - このフィールドを使用して、クラスター コントローラーのアクセス制御ロールを指定します。 既定値は、組み込みロールである [[所有者]](../role-based-access-control/built-in-roles.md#owner) です。 クラスター コントローラーの所有者の特権は、クラスターのリソース グループに限定されます。 

  ロールに対応するグローバル一意識別子を使用する必要があります。 既定値 (所有者) の場合、GUID は 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 です。 カスタム ロールの GUID を見つけるには、次のコマンドを使用します。 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **[サブスクリプション]** - Avere vFXT のサブスクリプションを選択します。 

* **[リソース グループ]** - Avere vFXT クラスターのリソース グループを選択するか、[新規作成] をクリックして新しいリソース グループ名を入力します。 

* **[場所]** - お客様のクラスターとリソースに使用する Azure の場所を選択します。

完了したら、**[OK]** をクリックします。 

> [!NOTE]
> クラスター コントローラーに公開 IP アドレスを割り当てたい場合は、既存のネットワークを選択するのではなく、クラスター用に新しい仮想ネットワークを作成してください。 この設定は 2 ページ目にあります。

## <a name="page-two-parameters---vfxt-cluster-information"></a>2 ページ目のパラメーター - vFXT クラスターの情報

デプロイ テンプレートの 2 ページ目では、主にクラスターのサイズ、ノードの種類、キャッシュ サイズ、ストレージのパラメーターを設定できます。 

![デプロイ テンプレートの 2 ページ目](media/avere-vfxt-deploy-2.png)

* **[Avere vFXT cluster node count]\(Avere vFXT クラスターのノード数\)** - クラスターで使用するノードの数を選択します。 最小で 3 ノード、最大で 12 ノードです。 

* **[Cluster administration password]\(クラスター管理パスワード\)** - クラスターを管理するためのパスワードを作成します。 このパスワードは、クラスターを監視したり設定を構成したりするためにクラスターのコントロール パネルにサインインする際に、ユーザー名 ```admin``` と共に使用されます。

* **[Avere cluster operations role]\(Avere クラスター操作ロール\)** - クラスター ノードのアクセス制御ロールの名前を指定します。 これは、前提条件の手順で作成されたカスタム ロールです。 

  「[クラスター ノードのアクセス ロールを作成する](avere-vfxt-prereqs.md#create-the-cluster-node-access-role)」で説明されている例では、ファイルに ```avere-operator.json``` という名前を付けて保存しています。対応するロール名は ```avere-operator``` です。

* **[Avere vFXT cluster name]\(Avere vFXT クラスターの名前\)** - クラスターに一意の名前を付けます。 

* **[サイズ]** - クラスター ノードの作成時に使用する VM の種類を指定します。 

* **[Cache size per node]\(ノードごとのキャッシュ サイズ\)** - クラスターのキャッシュは、クラスター ノード全体にわたるため、お客様の Avere vFXT クラスターの合計キャッシュ サイズは、ノードごとのキャッシュ サイズにノード数を掛けた値となります。 

  Standard_D16s_v3 クラスター ノードを使用している場合は 1 ノードあたり 1 TB を、Standard_E32s_v3 ノードを使用している場合は 1 ノードあたり 4 TB を使用する構成が推奨されます。

* **[仮想ネットワーク]** - クラスターの配置場所となる既存の VNET を選択するか、新しい VNET を定義して作成します。 

  > [!NOTE]
  > 新しい VNET を作成する場合は、新しいプライベート ネットワークにアクセスできるよう、クラスター コントローラーにパブリック IP アドレスが割り当てられます。 既存の VNET を選択する場合は、パブリック IP アドレスなしでクラスター コントローラーが構成されます。 
  > 
  > 公開された IP アドレスをクラスター コントローラーに使用すると、vFXT クラスターへのアクセスは容易になりますが、わずかにセキュリティ リスクが生じます。 
  >  * クラスター コントローラーのパブリック IP アドレスは、プライベート サブネット外から Avere vFXT クラスターに接続するジャンプ ホストとして使用できます。
  >  * コントローラーでパブリック IP アドレスを設定しない場合は、クラスターにアクセスするために別のジャンプ ホスト、VPN 接続、または ExpressRoute を使用する必要があります。 たとえば、既に VPN 接続が構成されている仮想ネットワーク内にコントローラーを作成します。
  >  * パブリック IP アドレスを使ってコントローラーを作成する場合、ネットワーク セキュリティ グループでコントローラー VM を保護する必要があります。 既定では、Avere vFXT for Azure のデプロイによって、ネットワーク セキュリティ グループが作成され、パブリック IP アドレスを備えたコントローラーのポート 22 のみにインバウンド アクセスが制限されます。 さらに、IP ソース アドレスの範囲にアクセスをロック ダウン (つまり、クラスター アクセスに使用するつもりのマシンからの接続のみを許可) することで、システムを保護できます。

* **[サブネット]** -お客様の既存の仮想ネットワークにあるサブネットを選択するか、新しいものを作成します。 

* **[Use blob storage]\(Blob Storage を使用する\)** - 新しい Azure BLOB コンテナーを作成して、それを新しい Avere vFXT クラスターのバックエンド ストレージとして構成するかどうかを選択します。 新しいコンテナーを作成する場合は、そのコンテナーに対してストレージ アカウントを指定する必要があります。 新しい BLOB コンテナーを作成しない場合は、クラスターの作成後にストレージをアタッチする必要があります (手順については、「[ストレージの構成](avere-vfxt-add-storage.md)」を参照)。 新しいコンテナーを作成したくない場合は、このフィールドを **[false]** に設定してください。

* **[ストレージ アカウント]** - 新しい Azure BLOB コンテナーを作成する場合、ストレージ アカウントの名前を入力します。 ストレージ アカウントは、ローカル冗長ストレージおよびホット アクセス層で構成された Standard 汎用 V2 アカウントであることが必要です。 ストレージ アカウントの要件については、「[ストレージの構成](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer)」の記事で詳しく説明されています。

## <a name="validation-and-purchase"></a>確認と購入

3 ページ目には、構成の概要が表示されます。ここでパラメーターを確認します。 確認後、問題がなければ、**[OK]** ボタンをクリックして次に進みます。 

![デプロイ テンプレートの 3 ページ目 - 確認](media/avere-vfxt-deploy-3.png)

4 ページ目で **[作成]** ボタンをクリックし、利用規約に同意して Avere vFXT for Azure クラスターを作成します。 

![デプロイ テンプレートの 4 ページ目 - 利用規約、[作成] ボタン](media/avere-vfxt-deploy-4.png)

クラスターのデプロイには 15 分から 20 分かかります。

## <a name="gather-template-output"></a>テンプレートの出力の収集

Avere vFXT テンプレートは、クラスターの作成が完了すると、新しいクラスターについてのいくつかの重要な情報を出力します。 

> [!TIP]
> テンプレートの出力から得られた管理 IP アドレスは必ずコピーしておいてください。 クラスターを管理するためには、このアドレスが必要です。

この情報を見つけるには、次の手順に従います。

1. クラスター コントローラー用のリソース グループに移動します。

1. 左側にある **[デプロイ]** をクリックしてから、**microsoft-avere.vfxt-template** をクリックします。

   ![左側で [デプロイ] が選択され、デプロイ名の下の表に microsoft-avere.vfxt-template が示されているリソース グループ ポータル ページ](media/avere-vfxt-outputs-deployments.png)

1. 左側にある **[出力]** をクリックします。 各フィールドの値をコピーします。 

   ![SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs、MGMT_IP の値がラベルの右側のフィールドに表示されている出力ページ](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>ストレージ エンドポイントを作成する (Azure BLOB を使用する場合)

バックエンド データ ストレージに Azure Blob Storage を使用する場合は、仮想ネットワークでストレージ サービス エンドポイントを作成する必要があります。 この[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)では、インターネット経由でルーティングするのではなく、Azure BLOB トラフィックをローカルで保持します。

1. ポータルで、左側にある **[仮想ネットワーク]** をクリックします。
1. コントローラーの vnet を選択します。 
1. 左側にある **[サービス エンドポイント]** をクリックします。
1. 上部にある **[追加]** をクリックします。
1. サービスは ``Microsoft.Storage`` のままにして、コントローラーのサブネットを選択します。
1. 下部にある **[追加]** をクリックします。

  ![サービス エンドポイントを作成する手順に関する注釈を示す Azure portal のスクリーンショット](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>次のステップ

これでクラスターが稼働状態になり、その管理 IP アドレスがわかったので、[クラスター構成ツールに接続](avere-vfxt-cluster-gui.md)して、サポートを有効にし、必要に応じてストレージを追加し、他のクラスター設定をカスタマイズすることができます。
