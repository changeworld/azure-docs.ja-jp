---
title: Azure Active Directory ポータルでユーザーの一覧をダウンロードする (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザー レコードを一括ダウンロードします。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517065"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Azure Active Directory ポータルでユーザーの一覧をダウンロードする (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括インポート (作成) 操作がサポートされています。

## <a name="required-permissions"></a>必要なアクセス許可

Azure AD 管理センターからユーザー一覧をダウンロードするには、Azure AD の組織レベルの管理者ロールの 1 つが割り当てられたユーザーとしてサインインしている必要があります。 ゲスト招待元とアプリケーション開発者は、管理者ロールとは見なされません。

## <a name="to-download-a-list-of-users"></a>ユーザーの一覧をダウンロードするには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[ユーザーをダウンロード]** を選択します。
1. **[ユーザーをダウンロード]** ページで **[開始]** を選択し、ユーザー プロファイルのプロパティが一覧表示された CSV ファイルを受け取ります。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

   ![ダウンロードするユーザー一覧を表示する場所を選択します](./media/users-bulk-download/bulk-download.png)

   ダウンロードしたファイルには、フィルター処理されたユーザーの一覧が含まれます。

   次のユーザー属性が含まれます。 

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - manager
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のご自分の一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

ユーザー一覧の作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 50 万ユーザーを一括作成およびダウンロードできます。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一括復元](users-bulk-restore.md)
