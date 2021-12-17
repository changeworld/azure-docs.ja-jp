---
title: Azure Active Directory ポータルでユーザーの一覧をダウンロードする | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザー レコードを一括ダウンロードします。
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfeef7e89d94b2f00d1ca1795bb9b4874858411
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402088"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Azure Active Directory ポータルでユーザーの一覧をダウンロードする

Azure Active Directory (Azure AD) では、ユーザーの一覧を一括にダウンロードする操作がサポートされています。

## <a name="required-permissions"></a>必要なアクセス許可

管理者ユーザー、管理者でないユーザーのどちらもユーザーの一覧をダウンロードできます。

## <a name="to-download-a-list-of-users"></a>ユーザーの一覧をダウンロードするには

1. [Azure DevOps 組織にサインインします](https://aad.portal.azure.com)。
2. **[Azure Active Directory]**  >  **[ユーザー]**  に移動します。
3. Azure AD で、 **[ユーザー]**  >  **[ユーザーをダウンロード]** を選択します。 既定では、すべてのユーザー プロファイルがエクスポートされます。
4. **[ユーザーをダウンロード]** ページで **[開始]** を選択し、ユーザー プロファイルのプロパティが一覧表示された CSV ファイルを受け取ります。 エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

   ![ダウンロードするユーザー一覧を表示する場所を選択します](./media/users-bulk-download/bulk-download.png)
   
>[!NOTE]
>ダウンロード ファイルには、適用したフィルターのスコープに基づいてフィルター処理されたユーザーの一覧が含まれます。

   次のユーザー属性が含まれます。

   - `userPrincipalName`
   - `displayName`
   - `surname`
   - `mail`
   - `givenName`
   - `objectId`
   - `userType`
   - `jobTitle`
   - `department`
   - `accountEnabled`
   - `usageLocation`
   - `streetAddress`
   - `state`
   - `country`
   - `physicalDeliveryOfficeName`
   - `city`
   - `postalCode`
   - `telephoneNumber`
   - `mobile`
   - `authenticationAlternativePhoneNumber`
   - `authenticationEmail`
   - `alternateEmailAddress`
   - `ageGroup`
   - `consentProvidedForMinor`
   - `legalAgeGroupClassification`

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中の一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

ユーザーの一覧をエクスポートするための一括アクティビティはそれぞれ最大 1 時間実行できます。 このペースで、最大 50 万人分のユーザーの一覧をエクスポートしてダウンロードできます。

## <a name="next-steps"></a>次のステップ

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一括復元](users-bulk-restore.md)
