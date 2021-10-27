---
title: Microsoft AppSource で Azure Container オファーに対するプランの技術的な構成を設定します。
description: Microsoft AppSource で Azure Container オファーに対するプランの技術的な構成を設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: f6145e8732252f7d7b407a2d258d2127e6911ddc
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072159"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Azure Container オファーに対するプランの技術的な構成を設定する

コンテナー イメージは、プライベートの [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) でホストされる必要があります。 このページを使用して、Azure Container Registry 内のコンテナー イメージ リポジトリの参照情報を指定します。

オファーを発行した後、特定のパブリック コンテナー レジストリでコンテナー イメージが Azure Marketplace にコピーされます。 Azure ユーザーがモジュールの使用を要求すると、プライベート コンテナー レジストリではなく、Azure Marketplace パブリック コンテナー レジストリからサービスが提供されます。

複数のプラットフォームを対象にしたり、タグを利用し、複数のバージョンのモジュール コンテナー イメージを提供したりできます。 タグとバージョン管理の詳細については、「[Azure Container の技術アセットの準備](azure-container-technical-assets.md)」を参照してください。

## <a name="image-repository-details"></a>イメージ リポジトリの詳細

リソースの使用状況が報告され、コンテナー イメージが含まれる Azure Container Registry に対してサービスが課金される **Azure サブスクリプション ID** を指定します。 この ID は Azure portal の[サブスクリプション ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にあります。

Azure Container Registry とコンテナー イメージが含まれる [**Azure リソース グループ名**](../azure-resource-manager/management/manage-resource-groups-portal.md)を指定します。 リソース グループにはサブスクリプション ID (上記) でアクセスできます。 この名前は Azure portal の[リソース グループ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ページにあります。

コンテナー イメージが含まれる [**Azure コンテナー レジストリ名**](../container-registry/container-registry-intro.md)を指定します。 このコンテナー レジストリは、前に指定した Azure リソース グループに存在する必要があります。 完全なログイン サーバー名ではなく、レジストリ名のみを指定します。 名前から **azurecr.io** を省略します。 レジストリ名は、Azure portal の[コンテナー レジストリ ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)にあります。

コンテナー イメージが含まれる Azure Container Registry に関連付けられている [**Azure Container Registry の管理者ユーザー名**](../container-registry/container-registry-authentication.md#admin-account)を指定します。 このユーザー名とパスワード (次の手順) は、会社にレジストリへのアクセス権があることを確実にするために必要です。 管理者ユーザー名とパスワードを取得するには、Azure コマンド ライン インターフェイス (CLI) で **admin-enabled** プロパティを **[True]** に設定します。 任意で、Azure portal で **[管理者ユーザー]** を **[有効にする]** に設定できます。

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="[コンテナー レジストリの更新] ダイアログ ボックスの画像。":::

コンテナー イメージが含まれる Azure Container Registry に関連付けられている管理者ユーザー名の **Azure Container Registry の管理者パスワード** を指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 **[コンテナー レジストリ]**  >  **[アクセス キー]** に移動するか、Azure CLI で [show コマンド](/cli/azure/acr/credential#az-acr-credential-show)を使用することで、Azure portal からパスワードを取得できます。

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Azure portal のアクセス キー画面の画像。":::

イメージが含まれる **Azure Container Registry 内のリポジトリ名** を指定します。 レジストリにイメージをプッシュするとき、リポジトリの名前を指定します。 リポジトリの名前は、[[コンテナー レジストリ]](https://azure.microsoft.com/services/container-registry/) の **リポジトリ ページ** に移動すると見つかります。 詳細については、「[Azure Portal でコンテナー レジストリ リポジトリを表示する](../container-registry/container-registry-repositories.md)」を参照してください。 設定した名前は変更できません。 アカウント内のオファー別に一意の名前を使用します。

## <a name="image-versions"></a>イメージ バージョン

更新プログラムを公開したとき、顧客は Azure Marketplace から更新プログラムを自動的に取得できなければなりません。 彼らが更新しない場合、特定のバージョンのイメージを維持できなければなりません。 これはイメージを更新するたびに新しいイメージ タグを追加することで行います。

**[Add Image version]\(イメージ バージョンの追加\)** を選択して、サポートされているすべてのプラットフォームでイメージの最新バージョンをポイントする **イメージ タグ** を含めます。 バージョン タグも含める必要があります (たとえば、xx.xx.xx から始まり、xx は数字です)。 複数のプラットフォームを対象とするには、顧客は[マニフェスト タグ](https://github.com/estesp/manifest-tool)を使用する必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。 マニフェスト タグ (最新のタグを除く) はすべて X.Y- か X.Y.Z- で始める必要があります。X、Y、Z は整数です。 たとえば、最新タグが `1.0.1-linux-x64`、`1.0.1-linux-arm32`、`1.0.1-windows-arm32` を指している場合、これらの 6 つのタグをこのフィールドに追加する必要があります。 タグとバージョン管理の詳細については、「[Azure Container の技術アセットの準備](azure-container-technical-assets.md)」を参照してください。

> [!TIP]
> テスト タグをイメージに追加すると、テスト中にイメージを識別できます。

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>次の手順

- **Microsoft と共同販売する** (省略可能) には、左側のナビゲーション メニューでそれを選択します。 詳細については、[共同販売パートナーのエンゲージメント](./co-sell-overview.md)に関するページを参照してください。
- **CSP (クラウド ソリューション パートナー、省略可能) を通して再販** するには、左側のナビゲーションメニューでそれを選択します。 詳細については、[CSP パートナーによる再販](cloud-solution-providers.md)に関するページを参照してください。
- これらのいずれかを設定していない場合、または完了している場合は、[プランを確認して発行](review-publish-offer.md)します。