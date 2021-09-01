---
title: Azure Active Directory にパブリック クライアント アプリケーションを登録する - DICOM 用 Azure Healthcare APIs
description: この記事では、パブリック アプリケーションを Azure Active Directory に登録する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779486"
---
# <a name="register-a-public-client-application"></a>パブリック クライアント アプリケーションを登録する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、パブリック アプリケーションを Azure Active Directory (Azure AD) に登録する方法について説明します。

クライアント アプリケーションの登録とは、ユーザーに代わって認証を行い、API のアクセス許可を要求することができるアプリケーションの Azure AD の表現です。 パブリック クライアントとは、シークレットの機密性を保持できないモバイル アプリケーションや単一ページの JavaScript アプリケーションなどのアプリケーションです。 この手順は [Confidential クライアント アプリケーションの登録](dicom-register-confidential-client-application.md)と似ていますが、パブリック クライアントがアプリケーションのシークレットを保持することは信頼できないため、追加する必要はありません。

このクイックスタート ガイドからは、[Microsoft ID プラットフォームにアプリケーションを登録する](.././../active-directory/develop/quickstart-register-app.md)方法に関する全般情報が提供されます。

## <a name="application-registrations-in-the-azure-portal"></a>Azure Portal のアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** を選択します。
3. **[新規登録]** を選択します。

   [ ![Azure portal での DICOM アプリケーションの登録。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>アプリケーションの登録の概要

1. DICOM サービス アプリケーションにユーザー向けの表示名を指定します。

   [ ![Azure の [アプリケーションの登録]。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. **[サポートされているアカウントの種類]** では、アプリケーションを使用できる、または API にアクセスできるユーザーを選択します。
3. (**オプション**) **[リダイレクト URI]** を指定します。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、入力します。
4. **[登録]** を選択します。

## <a name="configuring-an-application-as-a-public-application"></a>アプリケーションをパブリック アプリケーションとして構成する

[デスクトップ](.././../active-directory/develop/scenario-desktop-app-registration.md)、[モバイル](.././../active-directory/develop/scenario-mobile-app-registration.md)、または[シングルページ](.././../active-directory/develop/scenario-mobile-app-registration.md) アプリケーションをパブリック アプリケーションとして構成するには、次の手順を参照してください。

1. [Azure portal](https://portal.azure.com) で、 **[アプリの登録]** ブレードを選択し、 **[認証]** ブレードを選択します。

   [ ![Azure パブリック アプリケーションの登録。](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. **[詳細設定]** で、 **[はい]** を選択します。

   シングルページ アプリケーションの場合、 **[アクセス トークン]** と **[ID トークン]** を選択して暗黙のフローを有効にします。
   * アプリケーションでユーザーがサインインする場合は、 **[ID トークン]** を選択します。
   * アプリケーションでも保護された Web API を呼び出す必要がある場合は、 **[アクセス トークン]** を選択します。

3. **[保存]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

[Confidential クライアント アプリケーション](dicom-register-confidential-client-application.md)と同様に、このアプリケーションがユーザーに代わって要求しなければならない API アクセス許可を選択する必要があります。

DICOM サービスを使用する場合は、[所属する組織で使用している API] で Azure Healthcare APIs を検索し、DICOM 用の Azure API へのアクセス許可を追加します。 これは、Microsoft.HealthcareAPIs リソース プロバイダーを既に登録している場合にのみ見つけることができます。

[ ![自分の API のアクセス許可。](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

DICOM サービス アプリケーションから要求できるようにするアクセス許可を選択します。

[ ![API アクセス許可の要求。](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>次のステップ

この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。  

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)










