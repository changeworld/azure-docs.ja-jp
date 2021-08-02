---
title: ダウンロード可能なアクセス レビュー履歴レポート (プレビュー) を作成して管理する - Azure Active Directory
description: Azure Active Directory アクセス レビューを使用して、組織内のアクセス レビューのレビュー履歴をダウンロードできます。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/25/2021
ms.author: ajburnle
ms.openlocfilehash: 6f0d9b92b9aa3bb48533d5270c0fcd31aa8df82c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026649"
---
# <a name="create-and-manage-downloadable-access-review-history-report-preview-in-azure-ad-access-reviews"></a>Azure AD アクセス レビューでダウンロード可能なアクセス レビュー履歴レポート (プレビュー) を作成して管理する

Azure Active Directory (Azure AD) アクセス レビューを使用すると、ダウンロード可能なレビュー履歴を作成すると、組織がより多くの分析情報を得るために役立ちます。 レポートは、レポートの作成時にレビュー担当者が行った決定をプルします。 これらのレポートは、特定の時間枠の特定のアクセス レビューを含めるように構築でき、また、さまざまなレビューの種類とレビューの結果を含められるようフィルタリングできます。
 
## <a name="who-can-access-and-request-review-history"></a>レビュー履歴にアクセスして要求できるユーザー

レビュー履歴と要求レビュー履歴は、アクセス レビューを表示する権限を持っているすべてのユーザーが使用できます。 アクセス レビューを表示および作成できるロールを確認するには、[レビューできるリソースの種類](deploy-access-reviews.md#what-resource-types-can-be-reviewed)に関するページを参照してください。 グローバル管理者とグローバル閲覧者は、すべてのアクセス レビューを表示できます。 他のすべてのユーザーは、生成されたアクセス レビューに関するレポートのみを表示できます。

## <a name="how-to-create-a-review-history-report"></a>レビュー履歴レポートを作成する方法

**前提条件のロール:** アクセス レビューを表示する権限を持つすべてのユーザー

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。

1. 左側のメニューの **[アクセス レビュー]** で、 **[履歴の確認]** を選択します。
 
1. **[新しいレポート]** を選択します。 

1. レビューの開始日と終了日を指定します。

1. レビューの種類を選択し、レポートに含める結果を確認します。 

    ![アクセス レビュー - アクセス レビュー履歴レポート - 作成](./media/access-reviews-downloadable-review-history/create-review-history.png)

1. 次に、 **[作成]** を選択して、アクセス レビュー履歴レポートを作成します。

## <a name="how-to-download-review-history-reports"></a>レビュー履歴レポートをダウンロードする方法

レビュー履歴レポートを作成したら、ダウンロードできます。 作成されたレポートはすべて、CSV 形式で 30 日間ダウンロードできます。

1. **[ID ガバナンス]** の **[履歴の確認]** を選択します。 作成したレビュー履歴レポートはすべて使用できます。 
1. ダウンロードするレポートを選択します。 

## <a name="what-is-included-in-a-review-history-report"></a>レビュー履歴レポートに含まれるもの

レポートでは、ユーザーごとに詳細が表示され、次が示されます。

| 要素名 | 説明 |
| --- | --- |
| AccessReviewId |  レビュー オブジェクト ID |
| ReviewType | レビューの種類には、グループ、アプリケーション、Azure AD ロール、Azure ロール、アクセス パッケージが含まれます|
|ResourceDisplayName | レビューされるリソースの表示名 |
| ResourceId | レビューされるリソースの ID |
| ReviewName |  レビューの名前 |
| CreatedDateTime | レビューの作成日時 |
| ReviewStartDate | レビューの開始日
| ReviewEndDate | レビューの終了日 |
| ReviewStatus | レビューの状態。 すべてのレビューの状態については、[こちら](create-access-review.md)でアクセス レビューの状態テーブルを参照 してください。 |
| OwnerId | レビュー担当者の所有者 ID |
| OwnerName | レビュー担当者の所有者名 |
| OwnerUPN | レビュー担当者所有者のユーザー プリンシパル名 |
| PrincipalId | レビュー中のプリンシパルの ID |
| プリンシパル名 | レビュー中のプリンシパルの名前 |
| PrincipalUPN | レビューされるユーザーのプリンシパル名 |
| PrincipalType | プリンシパルの種類。 オプションには、ユーザー、グループ、サービス プリンシパルが含まれます |
| ReviewDate | レビューの日付 |
| ReviewResult | レビュー結果には、拒否、承認、およびレビューされていないが含まれます |
|妥当性 | レビュー担当者によって提供されるレビュー結果の正当な理由 |
| ReviewerId | レビュー担当者の ID |
| ReviewerName | レビュー担当者名 |
| ReviewerUPN | レビュー担当者のユーザー プリンシパル名 |
| ReviewerEmailAddress | レビュー担当者のメールアドレス |
| AppliedByName | レビュー結果を適用したユーザーの名前 |
| AppliedByUPN | レビュー結果を適用したユーザーのユーザー プリンシパル名|
| AppliedByEmailAddress | レビュー結果を適用したユーザーのメールアドレス |
| AppliedDate | レビュー結果が適用された日付 |
| AccessRecommendation | システムの推奨事項には、承認、拒否、および情報なしが含まれます |
|SubmissionResult | レビュー結果の送信状態には、適用済み、適用なしが含まれます。 |

## <a name="next-steps"></a>次のステップ
- [グループまたはアプリケーションへのアクセスのレビュー](perform-access-review.md)
- [グループまたはアプリケーションへの自分のアクセス権のレビュー](review-your-access.md)
- [グループまたはアプリケーションのアクセス レビューを完了する](complete-access-review.md)
