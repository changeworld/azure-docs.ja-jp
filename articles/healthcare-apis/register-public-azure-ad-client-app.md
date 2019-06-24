---
title: Azure Active Directory でパブリック クライアント アプリケーションを登録する - Azure API for FHIR
description: この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709603"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Azure Active Directory にパブリック クライアント アプリケーションを登録する

この記事では、パブリック アプリケーションを Azure Active Directory に登録する方法について説明します。 クライアント アプリケーションの登録とは、ユーザーに代わって認証を行い、API のアクセス許可を要求することができるアプリケーションの Azure Active Directory の表現です。 パブリック クライアントとは、シークレットの機密性を保持できないモバイル アプリケーションや単一ページの JavaScript アプリケーションなどのアプリケーションです。 この手順は [Confidential クライアントの登録](register-confidential-azure-ad-client-app.md)と似ていますが、パブリック クライアントがアプリケーションのシークレットを保持することは信頼できないため、追加する必要はありません。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録 (プレビュー)]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="application-registration-overview"></a>アプリケーションの登録の概要

1. アプリケーションに表示名を与えます。

2. 応答 URL を入力してください。 応答 URL は、認証コードがクライアント アプリケーションに返される場所です。 後で応答 URL を追加し、既存のものを編集することができます。

    ![Azure portal。 新しいパブリック アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API のアクセス許可

[Confidential クライアント アプリケーション](register-confidential-azure-ad-client-app.md)と同様に、このアプリケーションがユーザーに代わって要求できる API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を開き、[[FHIR API Resource Application Registration]\(FHIR API リソース アプリケーションの登録\)](register-resource-azure-ad-client-app.md) を選択します。

    ![Azure portal。 新しいパブリック API のアクセス許可。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. アプリケーションから要求できるようにするスコープを選択します。

## <a name="next-steps"></a>次の手順

この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-oss-powershell-quickstart.md)
