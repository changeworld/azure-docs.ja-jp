---
title: ラボを作成する
description: この記事では、Azure portal と Azure DevTest Labs を使用してラボを作成する手順について説明します。
ms.topic: how-to
ms.date: 10/27/2021
ms.openlocfilehash: ffec989e93bcd394c175a2b4a19c4cb72978beb0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464493"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボの作成

Azure DevTest Labs には Azure 仮想マシン (VM) やネットワークなどのリソースのグループが含まれます。 このインフラストラクチャを使用すると、制限とクォータを指定することで、これらのリソースをより適切に管理できます。 この記事では、Azure Portal を使用してラボを作成する手順を説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプション。 Azure 購入オプションの詳細については、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。 ラボを作成するには、サブスクリプションの所有者である必要があります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

次のリンクを選択すると、Azure portal ページに移動し、Azure DevTest Labs での新しいラボの作成を開始することができます。

[Get started with Azure DevTest Labs in minutes](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="create-a-devtest-labs-resource"></a>DevTest Labs リソースを作成する

**[Create Devtest Lab]\(DevTest Labs の作成\)** ページには 5 つのタブがあります。 1 つ目のタブは **[基本設定]** です。

> [!TIP]
> 各ページの下部に、**自動化用のテンプレートをダウンロード** できるリンクが表示されます。

### <a name="basic-settings-tab"></a>[基本設定] タブ

次の情報を指定します。

|プロパティ | 説明 |
|---|---|
|サブスクリプション| ドロップダウン リストから、ラボに使用する Azure サブスクリプションを選択します。|
|リソース&nbsp;グループ| ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
|ラボ名| ラボのサブスクリプション内に一意の名前を入力します。|
|場所| ドロップダウン リストから、ラボに使用する場所を選択します。|
|パブリックな環境| パブリック環境リポジトリには、ラボ ユーザーがラボ内で PaaS リソースを作成できるようにする選別された Azure Resource Manager テンプレートの一覧が含まれています。 詳細については、[パブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)に関するページを参照してください。|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-basic-settings.png" alt-text="[DevTest Lab の作成] の [基本設定] タブのスクリーンショット。":::


### <a name="auto-shutdown-tab"></a>[自動シャットダウン] タブ

自動シャットダウンを使用すると、毎日スケジュールされた時刻にラボ内のすべてのマシンを自動的にシャットダウンできます。 自動シャットダウン機能は、主にコストを節約する機能です。 ラボの作成後に自動シャットダウンの設定を変更するには、「[Azure DevTest Labs でラボのすべてのポリシーを管理](./devtest-lab-set-lab-policy.md#set-auto-shutdown)」を参照してください。

次の情報を指定します。

|プロパティ | 説明 |
|---|---|
|Enabled| このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。|
|スケジュールされたシャットダウン| 現在のラボのすべての VM をシャットダウンする時刻を入力します。|
|タイム ゾーン| ドロップダウン リストからタイム ゾーンを選択します。|
|自動シャットダウンの前に通知を送信しますか? | 指定した自動シャットダウン時刻の 30 分前に通知を送信することについて、 **[はい]** または **[いいえ]** を選択します。 **[はい]** を選択した場合は、通知が投稿または送信される Webhook URL のエンドポイントまたは電子メール アドレスを入力します。 ユーザーは通知を受信し、シャットダウンを遅らせるオプションが表示されます。|
|Webhook URL| 自動シャットダウンが発生する前に、指定した Webhook エンドポイントに通知が投稿されます。|
|電子メール アドレス| アラートの通知メールを受信するメール アドレスのセットをセミコロンで区切って入力してください。|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-auto-shutdown.png" alt-text="自動シャットダウン スケジュールの詳細のスクリーンショット。":::

### <a name="networking-tab"></a>[ネットワーク] タブ

既定のネットワーク (後から変更または構成可能) が自動的に作成されるほか、既存の仮想ネットワークを選択することもできます。

次の情報を指定します。

|プロパティ | 説明 |
|---|---|
|仮想ネットワーク| 既定のままにするか、ドロップダウン リストから既存の項目を選択します。 Azure では、論理的には仮想ネットワークは互いに分離されています。 同じ仮想ネットワーク内の仮想マシンは、既定で相互にアクセスできます。|
|Subnet| 既定のままにするか、ドロップダウン リストから既存の項目を選択します。 サブネットは、仮想ネットワーク内の IP アドレスの範囲です。これを使用すると、仮想マシンを相互に切り離したり、インターネットから切り離したりできます。|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-networking.png" alt-text="ネットワークの詳細のスクリーンショット。":::

### <a name="tags-tab"></a>[タグ] タブ

タグを適用することによって、ラボのリソースをカテゴリ別に管理して整理することができます。 詳細については、[ラボへのタグの追加](devtest-lab-add-tag.md)に関する記事を参照してください。

次の情報を指定します。

|プロパティ | 説明 |
|---|---|
|Name| タグ名は大文字と小文字が区別されず、512 文字に制限されます。|
|値| タグ値は大文字と小文字が区別され、256 文字に制限されます。|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-tags.png" alt-text="タグの詳細のスクリーンショット。":::

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認および作成]** タブですべての構成を確認します。 すべての設定が有効である場合、上部に "**成功**" と表示されます。 設定を確認し、 **[作成]** を選択します。 ラボ作成プロセスの状態は、ポータル ページの右上にある **[通知]** 領域を見て監視できます。 

:::image type="content" source="./media/devtest-lab-create-lab/portal-review-and-create.png" alt-text="確認と作成の詳細画面のスクリーンショット。":::

## <a name="post-creation"></a>投稿の作成

1. 作成プロセスが完了したら、デプロイの通知から **[リソースに移動]** を選択します。

    :::image type="content" source="./media/devtest-lab-create-lab/creation-notification.png" alt-text="DevTest Labs のデプロイの通知のスクリーンショット。":::

1. ラボの **[概要]** ページは次の図のようになります。

    :::image type="content" source="./media/devtest-lab-create-lab/lab-home-page.png" alt-text="DevTest Labs の [概要] ページのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

ラボを作成した後は、次の手順を考慮します。

* [VM をラボに追加する](devtest-lab-add-vm.md)
* [ラボへのアクセスをセキュリティで保護する](devtest-lab-add-devtest-user.md)
* [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)

