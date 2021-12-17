---
title: ラボ作成チュートリアル
description: このチュートリアルでは、Azure portal を使って Azure DevTest Labs のラボを作成します。 ラボの管理者は、ラボを設定し、ラボに VM を作成して、ポリシーを構成します。
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: 4a26501511adf65d2db8509120a1d4a014ef73ba
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578597"
---
# <a name="tutorial-set-up-a-lab-in-devtest-labs-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して DevTest Labs でラボを設定する

このチュートリアルでは、Azure portal を使ってラボを作成します。 ラボの管理者は、組織のラボを設定し、ラボに Azure 仮想マシン (VM) を作成して、ポリシーを構成します。 ラボのユーザー (開発者やテスト担当者など) は、ラボの VM を要求し、VM に接続して使用します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ラボを作成する
> * Azure 仮想マシン (VM) をラボに追加する
> * ユーザーを追加し、**DevTest Labs ユーザー** ロールに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-lab"></a>ラボを作成する

これらの手順は、Azure portal を使用して Azure DevTest Labs でラボを作成する方法を示しています。 

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索テキストボックスに「`DevTest Labs`」と入力し、結果から **[DevTest Labs]** を選択します。

    :::image type="content" source="./media/tutorial-create-custom-lab/portal-search-devtest-labs.png" alt-text="ポータルで DevTest Labs を検索するスクリーンショット。":::

1. **[DevTest Labs]** ページで **[+ 作成]** を選択します。

1. **[Devtest Lab の作成]** ページの **[基本設定]** タブで、次の情報を指定します。

    |プロパティ | 説明 |
    |---|---|
    |サブスクリプション| ドロップダウン リストから、ラボに使用する Azure サブスクリプションを選択します。|
    |リソース&nbsp;グループ| ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
    |ラボ名| ラボの名前を入力します。|
    |場所| ドロップダウン リストから、ラボに使用する場所を選択します。|
    |パブリックな環境| 既定値の **[オン]** をそのまま使用します。 パブリック環境リポジトリには、ラボ ユーザーがラボ内で PaaS リソースを作成できるようにする選別された Azure Resource Manager テンプレートの一覧が含まれています。|

    :::image type="content" source="./media/tutorial-create-custom-lab/create-custom-lab-blade.png" alt-text="[DevTest Lab の作成] の [基本設定] タブのスクリーンショット。":::

1. **[レビュー + 作成]** を選択して構成を検証し、 **[作成]** を選択します。 このチュートリアルの場合、他のタブでは既定値で十分です。

1. 作成プロセスが完了したら、デプロイの通知から **[リソースに移動]** を選択します。

    :::image type="content" source="./media/tutorial-create-custom-lab/creation-notification.png" alt-text="DevTest Labs のデプロイの通知のスクリーンショット。":::

1. ラボの **[概要]** ページは次の図のようになります。

    :::image type="content" source="./media/tutorial-create-custom-lab/lab-home-page.png" alt-text="DevTest Labs の [概要] ページのスクリーンショット。":::

## <a name="add-a-vm-to-the-lab"></a>VM をラボに追加する

1. **[DevTest ラボ]** ページで、ツール バーの **[+ 追加]** を選びます。

    :::image type="content" source="./media/tutorial-create-custom-lab/add-vm-to-lab-button.png" alt-text="DevTest Labs の [概要] ページと [追加] ボタンのスクリーンショット。":::

1. **[ベースの選択]** ページで、VM のマーケットプレイス イメージを選択します。 このガイドでは **[Windows Server 2019 Datacenter]** を選択します。 別のイメージを使用すると、一部のオプションが異なる場合があります。

1. **[基本設定]** タブで次の情報を指定します。

    |プロパティ |説明 |
    |---|---|
    |仮想マシン名&nbsp;&nbsp;| テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しており、その後に一意の 3 桁の数字が続きます。 そのままにするか、任意の一意の名前を入力します。|
    |[ユーザー名]| テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しています。 そのままにするか、任意の名前を入力します。 このユーザーには、仮想マシンの **管理者** 権限が付与されます。|
    |保存されているシークレットを使用する| このチュートリアルでは、チェックボックスをオフのままにします。 最初に Azure Key Vault にシークレットを保存してから、ここで使用できます。 詳細については、「[Azure DevTest Labs でキー コンテナーにシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)」を参照してください。 保存したシークレットを使用する場合は、ボックスをオンにし、 **[シークレット]** ドロップダウン リストからシークレットを選択します。|
    |Password|8 から 123 文字の長さのパスワードを入力します。|
    |既定のパスワードとして保存する| チェックボックスをオンにして、ラボに関連付けられている Azure Key Vault にパスワードを保存します。|
    |仮想マシンのサイズ| 既定値のままにするか、 **[サイズの変更]** を選択して別の物理コンポーネントを選択します。 このチュートリアルでは **[Standard_D4_v3]** を使用します。|
    |OS ディスクの種類|既定値のままにするか、ドロップダウン リストから別のオプションを選択します。|
    |Artifacts| このチュートリアルでは使用しません。|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-basic-settings.png" alt-text="仮想マシンの [基本設定] ページのスクリーンショット。":::

1. **[詳細設定]** タブを選択し、次の情報を指定します。

    |プロパティ |説明 |
    |---|---|
    |仮想ネットワーク| そのままにするか、ドロップダウン リストから別のネットワークを選択します。|
    |サブネット&nbsp;セレクター| そのままにするか、ドロップダウン リストから別のサブネットを選択します。|
    |IP アドレス| このチュートリアルでは、既定値の **[共有]** のままにします。 **[共有]** を選択すると、Windows VM では RDP、Linux VM では SSH が、Azure DevTest Labs によって自動的に有効にされます。 **[パブリック]** を使用した場合、DevTest Labs による変更がなくても、RDP と SSH が有効になります。  |
    |有効期限| 有効期限を設定しないままにしておくか、カレンダー アイコンを選択して有効期限を設定します。|
    |このマシンを要求可能にする| **[いいえ]** のままにしておきます。 ラボ ユーザーが VM を要求可能にするには、 **[はい]** を選択します。 マシンを要求可能として設定すると、作成時にその所有権が割り当てられません。 |
    |インスタンスの数| **[1]** のままにしておきます。 作成する仮想マシン インスタンスの数。|
    |Automation | 省略可能。 **[ARM テンプレートの表示]** を選択すると、新しいページにテンプレートが開きます。 テンプレートをコピーして保存すると、後で同じ仮想マシンを作成できます。 保存した Azure Resource Manager テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイ](../azure-resource-manager/templates/overview.md)できます。|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-advanced-settings.png" alt-text="仮想マシンの [詳細設定] ページ。":::

1. **[基本設定]** タブに戻り、 **[作成]** を選択します。

1. **[DevTest Lab]** ページに戻ります。 **[個人用ラボ]** で、 **[要求可能な仮想マシン]** を選択します。

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-creation-status.png" alt-text="ラボの VM 作成状態のページのスクリーンショット。":::

1. 数分たって仮想マシンが表示されない場合は、 **[最新の情報に更新]** を選択します。 インストールにかかる時間は、選択したハードウェア、基本イメージ、成果物によって異なります。 このチュートリアルで使用した構成のインストールは、約 12 分でした。

## <a name="add-a-user-to-the-devtest-labs-user-role"></a>ユーザーを DevTest Labs ユーザー ロールに追加する

1. 作成したラボを含むリソース グループに移動します。 [ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)または[所有者](../role-based-access-control/built-in-roles.md#owner)であることが必要です。

1. 左側のメニューで **[アクセス制御 (IAM)]** を選択します。

1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。

    ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **[ロール]** タブで、 **[DevTest Labs ユーザー]** ロールを選択します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **[メンバー]** タブで、ロールを割り当てるユーザーを選択します。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースを削除して、Azure でラボと VM を実行するための課金が生じないようにします。 ラボ内の VM にアクセスするために次のチュートリアルを実行する場合は、そのチュートリアルを完了した後にリソースをクリーンアップできます。 それ以外の場合は、次の手順に従います。 

1. 作成したラボのホーム ページに戻ります。

1. 上部のメニューで **[削除]** を選択します。

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-delete.png" alt-text="ラボの [削除] ボタンのスクリーンショット。":::

1. **[それを削除してよろしいですか]** ページで、テキスト ボックスにラボの名前を入力し、 **[削除]** を選択します。

1. 削除中、画面の上部にある **[通知]** を選択すると進行状況を表示できます。 ラボの削除にはしばらく時間がかかります。 ラボが削除されたら、次の手順に進みます。

1. 既存のリソース グループ内にラボを作成した場合は、すべてのラボ リソースが削除されています。 このチュートリアル用に新しいリソース グループを作成した場合は、それが空になっていて削除できます。 ラボがまだ存在している場合にリソース グループが削除されることはありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ラボを作成し、VM を追加し、ユーザーにラボへのアクセス許可を与えました。 ラボ ユーザーとしてラボにアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: ラボにアクセスする](tutorial-use-custom-lab.md)
