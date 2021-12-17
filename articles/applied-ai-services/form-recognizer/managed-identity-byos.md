---
title: Bring Your Own Storage (BYOS) を使用してマネージド ID を作成して使用する
titleSuffix: Azure Applied AI Services
description: BYOS アカウントでマネージド ID を作成して使用する方法を理解する
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83b8867ae750fe85bc848e339ddb0409cba85093
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400606"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>Form Recognizer リソース用のマネージド ID を作成して使用する

> [!IMPORTANT]
> Azure ロールベースのアクセス制御 (Azure RBAC) の割り当ては、現在プレビュー中であり、運用ワークロードにはお勧めしません。 特定の機能がサポートされなかったり、機能が制限されたりすることがあります。 Azure RBAC の割り当ては、マネージド ID のアクセス許可を付与するために使用されます。

## <a name="what-is-managed-identity"></a>マネージド ID とは

Azure マネージド ID は、Azure Active Directory (Azure AD) ID と Azure 管理対象リソースの特定のアクセス許可を作成する、サービス プリンシパルです。 マネージド ID を使用して、Azure AD 認証をサポートする任意のリソースへのアクセス権を付与できます。 アクセス権を付与するには、[Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md) (Azure RBAC) を使用してマネージド ID にロールを割り当てます。  Azure でマネージド ID を使用する場合、追加コストはかかりません。

マネージド ID では、プライベート アクセスとパブリック アクセスの Azure BLOB ストレージ アカウントの両方がサポートされます。  パブリック アクセスを持つストレージ アカウントの場合は、Shared Access Signature (SAS) を使用して制限付きアクセスを許可することを選択できます。   この記事では、Form Recognizer インスタンス向けのシステム割り当てマネージド ID を有効にする方法について説明します。

## <a name="private-storage-account-access"></a>プライベート ストレージ アカウント アクセス
> [!NOTE]
>
> 現在、Form Recognizer は、システム割り当てマネージド ID でのみサポートされています。 ユーザー割り当てマネージド ID は、計画に含まれており、近い将来サポートされる予定です。 


 プライベート Azure ストレージ アカウントのアクセスと認証は、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) でサポートされています。 Azure ストレージ アカウントが Virtual Network (VNet) またはファイアウォールによって保護されている場合、または Bring Your Own Storage (BYOS) を有効にしている場合、Form Recognizer ではストレージ アカウント データに直接アクセスできません。ただし、マネージド ID が有効になると、Form Recognizer サービスは、割り当てられたマネージド ID 資格情報を使用してストレージ アカウントにアクセスできます。

> [!NOTE]
>
> * [**Form Recognizer サンプル ラベル付けツール (FOTT)**](https://fott-2-1.azurewebsites.net/) を使用してストレージ データを分析する場合は、VNet またはファイアウォールの背後にツールをデプロイする必要があります。
>
> * Analyze [**Receipt**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)、[**Business Card**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)、[**Invoice**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)、[**ID Document**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)、および [**Custom Form**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) の API では、要求を生のバイナリ コンテンツとして送信することによって、1 つのドキュメントからデータを抽出できます。 これらのシナリオでは、マネージド ID 資格情報の要件はありません。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)—お持ちでない場合は、[**無料でアカウントを作成できます**](https://azure.microsoft.com/free/)。

* Azure portal 内の [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) または [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) リソース。 詳細の手順については、「[Azure portal を使用して Cognitive Services リソースを作成する](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)」を _参照してください_。

* Form Recognizer リソースと同じリージョンにある [**Azure BLOB ストレージ アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。 

  * ストレージ アカウントがファイアウォールの内側にある場合、**次の構成を有効にする必要があります**。 </br></br>

  * ストレージ アカウント ページで、 **[セキュリティ + ネットワーク]** → **[ネットワーク]** を選択します。
    :::image type="content" source="media/managed-identities/security-and-networking-node.png" alt-text="スクリーンショット: [セキュリティとネットワーク] タブ。":::

  * メイン ウィンドウで、 **[選択したネットワークからのアクセスを許可する]** を選択します。
  :::image type="content" source="media/managed-identities/firewalls-and-virtual-networks.png" alt-text="スクリーンショット: [選択されたネットワーク] のラジオ ボタンが選択された状態。":::

  * 選択されたネットワーク ページで、 **[例外]** カテゴリに移動し、[[**信頼されたサービスの一覧で Azure サービスにこのストレージ アカウントへのアクセスを許可する**](../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) チェックボックスがオンになっていることを確認します。

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="スクリーンショット: ポータル ビューの信頼されたサービスを許可するチェックボックス":::
* Azure portal で使用する [**Azure ロールベースのアクセス制御 (Azure RBAC)**](../../role-based-access-control/role-assignments-portal.md) の概要の理解。

## <a name="managed-identity-assignments"></a>マネージド ID の割り当て

マネージド ID には、**システム割り当て** と **ユーザー割り当て** の 2 種類があります。 現在、Form Recognizer は、システム割り当てマネージド ID でサポートされています。 システム割り当てマネージド ID は、サービス インスタンス上で直接 **有効化** されます。 既定では有効になっていないため、リソースにアクセスして、ID 設定を更新する必要があります。 システム割り当てマネージド ID は、ライフサイクル全体を通してリソースに関連付けされます。 リソースを削除すると、マネージド ID も削除されます。

以下の手順では、システム割り当てマネージド ID を有効にし、Form Recognizer に Azure BLOB ストレージ アカウントへの制限付きアクセスを許可します。

## <a name="enable-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を有効にする

>[!IMPORTANT]
>
> システム割り当てマネージド ID を有効にするには、**Microsoft.Authorization/roleAssignments/write** アクセス許可 ([**所有者**](../../role-based-access-control/built-in-roles.md#owner)や [**ユーザー アクセス管理者**](../../role-based-access-control/built-in-roles.md#user-access-administrator)など) が必要です。 4 つのレベル (管理グループ、サブスクリプション、リソース グループ、リソース) でスコープを指定できます。

1. ご利用の Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、**Form Recognizer** のリソース ページに移動します。

1. 左側のレールで、 **[リソース管理]** リストから **[ID]** を選択します。

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="スクリーンショット: Azure portal の [リソース管理] [ID] タブ。":::

1. メイン ウィンドウで、 **[System assigned Status]\(システム割り当てステータス\)** タブを **[オン]** に切り替えます。

1. **[アクセス許可]** で、 **[Azure ロールの割り当て]** を選択します。

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="スクリーンショット: Azure portal でシステム割り当てマネージド ID を有効にする。":::

1. [Azure ロールの割り当て] ページが開きます。 ドロップダウン メニューからご利用のサブスクリプションを選択し、 **[&plus; ロールの割り当ての追加]** を選択します。

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="スクリーンショット: Azure portal の [Azure ロールの割り当て] ページ。":::

    > [!NOTE]
    >
    > [追加] > [ロールの割り当ての追加] オプションが無効になっているか、アクセス許可エラー [you do not have permissions to add role assignment at this scope]\(このスコープでロールの割り当てを追加するためのアクセス許可がありません\) が発生したために、Azure portal でロールを割り当てることができない場合、現在ストレージ リソースのストレージ スコープで、Microsoft.Authorization/roleAssignments/write アクセス許可を持つロール (所有者やユーザー アクセス管理者など) が割り当てられたユーザーとしてサインインしていることを確認してください。

 7. 次に、Form Recognizer サービス リソースに **ストレージ BLOB データ閲覧者** ロールを割り当てます。 **[ロールの割り当ての追加]** ポップアップ ウィンドウで、以下のようにフィールドに入力して、 **[保存]** を選択します。

    | フィールド | 値|
    |------|--------|
    |**スコープ**| **_Storage_**|
    |**サブスクリプション**| **_ストレージ リソース に関連付けられているサブスクリプション_**。|
    |**リソース**| **_ストレージ リソースの名前_**|
    |**ロール** | **_ストレージ BLOB データ閲覧者_**—Azure Storage Blob コンテナーおよびデータに対する読み取りアクセスを許可します。|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="スクリーンショット: Azure portal の [ロールの割り当ての追加] ページ。":::

1. _[ロールの割り当てが追加されました]_ 確認メッセージが表示されたら、ページを更新して追加されたロールの割り当てを確認します。

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="スクリーンショット: [ロールの割り当てが追加されました] 確認ポップアップ メッセージ。":::

1. 変更がすぐに表示されない場合は、もう一度ページを更新してみてください。 ロールを割り当てたり、ロールの割り当てを削除したりした場合、変更が適用されるまでに最大 30 分かかることがあります。

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="スクリーンショット: [Azure ロールの割り当て] ウィンドウ。":::

 これで完了です。 システム割り当てマネージド ID を有効にする手順を完了しました。 この ID 資格情報を使用すると、BYOS アカウントに格納されているドキュメントとファイルに対する特定のアクセス権を Form Recognizer に付与できます。

## <a name="learn-more-about--managed-identity"></a>マネージド ID に関する詳細情報

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID に関してよく寄せられる質問 - Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)
