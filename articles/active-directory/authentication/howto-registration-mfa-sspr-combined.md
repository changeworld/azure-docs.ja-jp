---
title: 統合された登録の使用を開始する - Azure Active Directory
description: Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの統合された登録の有効化
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639699"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory での統合されたセキュリティ情報の登録の有効化

統合された登録の前、ユーザーは Azure Multi-Factor Authentication (MFA) とセルフサービス パスワード リセット (SSPR) の認証方法を別々に登録しました。 ユーザーは Multi-Factor Authentication と SSPR に同様の方法が使用されることに困惑しましたが、どちらの機能も登録する必要がありました。 現在では、統合された登録を使用することで、ユーザーは 1 回登録して Multi-Factor Authentication と SSPR の両方の利点を得ることができます。

この新しいエクスペリエンスを有効にする前に、[統合されたセキュリティ情報の登録](concept-registration-mfa-sspr-combined.md)に関する記事を確認し、この機能の効果と影響を確実に理解してください。

![統合されたセキュリティ情報登録の強化エクスペリエンス](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>統合された登録の有効化

統合された登録を有効にするには、次の手順に従います。

1. Azure portal にユーザー管理者または全体管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー設定]**  >  **[ユーザー機能プレビュー設定の管理]** に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** で、有効にする範囲 (**選択済み** のユーザーのグループまたは **すべて** のユーザー) を選択します。

   ![すべてのユーザーに対して統合されたセキュリティ情報のプレビュー エクスペリエンスを有効にする](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 統合された登録を有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、Multi-Factor Authentication と SSPR ポリシーでこれらの方法が有効な場合、これらを Multi-Factor Authentication と SSPR に使用できます。 このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (`https://aka.ms/ssprsetup`) にアクセスするユーザーは、ページにアクセスする前に多要素認証を実行する必要があります。

Internet Explorer でサイトとゾーンの割り当て一覧を構成した場合、以下のサイトは同じゾーン内に存在する必要があります。

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>統合登録の条件付きアクセス ポリシー

Azure Multi-Factor Authentication とセルフサービス パスワード リセット の登録をユーザーがいつどのように行うかについてのセキュリティ保護が、条件付きアクセス ポリシーのユーザー アクションを使用して可能になりました。 この機能は、[統合登録機能](../authentication/concept-registration-mfa-sspr-combined.md)を有効にしている組織で使用できます。 この機能は、HR オンボード中に信頼できるネットワークの場所などの一元化された場所から Azure Multi-Factor Authentication および SSPR の登録をユーザーに行わせたい組織で有効にすることができます。

条件付きアクセスでの信頼できる場所の作成について詳しくは、[Azure Active Directory 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md#named-locations)に関する記事をご覧ください。

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>信頼できる場所からの登録を要求するポリシーを作成する

次のポリシーは、統合された登録エクスペリエンスを使用して登録を試みるすべての選択ユーザーに適用されます。これにより、信頼されたネットワークとしてマークされている場所から接続されている場合を除き、アクセスがブロックされます。

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します。
1. **[新しいポリシー]** を選択します。
1. このポリシーの名前を入力します。たとえば、"*信頼されたネットワーク上の統合されたセキュリティ情報の登録*" などです。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。 このポリシーを適用するユーザーとグループを選択し、 **[完了]** を選択します。

   > [!WARNING]
   > 統合された登録に対してユーザーを有効にする必要があります。

1. **[クラウド アプリまたはアクション]** で、 **[ユーザー操作]** を選択します。 **[セキュリティ情報の登録]** をオンにし、 **[完了]** を選択します。

    ![セキュリティ情報の登録を制御する条件付きアクセスポリシーを作成する](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **[条件]**  >  **[場所]** で、次のオプションを構成します。
   1. **[はい]** を構成します
   1. **[任意の場所]** を含めます
   1. **[すべての信頼できる場所]** を除外します
1. *[場所]* ウィンドウで **[完了]** を選択し、 *[条件]* ウィンドウで **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセスのブロック]** 、 **[選択]** の順に選択します。
1. **[ポリシーを有効にする]** を **[オン]** に設定します
1. ポリシーを完了するには、 **[作成]** を選択します。

## <a name="next-steps"></a>次のステップ

ヘルプが必要な場合は、[統合されたセキュリティ情報の登録に関するトラブルシューティング](howto-registration-mfa-sspr-combined-troubleshoot.md)方法、または [Azure Active Directory 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md)を参照してください。

Azure AD テナントの機能を有効にするには、[セルフサービス パスワード リセットを有効にする](tutorial-enable-sspr.md)チュートリアルと、[Azure Multi-Factor Authentication を有効にする](tutorial-enable-azure-mfa.md)チュートリアルを参照してください。

[テナントでの統合された登録を有効にする](howto-registration-mfa-sspr-combined.md)方法、または[ユーザーに認証方法の再登録を強制する](howto-mfa-userdevicesettings.md#manage-user-authentication-options)方法について説明します。

また、[Azure Multi-Factor Authentication と SSPR で使用可能な方法](concept-authentication-methods.md)を確認することもできます。
