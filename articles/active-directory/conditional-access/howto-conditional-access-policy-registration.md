---
title: 条件付きアクセス - 統合されたセキュリティ情報 - Azure Active Directory
description: セキュリティ情報登録のためのカスタム条件付きアクセス ポリシーを作成する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/15/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d7d3ec6c9d66d756294597c7a282a232208b5b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710850"
---
# <a name="conditional-access-securing-security-info-registration"></a>条件付きアクセス:セキュリティ情報登録のセキュリティ保護

Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの登録をユーザーがいつどのように行うかについてのセキュリティ保護が、条件付きアクセス ポリシーのユーザー アクションを使用して可能になりました。 この機能は、[統合された登録](../authentication/concept-registration-mfa-sspr-combined.md)を有効にしている組織で使用できます。 この機能により、組織は、条件付きアクセス ポリシー内のアプリケーションと同様に登録プロセスを処理し、条件付きアクセスの機能を最大限に活用してエクスペリエンスをセキュリティ保護できます。 Microsoft Authenticator アプリにサインインしているか、パスワードレスの電話によるサインインを有効にしているユーザーは、このポリシーの対象となります。

一部の組織では、これまで、登録エクスペリエンスをセキュリティ保護する手段として、信頼されたネットワークの場所またはデバイスのコンプライアンスを使用していた場合がありました。 Azure AD に[一時アクセス パス](../authentication/howto-authentication-temporary-access-pass.md)が追加されたことにより、管理者は、任意のデバイスや場所から登録できるように、時間制限のある資格情報をユーザーに提供できます。 一時アクセス パスの資格情報は、多要素認証の条件付きアクセス要件を満たしています。

## <a name="template-deployment"></a>テンプレートのデプロイ

組織は、このポリシーをデプロイするのに以下に示す手順を使用するか、[条件付きアクセス テンプレート (プレビュー) ](concept-conditional-access-policy-common.md#conditional-access-templates-preview)を使用するかを選ぶことができます。 

## <a name="create-a-policy-to-secure-registration"></a>登録をセキュリティ保護するためのポリシーを作成する

次のポリシーは、統合された登録エクスペリエンスを使用して登録を試みる選択ユーザーに適用されます。 このポリシーでは、ユーザーが信頼できるネットワークの場所にいること、多要素認証を行うこと、または一時アクセス パスの資格情報を使用することが必要です。

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します。
1. **[新しいポリシー]** を選択します。
1. [名前] に、このポリシーの名前を入力します。 たとえば、「**TAP での統合されたセキュリティ情報の登録**」などです。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。

      > [!WARNING]
      > [[統合された登録]](../authentication/howto-registration-mfa-sspr-combined.md) に対してユーザーを有効にする必要があります。

   1. **[除外]** で、次のようにします。
      1. **[すべてのゲストと外部ユーザー]** を選択します。
      1. **[ディレクトリ ロール]** を選択し、 **[グローバル管理者]** を選択します
      
         > [!NOTE]
         > ゲスト ユーザーに対しては、一時アクセス パスは機能しません。

      1. **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
1. **[クラウド アプリまたはアクション]** から **[ユーザー アクション]** を選択し、 **[セキュリティ情報の登録]** をオンにします。
1. **[条件]**  >  **[場所]** で 
   1. **[構成]** を **[はい]** に設定します。 
      1. **[任意の場所]** を含めます。
      1. **[すべての信頼できる場所]** を除外します。
1. **[アクセス制御]**  >  **[付与]** で 
   1. **[アクセス権の付与]** 、 **[多要素認証を要求する]**  を選択します。
   1. **[選択]** を選択します。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

管理者は、新しいユーザーに対して一時アクセス パス資格情報を発行する必要があります。これにより、登録するための多要素認証の要件を満たすことができます。 このタスクを実行する手順については、[Azure AD での一時アクセス パスの作成](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)に関するセクションを参照してください。

組織では、手順 6b の **[多要素認証が必要]** に加えて、またはその代わりに、他の許可制御の要求を選択することもできます。 複数の制御を選択する場合は、この変更を行うときに、選択した制御の **すべて** または **いずれか** を必要とするように、適切なオプション ボタンのトグルを選択してください。

### <a name="guest-user-registration"></a>ゲスト ユーザーの登録

ディレクトリで多要素認証への登録が必要な[ゲスト ユーザー](../external-identities/what-is-b2b.md)については、次のガイドを使用して、[信頼されたネットワークの場所](concept-conditional-access-conditions.md#locations)の外部からの登録をブロックすることを選択できます。

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します。
1. **[新しいポリシー]** を選択します。
1. [名前] に、このポリシーの名前を入力します。 たとえば、「**信頼されたネットワーク上の統合されたセキュリティ情報の登録**」など。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[含める]** で、 **[すべてのゲストと外部ユーザー]** を選択します。
1. **[クラウド アプリまたはアクション]** から **[ユーザー アクション]** を選択し、 **[セキュリティ情報の登録]** をオンにします。
1. **[条件]**  >  **[場所]** で
   1. **[はい]** を構成します。
   1. **[任意の場所]** を含めます。
   1. **[すべての信頼できる場所]** を除外します。
1. **[アクセス制御]**  >  **[付与]** で
   1. **[アクセスのブロック]** を選択します。
   1. **[選択]** をクリックします。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[ユーザーに認証情報の再認証を要求する](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
