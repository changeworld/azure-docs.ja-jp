---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday モバイル アプリケーションの統合 | Microsoft Docs
description: Azure Active Directory と Workday モバイル アプリケーションの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday モバイル アプリケーションの統合

このチュートリアルでは、Azure Active Directory (Azure AD)、条件付きアクセス、および Intune と、Workday モバイル アプリを統合する方法について説明します。 Workday のモバイル アプリと Microsoft 製品を統合すると、次のことができます。

* サインインする前に、デバイスがポリシーに準拠していることを確認する。
* Workday のアプリにコントロールを追加して、ユーザーが会社のデータに安全にアクセスできるようにする。 
* Workday にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Workday と Azure AD の統合
* チュートリアル:[Azure Active Directory シングル サインオン (SSO) と Workday の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Workday のモバイル アプリケーションで Microsoft の条件付きアクセス ポリシーと Intune を構成し、テストします。

* Workday フェデレーション アプリケーションを Azure AD と共に構成して SSO を有効にできるようになりました。 構成方法の詳細については、[こちら](workday-tutorial.md)のリンクを参照してください。

> [!NOTE] 
> Workday では、Intune の App Protection ポリシーをサポートしていません。 条件付きアクセスを利用するには、モバイル デバイス管理を使用する必要があります。


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>ユーザーが Workday モバイル アプリにアクセスできることの確認

Workday を構成して、モバイル アプリ オファリングへのアクセスを許可します。 モバイル向けに次のポリシーを構成する必要があります。

これらは次の手順に従って構成できます。

1. 機能領域のドメイン セキュリティ ポリシー レポートにアクセスします。
2. セキュリティ ポリシーを選択します。
    * モバイルの使用 - Android
    * モバイルの使用 - iPad
    * モバイルの使用 - iPhone
3. [権限を編集] をクリックします。
4. レポートまたはタスクのセキュリティ保護可能な項目へのアクセス権をセキュリティ グループに付与するには、[View]\(表示\) または [Modify]\(変更\) チェック ボックスをオンにします。
5. 統合およびレポートまたはタスクのセキュリティ保護可能なアクションへのアクセス権をセキュリティ グループに付与するには、[Get]\(取得\) または [Put]\(配置\) チェック ボックスをオンにします。

**[Activate Pending Security Policy Changes]\(保留中のセキュリティ ポリシーの変更をアクティブ化\)** タスクを実行して、保留中のセキュリティ ポリシーの変更をアクティブにします。

## <a name="open-workday-login-page-in-mobile-browser"></a>モバイル ブラウザーでの Workday ログイン ページの表示

Workday のモバイル アプリに条件付きアクセスを適用するには、アプリを外部ブラウザーで開く必要があります。 これを行うには、 **[Edit Tenant Setup - Security]\(テナント設定の編集 - セキュリティ\)** の **[Enable Mobile Browser SSO for Native Apps]\(ネイティブ アプリに対してモバイル ブラウザー SSO を有効にする\)** ボックスをオンにします。 そのためには、iOS 用のデバイスと Android 用の仕事用プロファイルに、Intune の承認済みブラウザーがインストールされている必要があります

![モバイル ブラウザー ログイン](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>条件付きアクセス ポリシーの設定

このポリシーは、iOS または Android デバイスでのログインにのみ影響します。 これをすべてのプラットフォームに拡張する場合は、 **[Any Device]\(任意のデバイス\)** を選択します。 このポリシーでは、デバイスがポリシーに準拠していることが必要で、それが Microsoft Intune によって確認されます。 Android に仕事用プロファイルがあるため、ユーザーが仕事用プロファイルを使用してログインし、Intune ポータル サイト経由でアプリをインストールしていない限り、Workday (Web またはアプリ) にログインできません。 iOS で同じ状況が確実に適用されるようにするためには、追加の手順が 1 つ必要です。 以下では、条件付きアクセスの設定のスクリーンショットをいくつか示します。

**Workday では、次のアクセス制御をサポートしています。**
* 多要素認証が必要です
* デバイスは準拠としてマーク済みである必要がある

**Workday アプリでは、以下はサポートしていません。**
* 承認済みクライアント アプリを必須にする
* アプリの保護ポリシー (プレビュー) を必須にする

**Workday** を **マネージド デバイス** として設定するには、次の手順に従います。

![条件付きアクセス ポリシーを設定する](./media/workday-tutorial/managed-devices-only.png)

1. **[ホーム]、[Microsoft Intune]、[条件付きアクセス ポリシー]、[Managed Devices Only]\(マネージド デバイスのみ\)** の順にクリックします 

1. **[Managed Devices Only]\(マネージド デバイスのみ\)** ページで、 **[名前]** フィールドの値を `Managed Devices Only` と指定し、 **[Cloud apps or actions]\(クラウド アプリまたはアクション\)** をクリックします。

1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)** ページで、次の手順を実行します。

    a. **[Select what this policy applies to]\(このポリシーが適用される対象を選択する\)** を **[クラウド アプリ]** に切り替えます。

    b. [必要] で、 **[アプリを選択]** をクリックします。

    c. 選択リストから **[Workday]** を選択します。

    d. **[Done]** をクリックします。

1. **[ポリシーを有効にする]** をオンにします。

1. **[保存]** をクリックします。

**[Grant access]\(アクセス権の付与\)** では、次の手順を実行します。

![Workday の条件付きアクセス ポリシーの設定](./media/workday-tutorial/managed-devices-only-2.png)

1. **[ホーム]、[Microsoft Intune]、[条件付きアクセス ポリシー]、[Managed Devices Only]\(マネージド デバイスのみ\)** の順にクリックします 

1. **[Managed Devices Only]\(マネージド デバイスのみ\)** ページで、 **[名前]** フィールドの値を `Managed Devices Only` と指定し、 **[アクセス制御]、[許可]** の順にクリックします。

1. **[許可]** ページで、次の手順を実行します。

    a. 適用する制御として **[Grant access]\(アクセス権の付与\)** を選択します。

    b. **[デバイスは準拠としてマーク済みである必要があります]** ボックスをオンにします。

    c. **[選択したコントロールのいずれかが必要]** を選択します。

    d. **[選択]** をクリックします。

1. **[ポリシーを有効にする]** をオンにします。

1. **[保存]**

## <a name="set-up-device-compliance-policy"></a>デバイス コンプライアンス ポリシーの設定

iOS デバイスが MDM マネージド Workday アプリを介してのみログインできるようにするには、制限付きアプリの一覧に **com.workday.workdayapp** を追加して、App Store アプリをブロックする必要があります。 これにより、会社のポータルを通じて Workday アプリがインストールされているデバイスのみが Workday にアクセスできるようになります。 ブラウザーについては、デバイスが Intune によって管理されていて、かつマネージド ブラウザーが使用されている場合にのみ、Workday にアクセスできます。

![Workday のデバイス コンプライアンス ポリシーの設定](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Microsoft Intune アプリ構成ポリシーの設定

| シナリオ | キーと値のペア |
|----------------------------------------------------------------------------------------   |-----------|
| 次の [テナント] および [Web アドレス] フィールドを自動的に設定します。<br>● 仕事用プロファイルで Android を有効にしている場合は、Android 上の Workday。<br>● iPad および iPhone 上の Workday。     | テナントの構成には、これらの値を使用します。 <br>● 構成キー = UserGroupCode<br>●   値の型 = String <br>●   構成値 = ご使用のテナントの名前。 例: gms<br>Web アドレスの構成には、これらの値を使用します。<br>●    構成キー = AppServiceHost<br>● 値の型 = String<br>●    構成値 = ご使用のテナントのベース URL。 例: https://www.myworkday.com                              |   |
| iPad および iPhone 上の Workday に対してこれらの操作を無効にします。<br>●    切り取り、コピー、貼り付け<br>●   印刷                       | これらのキーの値 (ブール値) を False に設定して、機能を無効にします。<br>● AllowCutCopyPaste<br>●  AllowPrint  |
| Android 上の Workday でスクリーンショットを無効にします。 |機能を無効にするには、AllowScreenshots キーの値 (ブール値) を False に設定します。|
| ユーザーに推奨される更新プログラムを無効にします。|機能を無効にするには、AllowSuggestedUpdates キーの値 (ブール値) を False に設定します。|
|アプリ ストアの URL をカスタマイズして、選択したアプリ ストアにモバイル ユーザーを誘導します。|アプリ ストアの URL を変更するには、これらの値を使用します。<br>● 構成キー = AppUpdateURL<br>●   値の型 = String<br> ●   構成値 = アプリ ストアの URL|
|       |


## <a name="ios-configuration-policies"></a>iOS の構成ポリシー

1. https://portal.azure.com/ に移動してログインします
2. 「 **Intune** 」を検索するか、一覧でウィジェットをクリックします。
3. **[クライアント アプリ]、[アプリ]、[アプリ構成ポリシー]、[+ 追加]、[マネージド デバイス]** の順に移動します
4. [名前] を入力します。
5. **[プラットフォーム]** で、 **[iOS/iPadOS]** を選択します
6. **[関連アプリ]** で、追加した iOS 用 Workday アプリを選択します
7. **[構成設定]** をクリックし、 **[構成設定の形式]** で、 **[XML データを入力する]** を選択します
8. XML ファイルの例を次に示します。 適用する構成を追加します。 **STRING_VALUE** を、使用する文字列で置き換えます。 `<true />` または `<false />` を `<true />` または `<false />` で置き換えます。 構成を追加しない場合は、True に設定されたものとして機能します。

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. [追加] をクリックします。
10. ページを最新の情報に更新し、新しく作成したポリシーをクリックします。
11. [割り当て] をクリックし、アプリを適用するユーザーを選択します。
12. [保存] をクリックします。

## <a name="android-configuration-policies"></a>Android の構成ポリシー

1. `https://portal.azure.com/` に移動してログインします。
2. 「 **Intune** 」を検索するか、一覧でウィジェットをクリックします。
3. **[クライアント アプリ]、[アプリ]、[アプリ構成ポリシー]、[+ 追加]、[マネージド デバイス]** の順に移動します
5. [名前] を入力します。 
6. **[プラットフォーム]** で、 **[Android]** を選択します
7. **[関連アプリ]** で、追加した Android 用 Workday アプリを選択します
8. **[構成設定]** をクリックし、 **[構成設定の形式]** で、 **[Enter JSON Data]\(JSON データを入力する\)** を選択します

