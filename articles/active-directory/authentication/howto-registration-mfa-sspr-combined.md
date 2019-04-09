---
title: Azure AD SSPR と MFA のための統合された登録の使用を開始する (プレビュー)
description: Azure AD Multi-Factor Authentication とセルフサービスのパスワード リセットの統合された登録の有効化 (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a6896e2b9633b8de679e8d14a7957dc0e3229e7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226727"
---
# <a name="enable-combined-security-information-registration-preview"></a>統合されたセキュリティ情報の登録の有効化 (プレビュー)

この新しいエクスペリエンスを有効にする前に、記事「[統合されたセキュリティ情報の登録 (プレビュー)](concept-registration-mfa-sspr-combined.md)」を参照し、機能性とこの機能の影響を必ず理解してください。

![結合されたセキュリティ情報の登録は、サインインでより詳細な情報を要求するように、エクスペリエンスが強化されました。 例では、最も重要な方法として Microsoft Authenticator アプリの登録を示しています。](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure Multi-Factor Authentication と Azure AD パスワード リセットのセルフサービスのための統合されたセキュリティ情報の登録は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="enable-combined-registration"></a>統合された登録の有効化

統合された登録を有効にするには、次の手順を実行します。

1. Azure portal にユーザー管理者または全体管理者としてサインインします。
2. **Azure Active Directory** > **[ユーザー設定]** > **[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます – 更新]** で、**[選択済み]** のユーザーのグループまたは **[すべて]** のユーザーのうちのどちらを有効にするかを選択します。

![Azure AD ポータルですべてのユーザーに対して統合されたセキュリティ情報のプレビュー エクスペリエンスを有効にする](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 2019 年 3 月以降、無料/試用版の Azure AD テナントの MFA および SSPR ユーザーは、音声通話オプションを利用できなくなります。 この変更は、SMS メッセージには影響しません。 有料の Azure AD テナントのユーザーは、引き続き音声通話を利用できます。 この変更は、無料/試用版の Azure AD テナントのみに影響します。

> [!NOTE]
> 統合された登録を有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、MFA と SSPR ポリシーでこれらの方法が有効な場合、これらを MFA と SSPR に使用できます。 このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (`https:/aka.ms/ssprsetup`) にアクセスするユーザーは、ページにアクセスする前に多要素認証を実行する必要があります。

Internet Explorer でサイトとゾーンの割り当て一覧を構成している場合、次のサイトが同じゾーン内に存在する必要があります。

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>次の手順

[MFA と SSPR に使用できる方法](concept-authentication-methods.md)

[セルフサービスのパスワード リセットを構成する](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication を構成する](howto-mfa-getstarted.md)

[統合されたセキュリティ情報の登録のトラブルシューティング](howto-registration-mfa-sspr-combined-troubleshoot.md)