---
title: クイックスタート - Azure AD のセルフサービス パスワード リセット
description: このクイックスタートでは、ユーザーが自分のパスワードをリセットできるようにして IT 部門のオーバーヘッドを削減する、Azure AD のセルフサービス パスワード リセットを構成する方法を学習します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154858"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>クイック スタート:Azure Active Directory のセルフサービス パスワード リセットを構成する

このクイックスタートでは、Azure Active Directory (AD) のセルフサービス パスワード リセット (SSPR) を構成して、ユーザーが自分のパスワードをリセットしたり、自分のアカウントのロックを解除したりできるようにします。 SSPR を使用すると、ユーザーは、ヘルプデスクや管理者の支援なしで自分の資格情報をリセットできます。 この機能により、ユーザーは、追加のサポートを待たずに自分のアカウントへのアクセスを回復できます。

> [!IMPORTANT]
> このクイックスタートでは、管理者向けにセルフサービス パスワード リセットを有効にする方法を示します。 既にセルフサービス パスワード リセットの登録が済んでいて、自分のアカウントに戻る必要があるエンド ユーザーは、 https://aka.ms/sspr にアクセスしてください。
>
> ユーザーが自分でパスワードをリセットする機能が IT チームによって有効にされていない場合は、ヘルプデスクに連絡して追加のサポートを依頼してください。

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版ライセンスが有効になっている、動作している Azure AD テナント。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* グローバル管理者特権を持つアカウント。
* パスワードがわかっている非管理者テスト ユーザー (たとえば、*testuser*)。
    * ユーザーを作成する必要がある場合は、「[クイックスタート: Azure Active Directory に新しいユーザーを追加する](../add-users-azure-active-directory.md)」を参照してください。
* 非管理者テスト ユーザーがメンバーであることをテストするパイロット グループ (たとえば、*SSPR-Test-Group*)。
    * グループを作成する必要がある場合は、[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory-groups-create-azure-portal.md)方法を参照してください。

## <a name="enable-self-service-password-reset"></a>セルフサービス パスワード リセットを有効にする

[このプロセスを YouTube のビデオとして表示する](https://youtu.be/Pa0eyqjEjvQ)

1. Azure portal メニューまたは **[ホーム]** ページで、 **[Azure Active Directory]** を選択し、 **[パスワードのリセット]** を選択します。

1. **[プロパティ]** ページの **[パスワード リセットのセルフサービスが有効]** オプションの下で、 **[選択済み]** を選択します。
1. **[グループの選択]** を選択し、この記事の前提条件のセクションの一部として作成したパイロット グループ (たとえば、*SSPR-Test-Group*) を選択します。 準備ができたら、 **[保存]** を選択します。
1. **[認証方法]** ページで、次のように選択し、 **[保存]** を選択します。
    * リセットのために必要な方法の数: **1**
    * ユーザーが使用できる方法:
        * **モバイル アプリ コード**
        * **電子メール**

    > [!div class="mx-imgBorder"]
    > ![SSPR 用の認証方法を選択する][Authentication]

4. **[登録]** ページで、次のように選択し、 **[保存]** を選択します。
   * サインイン時にユーザーに登録を求める:**はい**
   * ユーザーが認証情報を再確認するように求められるまでの日数を設定する:**365**

## <a name="test-self-service-password-reset"></a>セルフサービス パスワード リセット をテストする

ここで、前のセクションで選択したグループの一部であるテスト ユーザー (たとえば、*testuser*) を使用して、SSPR 構成をテストします。 Microsoft は Azure 管理者アカウントに対して強力な認証要件を強制しているため、管理者アカウントを使用してテストすると結果が変わる可能性があります。 管理者のパスワード ポリシーの詳細については、[パスワード ポリシー](concept-sspr-policy.md)に関する記事を参照してください。

1. InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) に移動します。
2. 非管理者テスト ユーザー (たとえば、*testuser*) としてサインインし、認証用電話を登録します。
3. 完了したら、 **[問題ありません]** ボタンを選択し、ブラウザー ウィンドウを閉じます。
4. InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/sspr](https://aka.ms/sspr) に移動します。
5. 非管理者テスト ユーザーのユーザー ID (たとえば、*testuser*) と CAPTCHA の文字を入力し、 **[次へ]** を選択します。
6. 検証の手順に従って、パスワードをリセットします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

セルフサービス パスワード リセットを無効にするには、Azure portal で **[Azure Active Directory]** を検索して選択します。 **[パスワードのリセット]** を選択し、 **[パスワード リセットのセルフサービスが有効]** で **[なし]** を選択します。 準備ができたら、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウド限定ユーザーのためにセルフサービス パスワード リセットを構成する方法を学習しました。 より詳細なロール アウトを完了する方法については、ロール アウト ガイドに進んでください。

> [!div class="nextstepaction"]
> [セルフサービス パスワード リセット をロール アウトする](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure AD の使用できる認証方法と必要な数量"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
