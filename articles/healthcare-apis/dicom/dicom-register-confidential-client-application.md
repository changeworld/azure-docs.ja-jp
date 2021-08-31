---
title: Azure Active Directory に機密クライアント アプリケーションを登録する - DICOM 用 Azure Healthcare APIs
description: この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779489"
---
# <a name="register-a-confidential-client-application"></a>機密クライアント アプリケーションを登録する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、機密クライアント アプリケーションを Azure Active Directory (Azure AD) に登録する方法について説明します。

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

クライアント アプリケーションの登録は、ユーザーに代わって認証に使用でき、リソース アプリケーションへのアクセスを要求できるアプリケーションを Azure AD で表したものです。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。

新しい機密クライアント アプリケーションを登録するには、下の手順を参照してください。

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[アプリの登録]** ブレードを選択します。
3. **[新規登録]** を選択します。

   [ ![Azure の [アプリの登録]。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. このアプリケーションのユーザー向け表示名を入力します。

   [ ![Azure の [アプリケーションの登録]。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. **[サポートされているアカウントの種類]** では、アプリケーションを使用できる、または API にアクセスできるユーザーを選択します。
6. (**オプション**) **[リダイレクト URI]** を指定します。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、入力します。
7. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

これでアプリケーションが登録されたので、ユーザーに代わり、このアプリケーションによって要求する必要のある API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** ブレードを選択します。

   [ ![API アクセス許可を追加する](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. **[アクセス許可の追加]** を選択します。

   Azure Healthcare APIs を使用する場合は、 **[所属する組織で使用している API]** で **DICOM 用の Azure API** を検索し、DICOM サービスへのアクセス許可を追加します。 

   [ ![API アクセス許可を検索する](media/dicom-search-apis-permissions.png) ](media/dicom-search-apis-permissions.png#lightbox)

   DICOM 用の Azure API の検索結果が返されるのは、既に DICOM サービスをワークスペースにデプロイしている場合のみです。

   別のリソース アプリケーションを参照する場合は、 **[所属する組織で使用している API]** で作成済みの DICOM API リソース アプリケーションの登録を選択します。

3. ユーザーに代わって機密クライアント アプリケーションが要求するスコープ (アクセス許可) を選択します。 **user_impersonation** を選択し、 **[アクセス許可の追加]** を選択します。

   [ ![アクセス許可のスコープの選択。](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>アプリケーション シークレット

1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。

   [ ![証明書とシークレット。](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. クライアント シークレットの **[説明]** を入力します。 **[有効期限]** ドロップダウン メニューを選択して有効期限のタイム フレームを選択し、 **[追加]** をクリックします。

   [ ![クライアント シークレットの説明。](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. クライアント シークレット文字列が作成された後、その **[値]** と **[ID]** をコピーし、任意の安全な場所に格納します。

   [ ![クライアント シークレットの値 と ID。](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > クライアント シークレット文字列が Azure portal に表示されるのは 1 回だけです。 [Certificates & Secrets]\(クライアントとシークレット\) Web ページを離れてからそこに戻った場合、[値] 文字列はマスクされます。 クライアント シークレット文字列が生成された直後に、そのコピーを作成することが重要です。 クライアント シークレットのバックアップ コピーがない場合は、上記の手順を繰り返して再生成する必要があります。

## <a name="next-steps"></a>次のステップ

この記事では、Azure AD に機密クライアント アプリケーションを登録する方法の手順について説明しました。 また、DICOM 用の Azure API に API アクセス許可を追加する方法の手順も説明しました。 最後に、アプリケーション シークレットを作成する方法を示しました。 

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)



