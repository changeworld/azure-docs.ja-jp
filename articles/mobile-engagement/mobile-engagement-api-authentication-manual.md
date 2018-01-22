---
title: "Mobile Engagement REST API を使用した認証 - 手動の設定"
description: "Mobile Engagement REST API の認証を手動で設定する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Mobile Engagement REST API を使用した認証 - 手動の設定
これは、「[Mobile Engagement REST API での認証](mobile-engagement-api-authentication.md)」の付録のドキュメントです。 このドキュメントを先に読み、内容を把握してください。
このドキュメントでは、Azure Portal を使用して Mobile Engagement REST API の認証を設定するために 1 回限りの設定を行う代替の方法について説明します。

> [!NOTE]
> 以下の手順はこの [Active Directory ガイド](../azure-resource-manager/resource-group-create-service-principal-portal.md) に基づいており、Mobile Engagement API の認証に必要なものに対応してカスタマイズしています。 そのため、以下の手順を詳しく理解したい場合は、このガイドを参照してください。

1. [Azure Portal](https://portal.azure.com/) で Azure アカウントにログインします。
2. 左側のペインで **[Active Directory]** を選択します。

     ![Active Directory の選択][1]

3. ディレクトリ内のアプリケーションを表示するには、**[アプリの登録]** をクリックします。

     ![アプリケーションの表示][3]

4. **[新しいアプリケーションの登録]** をクリックします。

     ![[アプリケーションの追加]][4]

5. アプリケーションの名前を入力し、アプリケーションの種類を **[Web アプリ/API]** のままにして、[次へ] ボタンをクリックします。 **[サインオン URL]** にはダミーの URL を指定できます。サインオン URL はこのシナリオでは使用されず、URL 自体は検証されません。
6. 完了すると、指定した名前の Azure AD アプリが表示されます。 これは **AD\_APP\_NAME** です。書き留めておいてください。

     ![アプリケーション名][8]

7. アプリ名をクリックします。
8. **[アプリケーション ID]** を見つけて書き留めておきます。API 呼び出しの **CLIENT\_ID** として使用される CLIENT ID になります。

     ![アプリケーションの構成][10]

9. 右側の **[キー]** セクションを見つけます。

     ![アプリケーションの構成][11]

10. 新しいキーを作成し、すぐにコピーして保存し、後で使用できるようにします。 このキーは再表示されません。

     ![アプリケーションの構成][12]

    > [!IMPORTANT]
    > このキーは、指定した期間の最後に期限切れになるため、そのときには更新してください。そうしないと、API 認証が機能しなくなります。 このキーが侵害されていると思われる場合は、削除して、再作成することもできます。
    >
    >
11. ページの上部にある **[エンドポイント]** ボタンをクリックし、**[OAuth 2.0 トークン エンドポイント]** をコピーします。

    ![][14]

16. このエンドポイントは、次の形式になります。ここで、URL の GUID は **TENANT_ID** であるため、これを書き留めておきます。`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. ここで、このアプリケーションのアクセス許可の構成に進みます。 このために、 [Azure ポータル](https://portal.azure.com)を開く必要があります。 
18. **[リソース グループ]** をクリックし、**[Mobile Engagement]** リソース グループを見つけます。

    ![][15]

19. **[Mobile Engagement]** リソース グループをクリックして、ここの **[設定]** セクションに移動します。

    ![][16]

20. [設定] セクションの **[ユーザー]** をクリックし、**[追加]** をクリックして、ユーザーを追加します。

    ![][17]

21. **[ロールの選択]** をクリックします。

    ![][18]

22. **[所有者]** をクリックします。

    ![][19]

23. 検索ボックスで、アプリケーションの名前 **AD\_APP\_NAME** を検索します。 既定では、これは表示されません。 見つかったら、それを選択し、セクションの下部の **[選択]** をクリックします。

    ![][20]

24. **[アクセスを追加]** セクションで、それが **1 ユーザー、0 グループ**と表示されます。 このセクションの **[OK]** をクリックして変更を確認します。

    ![][21]

これで必要な Azure AD 構成が完了し、API を呼び出すためにすべて設定されました。

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



