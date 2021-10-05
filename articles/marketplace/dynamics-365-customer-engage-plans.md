---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & Power Apps プランを作成します。
description: サードパーティ製のアプリ管理のプランを有効にすることを選択した場合は、Dynamics 365 for Customer Engagement & Power Apps オファーのプランを構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 4bcd4a2bfc3b9daab502b55fccad63b0fb01be46
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082863"
---
# <a name="create-dynamics-365-for-customer-engagement--power-apps-plans"></a>Dynamics 365 for Customer Engagement & Power Apps プランの作成

オファーのアプリ ライセンス管理を有効にした場合、次のスクリーンショットに示されているように **[プランの概要]** タブが表示されます。 そうでない場合は、「[Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定する](dynamics-365-customer-engage-technical-configuration.md)」に進んでください。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

[ ![サードパーティのアプリのライセンスに対して有効になっている Dynamics 365 for Customer Engagement & Power Apps オファーの [プランの概要] タブのスクリーンショット。](./media/third-party-license/plan-tab-d365-workspaces.png) ](./media/third-party-license/plan-tab-d365-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

:::image type="content" source="./media/third-party-license/plan-tab.png" alt-text="サードパーティのアプリのライセンスに対して有効になっている Dynamics 365 for Customer Engagement & Power Apps オファーの [プランの概要] タブのスクリーンショット。":::

---

オファーでアプリ ライセンス管理が有効になっている場合は、少なくとも 1 つのプランを定義する必要があります。 同じオファー内でさまざまなオプションを使用して、さまざまなプランを作成できます。 これらのプラン (SKU とも呼ばれる) は、サービスの収益化やレベルの点で異なる可能性があります。 後で、これらのプランのサービス ID をソリューション パッケージにマップして、これらのプランに対して Dynamics プラットフォームによるランタイム ライセンスの確認を有効にします。 各プランのサービス ID をソリューション パッケージにマップします。 これにより、Dynamics プラットフォームで、これらのプランに対してライセンスの確認を実行できます。

## <a name="create-a-plan"></a>プランの作成

1. **[プランの概要]** ページの上部にある **[+ 新しいプランの作成]** を選択します。
1. 表示されるダイアログ ボックスの **[プラン ID]** ボックスに、一意のプラン ID を入力します。 最大 50 文字の英小文字、ダッシュ、アンダースコアのみを使用してください。 **[作成]** を選択した後にプラン ID を変更することはできません。
1. **[プラン名]** ボックスに、このプランの一意の名前を入力します。 最大 50 文字を使用します。
1. **［作成］** を選択します

## <a name="define-the-plan-listing"></a>プランのリストを定義する

**[プランのリスト]** タブでは、必要に応じてプランの名前と説明をコマーシャル マーケットプレースに表示するように定義できます。 この情報は、Microsoft AppSource の一覧ページに表示されます。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. **[プラン名]** ボックスに、このプランに対して前に指定した名前が表示されます。 これはいつでも変更できます。 この名前は、オファーのソフトウェア プランのタイトルとしてコマーシャル マーケットプレースに表示されます。
1. **[プランの説明]** ボックスで、このソフトウェア プランに固有な点や、オファー内の他のソフトウェア プランとの違いを説明できます。 説明は、最大 500 文字まで許可されます。
1. **[下書きの保存]** を選択し、ページ上部の階層リンクで **[プラン]** を選択します。

    [ ![パートナー センターのオファーの [プランの一覧] ページの [プランの概要] リンクが示されているスクリーンショット。](./media/third-party-license/bronze-plan-workspaces.png) ](./media/third-party-license/bronze-plan-workspaces.png#lightbox)

1. このオファーに対して別のプランを作成するには、 **[プランの概要]** ページの上部にある **[+ 新しいプランの作成]** を選択します。 次に、「[プランを作成する](#create-a-plan)」の手順を繰り返します。 それ以外の場合は、プランの作成が完了したら、次のセクション「サービス ID をコピーする」に進んでください。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. **[プラン名]** ボックスに、このプランに対して前に指定した名前が表示されます。 これはいつでも変更できます。 この名前は、オファーのソフトウェア プランのタイトルとしてコマーシャル マーケットプレースに表示されます。
1. **[プランの説明]** ボックスで、このソフトウェア プランに固有な点や、オファー内の他のソフトウェア プランとの違いを説明できます。 説明は、最大 500 文字まで許可されます。
1. **[ドラフトの保存]** を選択し、左上の **[プランの概要]** を選択します。

    :::image type="content" source="./media/third-party-license/bronze-plan.png" alt-text="パートナー センターのオファーの [プランの一覧] ページの [プランの概要] リンクが示されているスクリーンショット。":::

1. このオファーに対して別のプランを作成するには、 **[プランの概要]** ページの上部にある **[+ 新しいプランの作成]** を選択します。 次に、「[プランを作成する](#create-a-plan)」の手順を繰り返します。 それ以外の場合は、プランの作成が完了したら、次のセクション「サービス ID をコピーする」に進んでください。

---

## <a name="copy-the-service-ids"></a>サービス ID をコピーする

次の手順でソリューション パッケージにマップできるように、作成した各プランのサービス ID をコピーする必要があります。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

- 作成したプランごとに、サービス ID を安全な場所にコピーします。 次の手順でソリューション パッケージに追加します。 サービス ID は、`ISV name.offer name.plan ID` の形式で **[プランの概要]** ページに一覧表示されます。 たとえば、Fabrikam.F365.bronze などです。

    [ ![[プランの概要] ページのスクリーンショット。プランのサービス ID が強調表示されています。](./media/third-party-license/service-id-workspaces.png) ](./media/third-party-license/service-id-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

- 作成したプランごとに、サービス ID を安全な場所にコピーします。 次の手順でソリューション パッケージに追加します。 サービス ID は、`ISV name.offer name.plan ID` の形式で **[プランの概要]** ページに一覧表示されます。 たとえば、Fabrikam.F365.bronze などです。

    :::image type="content" source="./media/third-party-license/service-id.png" alt-text="[プランの概要] ページのスクリーンショット。プランのサービス ID が強調表示されています。":::

---

## <a name="add-service-ids-to-your-solution-package"></a>ソリューション パッケージにサービス ID を追加する

1. 前の手順でコピーしたサービス ID をソリューション パッケージに追加します。 詳細については、[ソリューションへのライセンス メタデータの追加](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution)に関するページと「[アプリの AppSource パッケージを作成する](/powerapps/developer/data-platform/create-package-app-appsource)」を参照してください。
1. CRM パッケージの .zip ファイルを作成したら、それを Azure Blob Storage にアップロードします。 アップロードした CRM パッケージの .zip ファイルが含まれている Azure Blob Storage アカウントの SAS URL を指定する必要があります。

## <a name="next-steps"></a>次のステップ

- 「[Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定する](dynamics-365-customer-engage-technical-configuration.md)」に進んで、ソリューション パッケージをプランにアップロードします。
