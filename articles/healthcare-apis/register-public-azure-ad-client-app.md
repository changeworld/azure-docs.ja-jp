---
title: Azure AD にパブリック クライアント アプリを登録する - Azure API for FHIR
description: この記事では、Azure での FHIR API のデプロイの準備として、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871002"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Azure Active Directory にパブリック クライアント アプリケーションを登録する

この記事では、パブリック アプリケーションを Azure Active Directory に登録する方法について説明します。  

クライアント アプリケーションの登録とは、ユーザーに代わって認証を行い、API のアクセス許可を要求することができるアプリケーションの Azure Active Directory の表現です。 パブリック クライアントとは、シークレットの機密性を保持できないモバイル アプリケーションや単一ページの JavaScript アプリケーションなどのアプリケーションです。 この手順は [Confidential クライアントの登録](register-confidential-azure-ad-client-app.md)と似ていますが、パブリック クライアントがアプリケーションのシークレットを保持することは信頼できないため、追加する必要はありません。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="application-registration-overview"></a>アプリケーションの登録の概要

1. アプリケーションに表示名を与えます。

2. 応答 URL を入力してください。 応答 URL は、認証コードがクライアント アプリケーションに返される場所です。 後で応答 URL を追加し、既存のものを編集することができます。

    ![Azure portal。 新しいパブリック アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API のアクセス許可

[Confidential クライアント アプリケーション](register-confidential-azure-ad-client-app.md)と同様に、このアプリケーションがユーザーに代わって要求できる API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を開きます。

    Azure API for FHIR を使用している場合は、 **[所属する組織で使用している API]** で Azure Healthcare API を検索し、Azure Healthcare API へのアクセス許可を追加します (下の画像)。
    
    他のリソース アプリケーションを参照している場合は、 **[自分の API][ で作成済みの ](register-resource-azure-ad-client-app.md)FHIR API リソース アプリケーションの登録**を選択します。

    ![Azure portal。 新しいパブリック API のアクセス許可 - Azure API for FHIR の既定値](media/public-client-app/api-permissions.png)


2. アプリケーションから要求できるようにするアクセス許可を選択します。![Azure portal。 アプリのアクセス許可](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR サーバーのオーソリティを検証する
この記事で登録したアプリケーションと FHIR サーバーが同じ Azure AD テナントにある場合、次の手順に進むことができます。

クライアント アプリケーションを、FHIR サーバーとは別の Azure AD テナントで構成する場合は、**オーソリティ**を更新する必要があります。 Azure API for FHIR の [設定] --> [認証] でオーソリティを設定してください。 オーソリティを、 **https://login.microsoftonline.com/\<TENANT-ID>** に設定します。

## <a name="next-steps"></a>次のステップ

この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Postman を使用して FHIR サーバーへのアクセスをテストします。
 
>[!div class="nextstepaction"]
>[Postman を使用して Azure API for FHIR にアクセスする](access-fhir-postman-tutorial.md)
