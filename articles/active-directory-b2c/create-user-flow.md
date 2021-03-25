---
title: ユーザー フローの作成 - Azure Active Directory B2C
description: Azure Active Directory B2C でアプリケーションのサインアップ、サインイン、ユーザー プロファイルの編集を可能にするユーザー フローを Azure portal で作成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556498"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローを作成する

Azure Active Directory B2C (Azure AD B2C) テナントにさまざまな種類の[ユーザー フロー](user-flow-overview.md)を作成し、必要に応じてそれらをアプリケーションで使用できます。 ユーザー フローは、アプリケーション間で再利用することができます。

> [!IMPORTANT]
> ユーザー フローのバージョンを参照する方法が変更されました。 これまでは、V1 (運用対応) バージョンと V1.1 および V2 (プレビュー) バージョンが提供されていました。 現在は、ユーザー フローは **推奨** (次世代プレビュー) バージョンと **標準** (一般提供) バージョンに統合されています。 V1.1 と V2 の従来のプレビュー ユーザー フローはすべて、**2021 年 8 月 1 日** までに非推奨になる予定です。 詳細については、[Azure AD B2C のユーザー フローのバージョン](user-flow-versions.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

- 新しいユーザー フローのテストに使用する **アプリケーションを登録** します。 例については、「[チュートリアル: Azure Active Directory B2C に Web アプリケーションを登録する](tutorial-register-applications.md)」を参照してください。
- Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft、Twitter などのプロバイダーを使用したユーザー サインインを有効にする場合は、**外部 ID プロバイダーを追加** します。 [Azure AD B2C でのアプリケーションへの ID プロバイダーの追加](add-identity-provider.md)に関する記事をご覧ください。
- **ローカル アカウント ID プロバイダーを構成** して、テナントのローカル アカウントでサポートする ID の種類 (電子メール、ユーザー名、電話番号) を指定します。 個々のユーザー フローを作成するときに、これらのサポートされている ID の種類から選択できます。 ユーザーがユーザー フローを完了すると、Azure AD B2C ディレクトリにローカル アカウントが作成され、**ローカル アカウント** ID プロバイダーによってユーザーの情報が認証されます。 次の手順に従って、テナントのローカル アカウント ID プロバイダーを構成します。

   1. [Azure portal](https://portal.azure.com/) にサインインします。 
   2. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
   3. Azure portal の上部にある検索バーで、**Azure AD B2C** を検索して選択します。
   4. **[管理]** で、 **[ID プロバイダー]** を選択します。
   5. ID プロバイダーの一覧で、 **[ローカル アカウント]** を選択します。
   6. **[ローカル IdP を構成します]** ページで、サポートする ID の種類をすべて選択します。 ここでオプションを選択すると、後で作成するユーザー フローでそれらを使用できるようになります。
      - **[電話]** (プレビュー): ユーザーが電話番号を入力できるようにします。これはサインアップ時に確認され、ユーザー ID になります。
      - **[電子メール]** (既定): ユーザーがメール アドレスを入力できるようにします。これはサインアップ時に確認され、ユーザー ID になります。
      - **[ユーザー名]** : ユーザーが独自の一意のユーザー ID を作成できるようにします。 ユーザーからメール アドレスが収集され、確認されます。
    7. **[保存]** を選択します。

## <a name="create-a-user-flow"></a>ユーザー フローを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータルツールバーの **[Directory + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントが含まれているディレクトリを選択します。

    ![B2C テナント、ディレクトリとサブスクリプションのペイン、Azure portal](./media/create-user-flow/directory-subscription-pane.png)

3. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
4. **[ポリシー]** で、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。

    ![[新しいユーザー フロー] ボタンが強調表示されているポータル内の [ユーザー フロー] ページ](./media/create-user-flow/signup-signin-user-flow.png)

5. **[ユーザー フローの作成]** ページで、作成するユーザー フローの種類を選択します (概要については、「[Azure Active Directory B2C のユーザー フロー](user-flow-overview.md)」を参照してください)。

    ![サインアップとサインインのフローが強調表示されているユーザー フローの選択ページ](./media/create-user-flow/select-user-flow-type.png)

6. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。 (ユーザー フローのバージョンに関する[詳細情報](user-flow-versions.md))。

    ![プロパティが強調表示されている、Azure portal 内の [ユーザー フローの作成] ページ](./media/create-user-flow/select-version.png)

7. ユーザー フローの **名前** を入力します (例: *signupsignin1*、*profileediting1*、*passwordreset1*)。
8. **[ID プロバイダー]** で、作成するユーザー フローの種類に応じてオプションを選択します。

   - **[ローカル アカウント]** : ユーザーが Azure AD B2C テナントにローカル アカウントを作成できるようにする場合は、使用する識別子の種類 (電子メール、ユーザー ID、電話など) を選択します。 [ローカル アカウント ID プロバイダー](#before-you-begin)設定で構成されている ID の種類だけが表示されます。

   - **[ソーシャル ID プロバイダー]** : Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft、Twitter など、[追加したソーシャル ID プロバイダー](add-identity-provider.md)を使用したユーザー サインインを許可する場合は、一覧からプロバイダーを選択します。

9. **[ユーザー属性と要求]** で、サインアップ中にユーザーから収集して送信する要求と属性を選択します。 **[詳細を表示]** を選択します。 属性と要求を選択し、 **[OK]** を選択します。

    ![3 つの要求が選択されている [属性と要求の選択] ページ](./media/create-user-flow/signup-signin-attributes.png)

10. **[作成]** を選択して、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. **[ポリシー]**  >  **[ユーザー フロー]** を選択し、作成したユーザー フローを選択します。 ユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、手順 1. で登録した Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
2. テストするユーザー フローの種類に応じて、有効なメール アドレスを使用してサインアップし、サインアップ フローに従うか、以前に作成したアカウントを使用してサインインします。

    ![[ユーザー フローを実行します] ボタンが強調表示されているポータル内の [ユーザー フローを実行します] ページ](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. ユーザー フローのプロンプトに従います。 ユーザー フローを完了すると、`https://jwt.ms` にトークンが返され、ユーザーに表示されます。

> [!NOTE]
> [ユーザー フローを実行します] エクスペリエンスは、現在、認可コード フローを使用する、種類が SPA の応答 URL と互換性がありません。 これらの種類のアプリで [ユーザー フローを実行します] エクスペリエンスを使用するには、種類が "Web" の応答 URL を登録し、[こちら](tutorial-register-spa.md)の説明に従って暗黙的なフローを有効にします。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C のユーザー フローに条件付きアクセスを追加する](conditional-access-user-flow.md)
- [Azure AD B2C ユーザー フローのユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)
