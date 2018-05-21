---
title: Azure Multi-Factor Authentication - しくみ
description: Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 709fab070533984f94a72ff2136a8bc32fbe6ec6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication のしくみ
2 段階認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、多要素認証を侵害することはかなり困難です。 攻撃者にユーザーのパスワードを知られても、信頼できるデバイスを手に入れなければ役に立ちません。 

![追加のセキュリティ確認](./media/concept-mfa-howitworks/howitworks.png)

Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。  2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。


## <a name="methods-available-for-two-step-verification"></a>2 段階認証で使用できる方法
ユーザーがサインインしようとすると、追加の確認がユーザーに対して行われます。  この 2 番目の確認のために使用できる方法の一覧を次に示します。

| 確認方法 | [説明] |
| --- | --- |
| 音声通話 |ユーザーの登録済みの電話番号が呼び出されます。 ユーザーは、必要に応じて PIN を入力した後、# キーを押します。 |
| テキスト メッセージ |6 桁のコードを含むテキスト メッセージがユーザーの携帯電話に送信されます。 ユーザーは、サインイン ページでこのコードを入力します。 |
| モバイル アプリの通知 |確認要求がユーザーのスマート フォンに送信されます。 ユーザーは、必要に応じて PIN を入力し、モバイル アプリで **[確認]** を選択します。 |
| モバイル アプリの確認コード |ユーザーのスマート フォンで実行されているモバイル アプリには、30 秒ごとに変更される確認コードが表示されます。 ユーザーは、最新のコードを確認し、サインイン ページでそのコードを入力します。 |
| サード パーティの OATH トークン | サード パーティの確認方法を受け入れるように Azure Multi-Factor Authentication Server を構成できます。 |

Azure Multi-Factor Authentication では、クラウドとサーバーの両方で検証方法を選択できます。 ユーザーが使用できる方法 (電話、テキスト、アプリの通知、またはアプリのコード) を選択できます。 詳細については、「[選択可能な検証方法](howto-mfa-mfasettings.md#selectable-verification-methods)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [Azure Multi-Factor Authentication のさまざまなバージョンと使用方法](concept-mfa-licensing.md)を確認する

- Azure MFA を[クラウドとオンプレミス](concept-mfa-whichversion.md)のどちらにデプロイするかを選択します。

- [よく寄せられる質問](multi-factor-authentication-faq.md)の回答を確認します。