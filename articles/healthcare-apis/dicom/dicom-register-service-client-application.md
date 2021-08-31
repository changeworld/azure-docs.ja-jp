---
title: Azure Active Directory にサービス クライアント アプリケーションを登録する - DICOM 用 Azure Healthcare APIs
description: この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779478"
---
# <a name="register-a-service-client-application"></a>サービス クライアント アプリケーションを登録する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、サービス クライアント アプリケーションを Azure Active Directory (Azure AD) に登録する方法について説明します。

## <a name="application-registrations-in-the-azure-portal"></a>Azure Portal のアプリケーションの登録

クライアント アプリケーションの登録とは、トークンの認証および取得に使用できるアプリケーションの Azure AD の表現です。 サービス クライアントは、対話型のユーザー認証なしでアクセス トークンを取得するために、アプリケーションから使用されることを目的としています。 アクセス トークンを取得するときには、特定のアプリケーション アクセス許可を持ち、アプリケーション シークレット (パスワード) を使用します。

下の手順を参照して新しいサービス クライアントを作成します。

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** を選択します。
3. **[新規登録]** を選択します。

   [ ![Azure の [アプリの登録]。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. DICOM サービス アプリケーションにユーザー向けの表示名を指定します。 サービス クライアント アプリケーションでは、一般に応答 URL を使用しません。

   [ ![Azure の [アプリケーションの登録]。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

これで DICOM サービス アプリケーションが登録されたので、ユーザーに代わり、このアプリケーションによって要求する必要がある API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** ブレードを選択します。

   [ ![API アクセス許可を追加する。](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. **[アクセス許可の追加]** を選択します。

   DICOM を使用する場合は、 **[所属する組織で使用している API]** で **Azure Healthcare APIs** を検索し、DICOM 用の Azure API へのアクセス許可を追加します。 これは、`Microsoft.HealthcareAPIs` リソース プロバイダーを既に登録している場合にのみ見つけることができます。

   [ ![自分の API のアクセス許可。](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [ ![API アクセス許可の要求。](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. ユーザーに代わって機密クライアント アプリケーションが要求するスコープ (アクセス許可) を選択します。

   [ ![アクセス許可のスコープの選択。](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. アプリケーションへの同意を許可します。 必要なアクセス許可を持っていない場合は、AAD 管理者に確認してください。


## <a name="application-secret"></a>アプリケーション シークレット

サービス クライアントでは、トークンを取得するためにシークレット (パスワード) を必要とします。

1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。

   [ ![証明書とシークレット。](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. クライアント シークレットの **[説明]** を入力します。 **[有効期限]** ドロップダウン メニューを選択して有効期限のタイム フレームを選択し、 **[追加]** をクリックします。

   [ ![クライアント シークレットの説明。](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. クライアント シークレット文字列が作成された後、その **[値]** と **[ID]** をコピーし、任意の安全な場所に格納します。

   [ ![クライアント シークレットの値 と ID。](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. [追加] を選択します。

> [!NOTE]
> クライアント シークレット文字列が Azure portal に表示されるのは 1 回だけです。 [Certificates & Secrets]\(クライアントとシークレット\) Web ページを離れてからそこに戻った場合、[値] 文字列はマスクされます。 クライアント シークレット文字列が生成された直後に、そのコピーを作成することが重要です。 クライアント シークレットのバックアップ コピーがない場合は、上記の手順を繰り返して再生成する必要があります。


## <a name="next-steps"></a>次のステップ

この記事では、サービス クライアント アプリケーションを Azure AD に登録する方法について学習しました。 

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)




