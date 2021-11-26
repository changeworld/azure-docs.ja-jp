---
title: Microsoft Azure Maps Creator を管理する
description: この記事では Microsoft Azure Maps Creator を管理する方法について説明します。
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 124b75aa0a80f84299fd0e113f2d4d7f1a427213
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400208"
---
# <a name="manage-azure-maps-creator"></a>Azure Maps Creator を管理する

Azure Maps Creator を使用すると、プライベートな屋内マップ データを作成することができます。 Azure Maps API と Indoor Maps モジュールを使用して、対話型で動的な屋内マップ Web アプリケーションを開発できます。 価格情報については、[「Azure Maps の価格」](https://aka.ms/CreatorPricing)の *Creator* セクションをご覧ください。

この記事では、Azure Maps アカウントで Creator リソースを作成および削除する手順について説明します。

## <a name="create-creator-resource"></a>Creator リソースを作成する

1. [Azure ポータル](https://portal.azure.com)

2. Azure portal メニューに移動します。 **[すべてのリソース]** を選択し、Azure Maps アカウントを選択します。

      :::image type="content" border="true" source="./media/how-to-manage-creator/select-all-resources.png" alt-text="Azure Maps アカウントの選択":::

3. ナビゲーション ウィンドウで、 **[Creator の概要]** を選択し、 **[作成]** を選択します。

    :::image type="content" border="true" source="./media/how-to-manage-creator/creator-blade-settings.png" alt-text="Azure Maps Creator 作成ページ":::

4. Creator リソースの名前、場所、およびマップ プロビジョニングのストレージ ユニットを入力した後、 **[確認および作成]** を選択します。

   :::image type="content" source="./media/how-to-manage-creator/creator-creation-dialog.png" alt-text="Creator アカウント情報入力ページ":::

5. 設定を確認し、 **[作成]** を選択します。

    :::image type="content" source="./media/how-to-manage-creator/creator-create-dialog.png" alt-text="Creator アカウント設定確認ページ":::

    デプロイの完了後、成功または失敗のメッセージを含むページが表示されます。

    :::image type="content" source="./media/how-to-manage-creator/creator-resource-created.png" alt-text="リソース デプロイ状態ページ":::

6. **[リソースに移動]** を選択します。 Creator リソース ビュー ページには、Creator リソースの状態と選択されている地域が表示されます。
      :::image type="content" source="./media/how-to-manage-creator/creator-resource-view.png" alt-text="Creator 状態ページ":::

   >[!NOTE]
   >Azure Maps アカウントに戻るには、ナビゲーション ウィンドウで **[Azure Maps アカウント]** を選択します。

## <a name="delete-creator-resource"></a>Creator リソースを削除する

Creator リソースを削除するには:

1. Azure Maps アカウントで、 **[Creator]** の下の **[概要]** を選択します。

2. **[削除]** を選択します。

    >[!WARNING]
    >Azure Maps アカウントの Creator リソースを削除すると、Creator サービスを使用して作成したコンバージョン、データセット、タイルセット、地物状態セットも削除されます。 Creator リソースを削除すると、元に戻すことはできません。

     :::image type="content" source="./media/how-to-manage-creator/creator-delete.png" alt-text="削除ボタンがある Creator ページ":::

3. 削除確認のメッセージが表示されるので、Creator リソースの名前を入力して確定します。 リソースが削除されると、次のような確認ページが表示されます。

     :::image type="content" source="./media/how-to-manage-creator/creator-confirm-delete.png" alt-text="削除の確認が表示された Creator ページ":::

## <a name="authentication"></a>認証

Creator は Azure Maps Access Control (IAM) の設定を継承します。 データにアクセスするすべての API 呼び出しは、認証および承認の規則と共に送信される必要があります。

Creator の使用状況データは、Azure Maps の使用状況グラフとアクティビティ ログに組み込まれています。  詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」をご覧ください。

>[!Important]
>次の機能を使用することをお勧めします。
>
> * Creator サービスを使用して Azure Maps アカウントで作成されたすべてのソリューションでは、Azure Active Directory (Azure AD)。 Azure AD の詳細については、「[Azure AD 認証](azure-maps-authentication.md#azure-ad-authentication)」を参照してください。
>
>* ロールベースのアクセス制御設定 (RBAC)。 これらの設定を使用すると、マップの作成者が Azure Maps データ共同作成者のロールとして機能し、ユーザーは Azure Maps データ リーダーのロールとして機能することができます。 詳細については、「[ロールベースのアクセス制御による認証](azure-maps-authentication.md#authorization-with-role-based-access-control)」を参照してください。

## <a name="access-to-creator-services"></a>Creator サービスにアクセスする

Creator サービスと、Creator でホストされるデータを使用するサービス (Render Service など) には、地理的な URL でアクセスできます。 地理的な URL は、作成時に選択した場所によって決まります。 たとえば、Creator を米国の地理的な場所のあるリージョンに作成した場合は、変換サービスへのすべての呼び出しを `us.atlas.microsoft.com/conversions` に送信する必要があります。 リージョンと地理的な場所のマッピングを表示するには、「[Creator サービスの地理的スコープ](creator-geographic-scope.md)」を参照してください。

また、Creator にインポートされたすべてのデータは、Creator リソースと同じ地理的な場所にアップロードされる必要があります。 たとえば、Creator が米国でプロビジョニングされている場合、すべての生データを `us.atlas.microsoft.com/mapData/upload` 経由でアップロードする必要があります。

## <a name="next-steps"></a>次のステップ

室内マッピングに関する Creator サービスの概要:

> [!div class="nextstepaction"]
> [データのアップロード](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [データ変換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [データセット](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [タイルセット](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [地物状態セット](creator-indoor-maps.md#feature-statesets)

Creator サービスを使用して、アプリケーションにフロア ガイドをレンダリングする方法について学習します。

> [!div class="nextstepaction"]
> [Azure Maps Creator チュートリアル](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Indoor Maps モジュールを使用する](how-to-use-indoor-module.md)
