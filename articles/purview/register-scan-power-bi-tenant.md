---
title: Power BI テナントの登録とスキャン (プレビュー)
description: Azure Purview portal を使用して Power BI テナントの登録とスキャンを行う方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 1f08bfd6b7c0439dd08a3091b5ea927781af9b84
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387583"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI テナントの登録とスキャン (プレビュー)

この記事では、Azure Purview portal を使用して Power BI テナントの登録とスキャンを行う方法について説明します。

> [!Note]
> Purview インスタンスと Power BI テナントが同じ Azure テナントにある場合、Power BI テナントのスキャンの設定にはマネージド ID (MSI) 認証のみが使用できます。 

## <a name="create-a-security-group-for-permissions"></a>アクセス許可のためのセキュリティ グループの作成

認証を設定するには、セキュリティ グループを作成し、カタログのマネージド ID を追加します。

1. [Azure portal](https://portal.azure.com) で、**Azure Active Directory** を検索します。
1. 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)」に従って、Azure Active Directory で新しいセキュリティ グループを作成します。

    > [!Tip]
    > 使用するセキュリティ グループが既にある場合は、この手順を省略できます。

1. **[グループの種類]** として **[セキュリティ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="セキュリティ グループの種類":::

1. カタログのマネージド ID をこのセキュリティ グループに追加します。 **[メンバー]** を選択してから **[+ メンバーの追加]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="カタログのマネージド インスタンスをグループに追加します。":::

1. カタログを検索して選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="カタログを検索して追加する":::

    追加されたことを示す成功通知が表示されます。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="カタログ MSI の追加成功":::

## <a name="associate-the-security-group-with-the-tenant"></a>セキュリティ グループのテナントへの関連付け

1. [Power BI 管理ポータル](https://app.powerbi.com/admin-portal/tenantSettings)にログインします。

    > [!Important]
    > テナント設定のページを表示するには、Power BI 管理者である必要があります。

1. **[開発者向け設定]**  >  **[Allow service principals to use read-only Power BI admin APIs (Preview)]\(読み取り専用 Power BI Admin API の使用をサービス プリンシパルに許可 (プレビュー)\)** を選択します。
1. **[特定のセキュリティ グループ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="サービス プリンシパルが読み取り専用 Power BI Admin API のアクセス許可を取得できるようにする方法を示す画像":::

    > [!Caution]
    > 作成したセキュリティ グループ (データ カタログのマネージド ID をメンバーとして持つ) に読み取り専用 Power BI Admin API の使用を許可するときは、このテナント内のすべての Power BI 成果物のメタデータ (ダッシュボードやレポートの名前、所有者、説明など) へのアクセスも許可します。 メタデータが Azure Purview に取り込まれると、Purview のアクセス許可 (Power BI のアクセス許可ではない) によって、そのメタデータを表示できるユーザーが決まります。

    > [!Note]
    > 開発者向け設定からセキュリティ グループを削除することはできますが、以前に抽出されたメタデータは Purview アカウントから削除されません。 これは、必要に応じて個別に削除することができます。

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI の登録とスキャンの設定

Power BI テナントの Admin API に接続するためのカタログのアクセス許可が付与されたので、カタログ ポータルからスキャンを設定できるようになりました。

まず、Purview URL に特別な機能フラグを追加します 

1. Purview インスタンスの URI の末尾に、次の文字列を追加します: `?feature.ext.catalog={"pbi":"true"}`。 これにより、カタログの Power BI 登録オプションが有効になります。

1. **管理センター** のアイコンを選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="管理センターのアイコン。":::

1. 次に、 **[データ ソース]** で **[+ 新規]** を選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="新しいデータ ソースのボタンの画像":::

    データ ソースとして **[Power BI]** を選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="選択可能なデータ ソースの一覧を示す画像":::

1. Power BI インスタンスにフレンドリ名を付けます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI データ ソースのフレンドリ名を示す画像":::

    名前の長さは 3 から 63 文字で、文字、数字、アンダースコア、およびハイフンのみを使用できます。  スペースは使用できません。

    既定では、同じ Azure サブスクリプションに存在する Power BI テナントがシステムによって検出されます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="登録された Power BI データ ソース":::

1. スキャンに名前を付けます。 サポートされている認証方法は **[マネージド ID]** のみであることに留意してください。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI スキャンの設定を示す画像":::

    スキャンの名前の長さは 3 から 63 文字で、文字、数字、アンダースコア、およびハイフンのみを使用できます。  スペースは使用できません。

1. スキャン トリガーを設定します。 オプションとしては、 **[1 度]** 、 **[7 日ごと]** 、 **[30 日ごと]** があります。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="スキャン トリガーの画像":::

1. **[Review new scan]\(新しいスキャンを確認\)** で **[保存および実行]** を選択してスキャンを開始します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI の保存および実行画面の画像":::

## <a name="next-steps"></a>次のステップ

PowerShell コマンドレットを使用して Power BI テナントを登録およびスキャンする方法については、以下を参照してください。
  
- [PowerShell を使用した Power BI の登録とスキャン](powershell-register-scan-power-bi.md)
