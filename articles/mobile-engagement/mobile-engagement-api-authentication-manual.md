---
title: "Mobile Engagement REST API を使用した認証: 手動の設定"
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
ms.openlocfilehash: 0b4a999c6778040e71f862d3a010b6635e84b26e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis-manual-setup"></a>Mobile Engagement REST API を使用した認証: 手動の設定
このドキュメントは、「[Mobile Engagement REST API での認証](mobile-engagement-api-authentication.md)」の付録です。 コンテキストを理解するために、まずこの記事をお読みください。 Azure Portal を使用して、Mobile Engagement REST API のワンタイム認証を設定する別の方法についても説明しています。

> [!NOTE]
> 以下の手順は、[こちらの Active Directory ガイド](../azure-resource-manager/resource-group-create-service-principal-portal.md)に基づいています。 これらの手順は Mobile Engagement API 用の認証要件のためにカスタマイズされています。 以下の手順を詳しく理解したい場合、これらの要件を参照してください。

1. [Azure Portal](https://portal.azure.com/) で Azure アカウントにサインインします。
2. 左側のペインで **[Active Directory]** を選択します。

   ![Active Directory の選択][1]

3. ディレクトリ内でアプリケーションを表示するには、**[アプリの登録]** を選択します。

   ![アプリケーションの表示][3]

4. **[新しいアプリケーションの登録]** を選択します。

   ![[アプリケーションの追加]][4]

5. アプリケーションの名前を入力します。 アプリケーションの種類を **[Web アプリ/API]** のままにして、**[次へ]** ボタンを選択します。 **[サインオン URL]** に任意のダミー URL を指定できます。 このシナリオではこれらを使用せず、URL 自体は検証されません。

   完了すると、指定した名前の Azure Active Directory (Azure AD) アプリを使用できるようになります。 これは **AD\_APP\_NAME** なので、メモしておきます。

   ![アプリの名前][8]

7. アプリの名前を選択します。

8. **[アプリケーション ID]** を検索し、その値をメモしておきます。 これは、API 呼び出しの **CLIENT\_ID** として使用されるクライアント ID です。

   ![アプリケーション ID の検索][10]

9. 右側の **[キー]** セクションを見つけます。

   ![[キー] セクション][11]

10. 新しいキーを作成し、すぐにコピーします。 これは再表示されません。

    ![キー ウィンドウとキーの詳細][12]

    > [!IMPORTANT]
    > このキーは、指定した期間の終わりに有効期限が切れます。 時期が来たら更新してください。そうしないと、API 認証が機能しなくなります。 このキーが侵害されている懸念がある場合は、キーを削除して再作成できます。
    >
    
11. ページの上部にある **[エンドポイント]** ボタンを選択します。 **[OAuth 2.0 トークン エンドポイント]** をコピーします。

    ![エンドポイントのコピー][14]

16. このエンドポイントは次の形式になります。URL の GUID は **TENANT_ID** を表します。`https://login.microsoftonline.com/<GUID>/oauth2/token`

17. 次に、このアプリのアクセス許可を構成します。 プロセスを開始するには、[Azure Portal](https://portal.azure.com) にアクセスします。

18. **[リソース グループ]** を選択し、**[MobileEngagement]** リソース グループを探します。

    ![MobileEngagement を探す][15]

19. **[MobileEngagement]** リソース グループを選択し、**[すべての設定]** を選択します。

    ![MobileEngagement の設定に移動する][16]

20. **[設定]** セクションで **[ユーザー]** を選択します。 ユーザーを追加するには、**[追加]** を選択します。

    ![ユーザーの追加][17]

21. **[ロールの選択]** をクリックします。

    ![ロールを選択する][18]

22. **[所有者]** を選択します。

    ![ロールとして [所有者] を選択する][19]

23. 検索ボックスで、アプリケーションの名前 **AD\_APP\_NAME** を検索します。 この名前は既定ではここにありません。 検索したら、それを選択します。 次に、セクションの下部にある **[選択]** をクリックします。

    ![名前を選択する][20]

24. **[アクセスを追加]** セクションで、これが **1 ユーザー、0 グループ**と表示されます。 変更を確定するには、**[OK]** を選択します。

    ![追加されたユーザーを確定する][21]

これで必要な Azure AD 構成が完了し、API を呼び出す準備が完了しました。

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



