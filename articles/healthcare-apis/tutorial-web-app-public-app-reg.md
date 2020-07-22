---
title: Web アプリ チュートリアル - クライアント アプリケーションのセットアップ
description: このチュートリアルでは、Web アプリケーションのデプロイを準備するためにパブリック アプリケーションを登録する手順について説明します
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870455"
---
# <a name="client-application-registration"></a>クライアント アプリケーションの登録
前のチュートリアルでは、Azure API for FHIR をデプロイしてセットアップしました。 これで、Azure API for FHIR のセットアップが完了したので、パブリック クライアント アプリケーションを登録します。 詳細またはトラブルシューティングについては、[パブリック クライアント アプリの登録](register-public-azure-ad-client-app.md)に関する完全な攻略ガイドで読むことができますが、主な手順については、このチュートリアルの以下の部分で紹介しています。

1. Azure Active Directory に移動します
1. **[アプリの登録]**  -->  **[新しい登録]** の順に選択します
1. アプリケーションの名前を設定し、リダイレクト URI を https://www.getpostman.com/oauth2/callback にセットアップします


![クライアント アプリケーションの登録](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>クライアント アプリケーションの設定
クライアント アプリケーションが登録されたら、[概要] ページからアプリケーション (クライアント) ID をコピーします。 この値は、後でクライアントにアクセスするときに必要になります。

![アプリ ID をコピーする](media/tutorial-web-app/app-id.png)

次に、正しい認証オプションを設定します。 左側で **[認証]** を選択します。 **[アクセス トークン]** と **[ID トークン]** チェック ボックスをオンにします。 このチュートリアルの 4 番目の部分で Web アプリケーションを作成するときの準備として、リダイレクト URI をセットアップすることもできます。 そうするには、リダイレクト URI の一覧に https://\<WEB-APP-NAME>.azurewebsites.net を追加します。 [Web アプリを記述する](tutorial-web-app-write-web-app.md)手順で別の名前を選択する場合は、ここに戻って更新する必要があります。

![アプリの認証設定](media/tutorial-web-app/app-authentication.png)

これで認証を正しくセットアップできたので、API のアクセス許可を設定します。 
1. **[API のアクセス許可]** を選択し、 **[アクセス許可の追加]** をクリックします
1. **[所属する組織で使用している API]** で、Azure Healthcare APIs (Azure 医療 API) を検索します
1. **user_impersonation** を選択し、 **[アクセス許可の追加]** をクリックします

## <a name="next-steps"></a>次の手順
これで、パブリック クライアント アプリケーションをセットアップできました。 次のチュートリアルでは、Postman を通じてこのアプリケーションをテストし、アクセスできるようにする手順を説明します。

>[!div class="nextstepaction"]
>[Postman でクライアント アプリケーションをテストする](tutorial-web-app-test-postman.md)
