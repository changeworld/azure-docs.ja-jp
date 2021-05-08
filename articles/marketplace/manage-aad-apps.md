---
title: Azure AD アプリケーションの追加と管理 - Azure Marketplace
description: パートナー センターでコマーシャル マーケットプレース プログラムに Azure AD アプリケーションを追加して管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107989"
---
# <a name="add-and-manage-azure-ad-applications"></a>Azure AD アプリケーションを追加して管理する

**適切なロール**

- 所有者
- 管理者

貴社の Azure Active Directory (Azure AD) に属しているアプリケーションまたはサービスに対し、パートナー センター アカウントへのアクセスを許可することができます。

## <a name="add-existing-azure-ad-applications"></a>既存の Azure AD アプリケーションを追加する

会社の Azure Active Directory に存在するアプリケーションを追加するには:

1. **[アカウント設定]** の **[ユーザー]** ページから **[Add Azure AD applications]\(Azure AD アプリケーションの追加\)** を選択します。
1. 表示された一覧から少なくとも 1 つの Azure AD アプリケーションを選択します。 検索ボックスを使用して特定の Azure AD アプリケーションを検索できます。 パートナー センター アカウントに追加する Azure AD アプリケーションを複数選択する場合は、それらに同じロール (カスタム アクセス許可のセット) を割り当てる必要があります。 ロールまたはアクセス許可が異なる複数の Azure AD アプリケーションを追加するには、ロール (またはカスタム アクセス許可のセット) ごとにこれらの手順を繰り返してください。
1. Azure AD アプリケーションの選択が完了したら、 **[選択項目の追加]** を選択します。
1. **[ロール]** セクションで、選択した Azure AD アプリケーションのロール (またはカスタマイズされたアクセス許可) を指定します。
1. **[保存]** を選択します。

## <a name="add-new-azure-ad-applications"></a>新しい Azure AD アプリケーションを追加する

新しい Azure AD アプリケーション アカウントにパートナー センターのアクセス権を与えたい場合、そのアカウントを **[ユーザー]** セクションで作成できます。 この場合、新しいアカウントは、パートナー センター アカウントだけでなく、貴社の職場アカウント (Azure AD テナント) にも作成されます。 その Azure AD アプリケーションの主な用途がパートナー センターの認証であり、それに直接アクセスするためのユーザーが不要であれば、 **[Reply URL]\(応答 URL\)** と **[App ID URI]\(アプリ ID/URI\)** には、任意の有効なアドレスを入力してかまいません (ただし、ディレクトリ内の他の Azure AD アプリケーションによって使用されていない値)。

1. **[アカウント設定]** の **[ユーザー]** ページから **[Add Azure AD applications]\(Azure AD アプリケーションの追加\)** を選択します。
1. 次のページで、 **[New Azure AD application]\(新しい Azure AD アプリケーション\)** を選択します。
1. 新しい Azure AD アプリケーションの **応答 URL** を入力します。 これは、Azure AD アプリケーションにユーザーがサインインして使用するための URL です ("アプリの URL" や "サインオン URL" と呼ばれることもあります)。 *応答 URL* は 256 文字未満、かつディレクトリ内で一意であることが必要です。
1. 新しい Azure AD アプリケーションの **アプリ ID/URI** を入力します。 これは、シングル サインオン要求が Azure AD に送信されるときに提示される、Azure AD アプリケーションの論理的な ID です。 *アプリ ID/URI* は、ディレクトリ内の Azure AD アプリケーションごとに一意であることが必要です。 この ID は 256 文字以下にする必要があります。 アプリ ID/URI の詳細については、[Azure Active Directory とアプリケーションの統合](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)に関するページを参照してください。
1. **[ロール]** セクションで、Azure AD アプリケーションのロール (またはカスタマイズされたアクセス許可) を指定します。
1. **[保存]** を選択します。

Azure AD アプリケーションを追加または作成したら、 **[ユーザー]** セクションに戻り、アプリケーション名を選択して、アプリケーションの設定 (テナント ID、クライアント ID、応答 URL、アプリ ID/URI など) を確認することができます。

## <a name="remove-an-azure-ad-application"></a>Azure AD アプリケーションを削除する

職場アカウント (Azure AD テナント) からアプリケーションを削除するには、 **[アカウント設定]** の **[ユーザー]** に移動し、削除したいアプリケーションを右端の列のチェック ボックスで選択して、使用可能なアクションから **[削除]** を選びます。 選択されたアプリケーションを削除してよいか確認するポップアップ ウィンドウが表示されます。

## <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD アプリケーションのキーを管理する

Microsoft Azure AD でのデータの読み取り/書き込みを Azure AD アプリケーションで行うには、キーが必要です。 Azure AD アプリケーションのキーは、パートナー センターでその情報を編集することにより作成できます。 不要になったキーを削除することもできます。

1. **[アカウント設定]** の **[ユーザー]** ページから Azure AD アプリケーションの名前を選択します。 Azure AD アプリケーションのアクティブなキーがすべて表示されます (キーが作成された日付とその有効期限が切れる日付 50 を含む)。
1. 不要になったキーを削除するには、 **[削除]** を選択します。
1. 新しいキーを追加するには、 **[Add new key]\(新しいキーを追加\)** を選択します。
1. **クライアント ID** と **キー値** を示した画面が表示されます。 この情報は必ず印刷またはコピーしてください。このページを離れた後は二度とアクセスできなくなります。
1. さらにキーを作成したい場合は、 **[Add another key]\(別のキーを追加\)** を選択します。
