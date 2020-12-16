---
title: インスタンスと認証を設定する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins サービスのインスタンスを設定する方法について説明します
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 90a425fe64424c946a02f3c113889b62b58fbeb4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032322"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins インスタンスと認証を設定する (ポータル)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する** 手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が整います。

この記事のこのバージョンでは、Azure portal を使用して、これらの手順を 1 つずつ手動で実行します。 Azure portal は、コマンドライン ツールに代えて使用できる、Web ベースの統合コンソールです。
* CLI を使用してこれらの手順を手動で実行するには、こちら記事の CLI バージョンを確認してください: [*方法: インスタンスと認証を設定する (CLI)*](how-to-set-up-instance-cli.md)。
* デプロイ スクリプトのサンプルを使用して自動での設定を実行するには、こちらの記事のスクリプト化されたバージョンを確認してください: [*方法: インスタンスと認証を設定する (スクリプト化)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

このセクションでは、[Azure portal](https://ms.portal.azure.com/) を使用して、**Azure Digital Twins の新しいインスタンスを作成** します。 ポータルに移動し、自分の資格情報でログインします。

Azure portal に入ったら、Azure サービスのホーム ページ メニューで _[リソースの作成]_ を選択して開始します。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Azure portal のホーム ページから [リソースの作成] を選択する":::

検索ボックスで *Azure Digital Twins* を検索し、結果から **[Azure Digital Twins]** サービスを選択します。 _[作成]_ ボタンを選択して、サービスの新しいインスタンスを作成します。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Azure Digital Twins サービスのページから [作成] を選択する":::

以下の *[リソースの作成]* ページで、次に示した値を入力します。
* **サブスクリプション**:使用している Azure サブスクリプション
  - **[リソース グループ]** :インスタンスのデプロイ先にするリソース グループ。 検討できる既存のリソース グループがまだない場合は、 *[新規作成]* リンクを選択して新しいリソース グループの名前を入力することで、ここで作成することができます
* **[場所]** :デプロイする Azure Digital Twins 対応のリージョン。 リージョンのサポートに関する詳細については、"[*リージョン別の利用可能な Azure 製品 (Azure Digital Twins)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)" に関するページを参照してください。
* **[リソース名]** : Azure Digital Twins インスタンスの名前。 新しいインスタンスの名前は、サブスクリプションのリージョン内で一意である必要があります (つまり、サブスクリプションのリージョン内に、選択した名前を既に使用している別の Azure Digital Twins インスタンスが存在する場合は、異なる名前を選択するよう求められます)。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="説明に示された値を入力して Azure Digital Twins リソースを作成する":::

終わったら、 _[確認と作成]_ を選択します。 これにより、[概要] ページへ遷移します。ここでは、入力したインスタンスの詳細を確認して、 _[作成]_ を押します。 

### <a name="verify-success-and-collect-important-values"></a>成功を確認して重要な値を収集する

*[作成]* を押した後は、ポータル アイコン バーの Azure 通知にインスタンスのデプロイの状態が表示されます。 この通知はデプロイが成功した時点で示され、作成したインスタンスを表示するための _[リソースに移動]_ ボタンを選択できるようになります。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="デプロイの成功の表示と [リソースに移動] ボタンの強調表示がある Azure 通知のビュー":::

また、デプロイが失敗した場合は、その理由が通知に示されます。 エラー メッセージの通知内容を確認して、インスタンスの作成を再試行します。

>[!TIP]
>インスタンスが作成されたら、Azure portal の検索バーでインスタンスの名前を検索して、いつでもそのページに戻ることができます。

インスタンスの *[概要]* ページで、 *[名前]* 、 *[リソース グループ]* 、および *[ホスト名]* をメモします。 これらはすべて、Azure Digital Twins インスタンスの操作を続行する際に必要になる可能性がある重要な値です。 他のユーザーがそのインスタンスに対してプログラミングする場合は、これらの値を彼らと共有する必要があります。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="インスタンスの [概要] ページの重要な値の強調表示":::

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

最初に、Azure portal 上の Azure Digital Twins インスタンスのページを開きます。 インスタンスのメニューから *[アクセス制御 (IAM)]* を選択します。 *[ロールの割り当てを追加する]* にある *[追加]* ボタンを選択します。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="[アクセス制御 (IAM)] ページからロールの割り当てを追加することを選ぶ":::

次の *[ロールの割り当てを追加する]* ページで、値を入力します (Azure サブスクリプションにおいて [十分なアクセス許可](#prerequisites-permission-requirements)を持つユーザーが完了する必要があります)。
* **ロール**: ドロップダウン メニューから *[Azure Digital Twins Data Owner]\(Azure Digital Twins データ所有者\)* を選択します。
* **アクセスの割り当て先**:ドロップダウン メニューから *[Azure AD user, group or service principal]\(Azure AD ユーザー、グループ、またはサービス プリンシパル\)* を選択します
* **Select**:割り当てるユーザーの名前または電子メール アドレスを検索します。 結果を選択すると、 *[選択したメンバー]* セクションにそのユーザーが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="[ロールの割り当てを追加する] ダイアログに表示されたフィールドへの入力":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

詳細情報の入力が完了したら、 *[保存]* ボタンをクリックします。

### <a name="verify-success"></a>成功を確認する

*[アクセス制御 (IAM)] > [ロールの割り当て]* 下で設定したロールの割り当てを確認できます。 一覧に *"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)* のロールを持つユーザーが表示されます。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのビュー":::

これで、Azure Digital Twins インスタンスの準備が完了し、これを管理するためのアクセス許可が割り当てられました。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins CLI コマンドを使用して、インスタンスでの個別の REST API 呼び出しをテストします。 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*方法: Azure Digital Twins CLI を使用する*](how-to-use-cli.md)

または、認証コードを使用してクライアント アプリケーションをインスタンスに接続する方法を確認します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)