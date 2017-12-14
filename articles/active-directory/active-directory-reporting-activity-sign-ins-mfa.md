---
title: "Azure Portal での多要素認証レポートのリファレンス | Microsoft Docs"
description: "Azure Portal の多要素認証レポートの参照情報"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 8e4fc5c9eb031beef42b52c771ef88ea80dba068
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Azure Portal の多要素認証レポートのリファレンス

環境の動作状況を判断するために必要な情報は、[Azure Portal](https://portal.azure.com) の [Azure Active Directory (Azure AD) レポート](active-directory-reporting-azure-portal.md)で入手できます。

[サインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)を見ると、多要素認証 (MFA) の使用状況を含め、管理対象アプリケーションの使用状況とユーザーのサインイン アクティビティに関する情報を把握できます。 

また、MFA データから、組織内の MFA の動作状況を洞察することができます。 たとえば、次のような疑問の答えがわかります。 

- MFA を使用したサインイン チャレンジが実行されたかどうか。 

- ユーザーが MFA を完了した方法。 

- ユーザーが MFA を完了できなかった理由。  

すべてのサインイン データを集約することで、組織内の MFA エクスペリエンスの理解を深めることができます。 たとえば、データから次のような疑問の答えがわかります。 

- MFA チャレンジを受けているユーザー数。  

- MFA チャレンジを完了できないユーザー数。 

- エンド ユーザーが経験している一般的な MFA の問題。 


このデータは、Azure Portal と[レポート API](active-directory-reporting-api-getting-started-azure-portal.md) で入手できます。 


## <a name="data-structure"></a>データ構造


MFA のサインイン アクティビティ レポートから、次の情報にアクセスできます。

**MFA の必要性**: サインインに MFA が必要かどうか。 ユーザーごとの MFA、条件付きアクセス、または他の理由で、MFA を必須にすることができます。 指定できる値は `Yes` または `No` です。

**MFA の認証方法:** ユーザーが MFA の完了に使用した認証方法。 次のいずれかの値になります。 

- テキスト メッセージ 

- モバイル アプリの通知 

- 電話の呼び出し (認証用電話) 

- モバイル アプリの確認コード 

- 電話の呼び出し (会社の電話) 

- 電話の呼び出し (代替の認証用電話) 

**MFA 認証の詳細:** スクラブ バージョンの電話番号 (例: +X XXXXXXXX64)。 

**MFA の結果:** MFA が満たされたか拒否されたかに関する詳細情報。

- MFA が満たされた場合は、MFA がどのように満たされたかに関する情報がこの列に表示されます。 

- MFA が拒否された場合は、この列に拒否の理由が表示されます。 指定できる値は `Satisfied` または `Denied` です。 

次のセクションでは、MFA の結果フィールドに使用できる文字列値を示します。

## <a name="status-strings"></a>状態文字列

ここでは、MFA の結果状態文字列に使用できる値を示します。

### <a name="satisfied-status-strings"></a>Satisfied の状態文字列


- Azure Multi-Factor Authentication

    - completed in the cloud (クラウドで完了しました) 

    - has expired due to the policies configured on tenant (テナントに構成されているポリシーのため期限が切れました) 

    - registration prompted (登録がプロンプトされました) 

    - satisfied by claim in the token (トークンの要求によって満たされました) 

    - satisfied by claim in the token (トークンの要求によって満たされました) 

    - satisfied by claim in the token (トークンの要求によって満たされました) 

    - satisfied by claim in the token (トークンの要求によって満たされました) 

    - satisfied by claim provided by external provider (外部プロバイダーから送信された要求によって満たされました) 

    - satisfied by strong authentication (強力な認証によって満たされました) 

    - skipped as flow exercised was Windows broker logon flow (実行されたフローが Windows ブローカー ログオン フローのためスキップされました) 

    - skipped as flow exercised was Windows broker logon flow (実行されたフローが Windows ブローカー ログオン フローのためスキップされました) 

    - skipped due to app password (アプリ パスワードによりスキップされました) 

    - skipped due to location (場所によりスキップされました) 

    - skipped due to registered device (登録済みデバイスによりスキップされました) 
    
    - skipped due to remembered device (記憶済みデバイスによりスキップされました) 

    - successfully completed (正常に完了しました) 

- Redirected to external provider for multi-factor authentication (多要素認証のために外部プロバイダーにリダイレクトされました) 

 
### <a name="denied-status-strings"></a>Denied の状態文字列

Azure Multi-Factor Authentication denied; (Azure Multi-Factor Authentication は拒否されました) 

- authentication in-progress (認証が進行中) 

- duplicate authentication attempt (重複する認証試行) 

- entered incorrect code too many times (不適切なコードが何度も入力されました) 

- invalid authentication (無効な認証) 

- invalid mobile app verification code (無効なモバイル アプリ確認コード) 

- misconfiguration (構成の誤り) 

- phone call went to voicemail (ボイスメールに対する電話の呼び出し) 

- phone number has an invalid format (電話番号の形式が正しくありません) 

- service error (サービス エラー) 

- unable to reach the user’s phone (ユーザーの電話にアクセスできません) 

- unable to send the mobile app notification to the device (モバイル アプリ通知をデバイスに送信できません) 

- unable to send the mobile app notification (モバイル アプリ通知を送信できません) 

- user declined the authentication (ユーザーが認証を拒否しました) 

- user did not respond to mobile app notification (ユーザーがモバイル アプリ通知に応答しませんでした) 

- user does not have any verification methods registered (ユーザーが確認方法を登録していません) 

- user entered incorrect code (ユーザーが不適切なコードを入力しました) 

- user entered incorrect PIN (ユーザーが不適切な PIN を入力しました) 

- user hung up the phone call without succeeding the authentication (ユーザーが認証に成功しないまま電話を切りました) 

- user is blocked (ユーザーはブロックされています) 

- user never entered the verification code (ユーザーは確認コードを入力しませんでした) 

- user not found (ユーザーが見つかりません) 
 
- verification code already used once (確認コードは既に使用されています) 



## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Active Directory レポート](active-directory-reporting-azure-portal.md)」をご覧ください。




























