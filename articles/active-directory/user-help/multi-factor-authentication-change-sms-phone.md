---
title: テキスト メッセージ 2 段階認証の使用に関する一般的な問題 - Azure Active Directory | Microsoft Docs
description: 別のモバイル デバイスを 2 要素認証方法として設定する方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781246"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>テキスト メッセージ 2 段階認証に関する一般的な問題

テキスト メッセージで確認コードを受信する方法は、2 段階認証の一般的な検証方法です。 コードを受け取ることを予期していなかった場合、または間違った電話でコードを受け取った場合は、次の手順を使用してこの問題を解決します。  

> [!Note]
> ご自身の組織で確認用テキスト メッセージの受信が許可されていない場合は、別の方法を選択するか、担当の管理者にお問い合わせください。

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>間違った電話でコードを受け取った場合

1. ご自身のセキュリティ情報を管理するための **[マイ セキュリティ情報]** にサインインします。

1. **[セキュリティ情報]** ページ上で、変更する電話番号を、登録済み認証方法の一覧から選択し、 **[変更]** を選択します。

1. 新しい番号の国または地域を選択し、お使いのモバイル デバイスの電話番号を入力します。

1. **[Text me a code to receive text messages for verification]\(確認のためにコードをテキストで送信して、そのテキスト メッセージを受信する\)** を選択し、 **[次へ]** を選択します。

1. メッセージが表示されたら、Microsoft から送信されたテキスト メッセージ内の確認コードを入力し、 **[次へ]** を選択します。

1. 電話が正常に登録されたことが通知されたら、 **[完了]** を選択します。

## <a name="if-you-receive-a-code-unexpectedly"></a>予期しないコードを受信した場合

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>2 段階認証用の電話番号を既に登録している場合

予期しないテキスト メッセージを受信した場合、他のユーザーがあなたのパスワードを知っていて、お使いのアカウントを引き継ごうとしている可能性があります。 直ちにパスワードを変更し、何が起こったかを組織の管理者に知らせます。

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>2 段階認証用の電話番号を登録していない場合

テキスト メッセージの本文内で `STOP` を指定して、テキスト メッセージに返信できます。 このメッセージにより、プロバイダーは、あなたの電話番号に今後メッセージを送信できなくなります。 さまざまなコードが含まれる同様のメッセージに対して、返信しなければならないことがあります。  

ただし、既に 2 段階認証を使用している場合、このメッセージを送信すると、この電話番号を使用してサインインできなくなります。 テキスト メッセージの受信を再開するには、最初のテキスト メッセージの本文内で `START` を指定して、そのメッセージに返信します。

## <a name="next-steps"></a>次のステップ

- [2 段階認証のサポートを受ける](multi-factor-authentication-end-user-troubleshoot.md)
- [携帯電話を 2 段階認証方法として設定する](multi-factor-authentication-setup-phone-number.md)