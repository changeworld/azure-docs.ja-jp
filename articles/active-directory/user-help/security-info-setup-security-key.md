---
title: セキュリティ キーを使用するようにセキュリティ情報を設定する (プレビュー) - Azure Active Directory | Microsoft Docs
description: Fast Identity Online (FIDO2) セキュリティ キーを使用して本人確認をするようにセキュリティ情報を設定する方法です。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386728"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>セキュリティ キーを使用するようにセキュリティ情報を設定する (プレビュー)

セキュリティ キーは、組織内でパスワードなしのサインイン方法として使用できます。 セキュリティ キーは、職場または学校アカウントにサインインするために一意の PIN と共に使用される物理デバイスです。 セキュリティ キーを使用するには、物理デバイスと自分だけが知る情報が必要となります。このため、ユーザー名とパスワードよりも強力な認証方法と見なされています。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、Azure Active Directory (Azure AD) 環境の設定と管理方法の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory)」を参照してください。

## <a name="what-is-a-security-key"></a>セキュリティ キーとは

現在、[Fast Identity Online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) というパスワードなしの認証方法を使用するセキュリティ キーの設計とプロバイダーが複数サポートされています。 この方法では、職場または学校アカウントに一度サインインするだけで、組織のすべてのクラウドベース リソースとサポート対象ブラウザーにアクセスできます。

職場または学校アカウントにセキュリティ キーが必要な場合は、管理者または組織から提供されます。 使用できるセキュリティ キーには、デバイスに差し込む USB キーや NFC リーダーでタップする NFC キーなど、さまざまな種類があります。 ご使用のセキュリティ キーの詳細 (種類など) については、製造元のドキュメントを参照してください。

> [!Note]
> FIDO2 セキュリティ キーを使用できない場合は、他のパスワードなしの認証方法として、Microsoft Authenticator アプリや Windows Hello などを使用できます。 Microsoft Authenticator アプリの詳細については、「[Microsoft Authenticator アプリとは](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)」を参照してください Windows Hello の詳細については、[Windows Hello の概要](https://www.microsoft.com/windows/windows-hello)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

セキュリティ キーの登録を開始する前に、次の点を再度確認してください。

- 管理者がこの機能を組織内で使用できる状態にしている。

- ご使用のデバイスが、Windows 10 バージョン 1903 以降を実行し、Microsoft Edge ブラウザーを使用している。

- 管理者または組織から物理的なセキュリティ キーを受け取っている。 このセキュリティ キーは FIDO2 と Microsoft の両方に準拠している必要があります。 セキュリティ キーやその互換性について不明な点がある場合は、所属組織のヘルプ デスクにお問い合わせください。

## <a name="register-your-security-key"></a>セキュリティ キーの登録

セキュリティ キーを使用して職場または学校アカウントにサインインするには、セキュリティ キーが Windows および一意の PIN と連動するように準備する必要があります。

1. https://myprofile.microsoft.com で **[マイ プロファイル]** ページにアクセスし、まだサインインしていない場合はサインインします。

2. **[セキュリティ情報]** 、 **[方法の追加]** の順に選択し、 **[方法を追加します]** ドロップダウン リストで **[セキュリティ キー]** を選択します。

    ![セキュリティ キーが選択された [方法の追加] ボックス](media/security-info/security-info-security-key-add-method.png)

3. **[追加]** を選択し、所有しているセキュリティ キーの種類 ( **[USB デバイス]** または **[NFC デバイス]** ) を選択します。

    ![所有しているセキュリティ キーの種類として USB または NFC を選択](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 所有しているセキュリティ キーの種類がわからない場合は、製造元のドキュメントを参照してください。 製造元が不明な場合は、所属組織のヘルプ デスクにお問い合わせください。

4. セキュリティ キーを手元に準備し、 **[セキュリティ キー]** ボックスで **[次へ]** を選択します。

    ![セキュリティ キーの登録を開始するボックス](media/security-info/security-info-security-key-start-setup.png)

    新しいサインイン方法を設定するための新たなボックスが表示されます。

5. **[新しいサインイン方法の設定]** ボックスで **[次へ]** を選択し、次の操作を行います。

    - セキュリティ キーが USB デバイスの場合は、セキュリティ キーをデバイスの USB ポートに挿入します。

    - セキュリティ キーが NFC デバイスの場合は、リーダーに対してセキュリティ キーをタップします。

6. **[Windows セキュリティ]** ボックスに一意のセキュリティ キー PIN を入力し、 **[OK]** を選択します。

    **[新しいサインイン方法の設定]** ボックスに戻ります。

7. **[次へ]** を選択します。

8. **[セキュリティ情報]** ページに戻り、新しいセキュリティ キーに対して後で識別するための名前を入力し、 **[次へ]** を選択します。

    ![[セキュリティ情報] ページでセキュリティ キーに名前を付ける](media/security-info/security-info-security-key-name.png)

    セキュリティ キーが登録され、職場または学校アカウントへのサインインに使用できるようになります。

9. **[完了]** を選択して **[セキュリティ キー]** ボックスを閉じます。

    お使いのセキュリティ キー情報で **[セキュリティ情報]** ページが更新されます。

    ![登録した方法がすべて表示されている [セキュリティ情報] ページ](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>セキュリティ情報からのセキュリティ キーの削除

不要となったセキュリティ キーは、セキュリティ情報から削除できます。 これにより職場または学校アカウントでセキュリティ キーが使用されなくなりますが、セキュリティ キーには自分のデータと資格情報が格納されたままとなります。 セキュリティ キー自体から自分のデータと資格情報を削除するには、この記事の [Microsoft と互換性のあるセキュリティ キーのリセット](#reset-your-security-key)に関するセクションの手順に従う必要があります。

1. 削除するセキュリティ キーの **[削除]** リンクを選択します。

2. **[Delete security key]\(セキュリティ キーの削除\)** ボックスで **[OK]** を選択します。

    セキュリティ キーが削除され、職場または学校アカウントへのサインインに使用できなくなります。

>[!Important]
>このセキュリティ キーを誤って削除した場合は、この記事の[セキュリティ キーの登録方法](#register-your-security-key)に関するセクションの手順に従って、もう一度登録する必要があります。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Windows 設定からのセキュリティ キー設定の管理

**Windows 設定**アプリからセキュリティ キーの設定を管理することができます (セキュリティ キーのリセットやセキュリティ キー PIN の新規作成など)。

### <a name="reset-your-security-key"></a>セキュリティ キーのリセット

物理的なセキュリティ キーに格納されているアカウント情報をすべて削除する場合は、キーを出荷時の既定値に戻す必要があります。 セキュリティ キーをリセットすると、キーからすべてのデータが削除されて、最初からやり直すことができます。

>[!IMPORTANT]
>セキュリティ キーをリセットすると、キーからすべてのデータが削除されて出荷時の既定値にリセットされます。
>
> **すべてのデータと資格情報が消去されます。**

#### <a name="to-reset-your-security-key"></a>セキュリティ キーをリセットするには

1. Windows 設定アプリを開き、 **[アカウント]** 、 **[サインイン オプション]** 、 **[セキュリティ キー]** 、 **[管理]** の順に選択します。

2. セキュリティ キーを USB ポートに挿入するか、NFC リーダーをタップして本人確認を行います。

3. ご使用のセキュリティ キーの製造元に応じて、画面の指示に従います。 ご使用のキーの製造元が画面の指示に表示されない場合は、製造元のサイトで詳細情報を確認してください。

4. **[閉じる]** を選択して **[管理]** 画面を閉じます。

### <a name="create-a-new-security-key-pin"></a>新しいセキュリティ キー PIN の作成

セキュリティ キーに新しいセキュリティ キー PIN を作成することができます。

#### <a name="to-create-a-new-security-key-pin"></a>新しいセキュリティ キー PIN を作成するには

1. Windows 設定アプリを開き、 **[アカウント]** 、 **[サインイン オプション]** 、 **[セキュリティ キー]** 、 **[管理]** の順に選択します。

2. セキュリティ キーを USB ポートに挿入するか、NFC リーダーをタップして本人確認を行います。
3. **[Security Key PIN]\(セキュリティ キー PIN\)** 領域で **[追加]** を選択し、新しいセキュリティ キー PIN を入力して確認してから **[OK]** を選択します。

    セキュリティ キーが新しいセキュリティ キー PIN で更新されて、職場または学校アカウントで使用できるようになります。 PIN を再度変更する場合は、 **[変更]** ボタンを選択します。
4. **[閉じる]** を選択して **[管理]** 画面を閉じます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[Set up security info to use an authenticator app](security-info-setup-auth-app.md)」(認証アプリを使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。

## <a name="next-steps"></a>次の手順

- ブログ「[Microsoft の Azure AD が FIDO2 セキュリティ キーの公開プレビューを開始、パスワードなしのログインが可能に](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)」や、「[Microsoft Authenticator アプリとは](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview)」と [Windows Hello の概要](https://www.microsoft.com/windows/windows-hello)に関する記事を読んで、パスワードなしの認証方法について詳しく確認する。

- [Microsoft と互換性のあるセキュリティ キー](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)に関する詳細情報を確認する。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。
