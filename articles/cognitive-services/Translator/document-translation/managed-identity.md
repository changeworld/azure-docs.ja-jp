---
title: マネージド ID の作成と使用
titleSuffix: Azure Cognitive Services
description: Azure portal でマネージド ID を作成して使用する方法について説明します
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 09/09/2021
ms.author: lajanuar
ms.openlocfilehash: b6a25b832c3acc65d6a7b343901cf23cdd17f98a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255789"
---
# <a name="create-and-use-managed-identity"></a>マネージド ID の作成と使用

> [!IMPORTANT]
>
> ドキュメント翻訳のマネージド ID は、現在グローバル リージョンでは使用できません。 ドキュメント翻訳の操作にマネージド ID を使用する場合は、グローバルではない Azure リージョンに [Translator リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)してください。

## <a name="what-is-managed-identity"></a>マネージド ID とは

 Azure マネージド ID は、Azure Active Directory (Azure AD) ID と Azure 管理対象リソースの特定のアクセス許可を作成する、サービス プリンシパルです。 マネージド ID を使用して、Azure AD 認証をサポートする任意のリソースへのアクセス権を付与できます。 アクセス権を付与するには、[Azure ロールベースのアクセス制御](../../../role-based-access-control/overview.md) (Azure RBAC) を使用してマネージド ID にロールを割り当てます。  Azure でマネージド ID を使用する場合、追加コストはかかりません。

マネージド ID では、プライベート アクセスとパブリック アクセスの Azure BLOB ストレージ アカウントの両方がサポートされます。  **パブリック アクセスを持つ** ストレージ アカウントの場合は、Shared Access Signature (SAS) を使用して制限付きアクセスを許可することを選択できます。  この記事では、システム割り当てマネージド ID を使用して、Azure BLOB ストレージ アカウント内の翻訳ドキュメントへのアクセスを管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)—お持ちでない場合は、[**無料でアカウントを作成できます**](https://azure.microsoft.com/free/)。

* **グローバルではない** リージョンに割り当てられた、[**単一サービス Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (マルチサービス Cognitive Services ではない) リソース。 詳細の手順については、「[Azure portal を使用して Cognitive Services リソースを作成する](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)」を _参照してください_。

* Azure portal で使用する [**Azure ロールベースのアクセス制御 (Azure RBAC)**](../../../role-based-access-control/role-assignments-portal.md) の概要の理解。

* Translator リソースと同じリージョンにある [**Azure BLOB ストレージ アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。 

* **ストレージ アカウントがファイアウォールの内側にある場合、次の構成を有効にする必要があります**。 </br>

  * ストレージ アカウント ページで、左側のメニューから **[セキュリティとネットワーク]** → **[ネットワーク]** を選択します。
    :::image type="content" source="../media/managed-identities/security-and-networking-node.png" alt-text="スクリーンショット: [セキュリティとネットワーク] タブ。":::

  * メイン ウィンドウで、 **[選択されたネットワークからのアクセスを許可する]** を選択します。
  :::image type="content" source="../media/managed-identities/firewalls-and-virtual-networks.png" alt-text="スクリーンショット: [選択されたネットワーク] ラジオ ボタンが選択された状態。":::

  * 選択されたネットワーク ページで、 **[例外]** カテゴリに移動し、[[**信頼されたサービスの一覧で Azure サービスにこのストレージ アカウントへのアクセスを許可する**](../../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) チェックボックスがオンになっていることを確認します。

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="スクリーンショット: ポータル ビューの信頼されたサービスを許可するチェックボックス":::

## <a name="managed-identity-assignments"></a>マネージド ID の割り当て

マネージド ID には、**システム割り当て** と **ユーザー割り当て** の 2 種類があります。  現在、ドキュメント翻訳はシステム割り当てマネージド ID でサポートされています。 システム割り当てマネージド ID は、サービス インスタンス上で直接 **有効化** されます。 既定では有効になっていないため、リソースにアクセスして、ID 設定を更新する必要があります。 システム割り当てマネージド ID は、ライフサイクル全体を通してリソースに関連付けされます。 リソースを削除すると、マネージド ID も削除されます。

以下の手順では、システム割り当てマネージド ID を有効にし、Translator リソースに Azure BLOB ストレージ アカウントへの制限付きアクセスを許可します。

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>Azure portal を使用してシステム割り当てマネージド ID を有効にする

>[!IMPORTANT]
>
> システム割り当てマネージド ID を有効にするには、**Microsoft.Authorization/roleAssignments/write** アクセス許可 ([**所有者**](../../../role-based-access-control/built-in-roles.md#owner)や [**ユーザー アクセス管理者**](../../../role-based-access-control/built-in-roles.md#user-access-administrator)など) が必要です。 4 つのレベル (管理グループ、サブスクリプション、リソース グループ、リソース) でスコープを指定できます。

1. ご利用の Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、**Translator** のリソース ページに移動します。

1. 左側のレールで、 **[リソース管理]** リストから **[ID]** を選択します。

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="スクリーンショット: Azure portal の [リソース管理] [ID] タブ。":::

1. メイン ウィンドウで、 **[System assigned Status]\(システム割り当てステータス\)** タブを **[オン]** に切り替えます。

1. **[アクセス許可]** で、 **[Azure ロールの割り当て]** を選択します。

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="スクリーンショット: Azure portal でシステム割り当てマネージド ID を有効にする。":::

1. [Azure ロールの割り当て] ページが開きます。 ドロップダウン メニューからご利用のサブスクリプションを選択し、 **[&plus; ロールの割り当ての追加]** を選択します。

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="スクリーンショット: Azure portal の [Azure ロールの割り当て] ページ。":::

    >[!NOTE]
    >
    > [追加] > [ロールの割り当ての追加] オプションが無効になっているか、アクセス許可エラー "このスコープでロールの割り当てを追加するためのアクセス許可がありません" が発生したために、Azure portal でロールを割り当てることができない場合は、現在、ストレージ リソースのストレージ スコープで、Microsoft.Authorization/roleAssignments/write アクセス許可を持つロール ([**所有者**](../../../role-based-access-control/built-in-roles.md#owner)や [**ユーザー アクセス管理者**](../../../role-based-access-control/built-in-roles.md#user-access-administrator)など) が割り当てられたユーザーとしてサインインしていることを確認してください。

1. 次に、Translator サービス リソースに **ストレージ BLOB データ共同作成者** ロールを割り当てます。 **[ロールの割り当ての追加]** ポップアップ ウィンドウで、以下のようにフィールドに入力して、 **[保存]** を選択します。

    | フィールド | 値|
    |------|--------|
    |**スコープ**| **_Storage_**。|
    |**サブスクリプション**| **_ストレージ リソース に関連付けられているサブスクリプション_**。|
    |**リソース**| **_ストレージ リソースの名前_**。|
    |**ロール** | **_ストレージ BLOB データ共同作成者_**。|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="スクリーンショット: Azure portal の [ロールの割り当ての追加] ページ。":::

1. _[ロールの割り当てが追加されました]_ 確認メッセージが表示されたら、ページを更新して追加されたロールの割り当てを確認します。 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="スクリーンショット: [ロールの割り当てが追加されました] 確認ポップアップ メッセージ。":::

1. 変更がすぐに表示されない場合は、もう一度ページを更新してみてください。 ロールを割り当てたり、ロールの割り当てを削除したりした場合、変更が適用されるまでに最大 30 分かかることがあります。

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="スクリーンショット: [Azure ロールの割り当て] ウィンドウ。":::

 お疲れ様です。 システム割り当てマネージド ID を有効にする手順を完了しました。 この ID 資格情報を使用して、Translator にストレージ リソースへの特定のアクセス権を付与ことができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID: よく寄せられる質問](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[マネージド ID を使用してアクセス トークンを取得する](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)