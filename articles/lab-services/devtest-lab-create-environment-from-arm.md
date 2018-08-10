---
title: Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートから Azure DevTest Labs で複数 VM の環境と PaaS リソースを作成する方法について説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: a6f6beedfc6c23be70693428388f6d0e585260bc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433172"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) を使用すると、簡単に [1 度に 1 台の VM をラボに追加する](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm)ことができます。 ところが、環境に複数の VM が含まれる場合は、各 VM を個別に作成する必要があります。 多層 Web アプリや SharePoint ファームのようなシナリオでは、シングル ステップで複数の VM を作成できるメカニズムが必要です。 Azure Resource Manager テンプレートを使用することで、Azure のソリューションのインフラストラクチャと構成を定義して、複数の VM を一貫した状態で繰り返しデプロイできるようになりました。 この機能には次のような利点があります。

- Azure Resource Manager テンプレートが、ソース管理リポジトリ (GitHub や Team Services Git) から直接読み込まれます。
- 構成したら、ユーザーは、他の種類の [VM ベース](./devtest-lab-comparing-vm-base-image-types.md)を使用する場合と同様に、Azure Portal から Azure Resource Manager テンプレートを選択して環境を作成できます。
- Azure PaaS のリソースを、IaaS VM だけでなく Azure Resource Manager テンプレートから環境にプロビジョニングできます。
- 環境のコストを、他の種類のベースによって作成された個々の VM だけでなくラボで追跡できます。
- PaaS のリソースが作成され、コスト管理に表示されます。ただし、VM の自動シャットダウンは PaaS リソースに適用されません。
- ユーザーは、単一のラボの VM と同じ VM ポリシー コントロールをさまざまな環境で使用できます。

Resource Manager テンプレートを使用してラボのすべてのリソースを単一の操作でデプロイ、更新、または削除することには多くの利点があります。詳細については、[こちらで](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager)ご確認ください。

> [!NOTE]
> より多くのラボの VM を作成するためのベースとして Resource Manager テンプレートを使用する場合、マルチ VM を作成するか、単一 VM を作成するかでいくつかの違いがあることに注意してください。 その違いについては、[仮想マシンの Azure Resource Manager テンプレートの使用](devtest-lab-use-resource-manager-template.md)に関するページで詳しく説明しています。
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Azure Resource Manager テンプレート リポジトリを構成する

コードとしてのインフラストラクチャとコードとしての構成のベスト プラクティスの 1 つとして、環境テンプレートをソース管理で管理する必要があります。 Azure DevTest Labs は、このベスト プラクティスに従って、GitHub または VSTS の Git リポジトリから直接すべての Azure Resource Manager テンプレートを読み込みます。 その結果、Resource Manager のテンプレートは、テスト環境から運用環境まで、リリース サイクル全体で使用することができます。

DevTest Labs チームが[パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)内に作成したテンプレートをチェックアウトします。 このパブリック リポジトリでは、他のユーザーによって共有されているテンプレートを表示し、必要に応じてそのまま使用したり、カスタマイズしたりすることができます。 テンプレートを作成した後、それを他のユーザーと共有するには、このリポジトリに格納します。 クラウド内に環境をセットアップするために使用できるテンプレートを使用して、独自の Git リポジトリを設定することもできます。 

この場合、リポジトリ内で Azure Resource Manager テンプレートを整理するために従わなければならないルールがいくつかあります。

- マスター テンプレート ファイルの名前を `azuredeploy.json` にする必要があります。 

    ![主要な Azure Resource Manager テンプレート ファイル](./media/devtest-lab-create-environment-from-arm/master-template.png)

- パラメーター ファイルで定義したパラメーター値を使用する場合は、パラメーター ファイルの名前を `azuredeploy.parameters.json` にする必要があります。
- `_artifactsLocation` と `_artifactsLocationSasToken` のパラメーターを使用すると、parametersLink URI の値を構築できるため、DevTest Labs で入れ子にされたテンプレートを自動的に管理できるようになります。 詳細については、「[How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/)」(Azure DevTest Labs を使用してテスト環境での入れ子にされた Resource Manager テンプレートのデプロイを簡単にする方法) を参照してください。
- メタデータを定義して、テンプレートの表示名と説明を指定することができます。 このメタデータは、`metadata.json` はという名前のファイルに格納する必要があります。 次のメタデータ ファイルの例で、表示名と説明を指定する方法を示します。 

    ```json
    {
 
        "itemDisplayName": "<your template name>",
 
        "description": "<description of the template>"
 
    }
    ```

次の手順では、Azure Portal を使用してラボにリポジトリを追加する方法を説明します。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボの **[概要]** ウィンドウで、**[構成とポリシー]** を選択します。

    ![構成とポリシー](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **[Configuration and Policies (構成とポリシー)]** の設定一覧で、**[リポジトリ]** を選択します。 **[リポジトリ]** ウィンドウには、ラボに追加されたリポジトリが一覧表示されます。 `Public Repo` という名前のリポジトリがすべてのラボに自動的に生成され、複数の VM アーティファクトを格納する [DevTest ラボ GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)に接続され、使用されます。

    ![Public Repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **[追加+]** を選択し、Azure Resource Manager テンプレートのリポジトリを追加します。
1. 2 つ目の **[リポジトリ]** ウィンドウが開いたら、必要な情報を次のように入力します。
    - **名前** - ラボで使用するリポジトリ名を入力します。
    - **Git クローン URL** - GitHub または Visual Studio Team Services の GIT HTTPS クローン URL を入力します。  
    - **分岐** - Azure Resource Manager テンプレートの定義にアクセスするための分岐名を入力します。 
    - **個人用アクセス トークン** - 個人用アクセス トークンは、リポジトリに安全にアクセスするために使用されます。 Visual Studio Team Services からトークンを取得するには、**&lt;自分の名前>、[マイ プロファイル]、[セキュリティ]、[Public access token (パブリック アクセス トークン)]** の順に選択します。 GitHub からトークンを入手するには、アバターを選択した後に、**[設定]、[Public access token (パブリック アクセス トークン)]** の順に選択します。 
    - **フォルダー パス** - 2 つの入力フィールドのいずれかを使用して、アーティファクトの定義 (最初の入力フィールド) または Azure Resource Manager テンプレートの定義に、フォワード スラッシュ (/) で始まり、Git クローン URI に対して相対的なフォルダー パスを入力します。   
    
        ![Public Repo](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. すべての必須フィールドを入力し、検証に合格したら、**[保存]** を選択します。

次のセクションでは、Azure Resource Manager テンプレートから環境を作成する手順を説明します。

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Azure Portal を使用して Resource Manager テンプレートから環境を作成する

ラボで Azure Resource Manager テンプレート リポジトリが構成されると、ラボのユーザーは Azure Portal で次の手順を使用して環境を作成できます。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボのウィンドウで、**[追加+]** を選択します。
1. **[ベースの選択]** ウィンドウに、使用できるベース イメージが表示されます。Azure Resource Manager テンプレートがその先頭に表示されます。 目的の Azure Resource Manager テンプレートを選択します。

    ![ベースの選択](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. **[追加]** ウィンドウで、**[環境名]** に値を入力します。 環境名は、ラボでユーザーに表示される内容です。 その他の入力フィールドは、Azure Resource Manager テンプレートで定義します。 既定値がテンプレートで定義されている場合、または `azuredeploy.parameter.json` ファイルが存在する場合は、これらの入力フィールドに既定値が表示されます。 *セキュリティで保護された文字列*型のパラメーターの場合、Azure Key Vault に格納されているシークレットを使用することができます。 キー コンテナーにシークレットを保存し、ラボ リソースの作成時に使用する方法については、[Azure Key Vault にシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)方法に関する記事を参照してください。  

    ![[追加] ウィンドウ](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > 値を指定した場合でも、空の値として表示されるパラメーター値がいくつかあります。 そのため、ユーザーが Azure Resource Manager テンプレートのパラメーターにそれらの値を割り当てた場合、DevTest Labs に値は表示されません。 代わりに、空白の入力フィールドが表示され、ラボ ユーザーは環境を作成するときに、ここに値に入力する必要があります。
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. **[追加]** を選択して環境を作成します。 環境でプロビジョニングが開始され、**[仮想マシン]** の一覧に状態が即座に表示されます。 Azure Resource Manager テンプレートで定義されているすべてのリソースをプロビジョニングするために、新しいリソース グループがラボによって自動的に作成されます。
1. 環境が作成されたら、**[仮想マシン]** の一覧で環境を選択してリソース グループ ウィンドウを開き、環境内にプロビジョニングされたすべてのリソースを参照します。
    
    ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   環境にプロビジョニングされている VM の一覧を単に表示するために環境を展開することもできます。
    
    ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 任意の環境をクリックして、アーティファクトの適用、データ ディスクのアタッチ、自動シャット ダウン時刻の変更など、使用可能なアクションを参照します。

    ![環境のアクション](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Resource Manager テンプレートをデプロイして VM を作成する
Resource Manager テンプレートを保存して必要に応じてカスタマイズしたら、VM の作成を自動化するのに使用できます。 
- 「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)」では、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法を説明しています。 
- 「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)」では、Azure CLI と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明しています。

> [!NOTE]
> ラボ所有者権限を持つユーザーだけが、Azure PowerShell を使用して Resource Manager テンプレートから VM を作成できます。 Resource Manager テンプレートを使用して VM の作成を自動化する場合でユーザー権限しか持っていない場合は、[CLI で **az lab vm create** コマンド](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create) を使用します。

### <a name="general-limitations"></a>一般的な制限事項 

DevTest Labs で Resource Manager テンプレートを使用する場合は、次の制限事項を考慮してください。

- 作成した Resource Manager テンプレートから既存のリソースは参照できません。新しいリソースのみを参照できます。 さらに、通常 DevTest Labs の外部にデプロイする既存の Resource Manager テンプレートがあり、これに既存のリソースへの参照が含まれている場合は、ラボで使用できません。

   唯一の例外は、既存の仮想ネットワークを参照**できる**ことです。 

- Resource Manager テンプレートから作成されたラボ VM からは、数式を作成できません。 

- Resource Manager テンプレートから作成されたラボ VM からは、カスタム イメージを作成できません。 

- Resource Manager テンプレートをデプロイするときに、ほとんどのポリシーは評価されません。

   たとえば、ユーザーは 5 つの VM しか作成できないことを指定するラボ ポリシーがあるとします。 ただし、ユーザーは、数十台の VM を作成する Resource Manager テンプレートをデプロイできます。 次のようなポリシーは評価されません。

   - ユーザーあたりの VM 数
   - ラボ ユーザーあたりの Premium VM 数
   - ラボ ユーザーあたりの Premium ディスク数


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>ラボ ユーザーの環境リソース グループへのアクセス権を構成する

ラボ ユーザーは、Resource Manager テンプレートをデプロイできます。 しかし既定では、ラボ ユーザーは閲覧者のアクセス権を持ちます。つまり、環境リソース グループのリソースを変更できません。 たとえば、リソースを停止または開始できません。

ラボ ユーザーに共同作成者のアクセス権を付与するには、次の手順に従います。 すると、ラボ ユーザーが Resource Manager テンプレートをデプロイするときに、その環境のリソースを編集できるようになります。 


1. ラボの **[概要]** ウィンドウで、**[構成とポリシー]** を選択します。
1. **[ラボの設定]** を選択します。
1. [ラボの設定] ウィンドウで、**[共同作成者]** を選択してラボ ユーザーに書き込み権限を付与します。

    ![ラボ ユーザーのアクセス権の構成](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順
* VM が作成されたら、その VM の管理ウィンドウで **[接続]** を選択して VM に接続できます。
* ラボの **[仮想マシン]** 一覧で環境を選択して、環境内のリソースを表示および管理します。 
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。
