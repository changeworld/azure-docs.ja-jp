---
title: "Azure AD Connect: パススルー認証 - スマート ロックアウト | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証がクラウドのブルート フォース パスワード攻撃からオンプレミスのアカウントを保護する方法について説明します"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: abd9b8559cb70be6d03b85cfe19a6b37b7069985
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory パススルー認証: スマート ロックアウト

## <a name="overview"></a>概要

Azure Active Directory (Azure AD) は、ブルート フォース パスワード攻撃から保護し、正規のユーザーが Office 365 および SaaS アプリケーションからロックアウトされることを防ぎます。 *スマート ロックアウト*と呼ばれるこの機能は、サインイン方法としてパススルー認証を使っている場合にサポートされます。 スマート ロックアウトはすべてのテナントに対して既定で有効で、お使いのユーザー アカウントを継続的に保護します。

スマート ロックアウトは、失敗したサインインの試行を追跡します。 特定の*ロックアウトしきい値*の後に、*ロックアウト期間*を開始します。 スマート ロックアウトはロックアウト期間中に攻撃者がサインインしようとしても拒否します。 攻撃が継続して発生する場合、ロックアウト期間終了後にサインインの試行が失敗すると、ロックアウト期間がさらに長くなります。

>[!NOTE]
>既定のロックアウトのしきい値は、試行失敗 10 回です。 既定のロックアウト期間は 60 秒です。

また、スマート ロックアウトは正規のユーザーによるサインインと攻撃者によるサインインを区別します。 ほとんどの場合は攻撃者のみをロックアウトします。 この機能は、攻撃者の悪意によって正規のユーザーがロックアウトされるのを防ぎます。 スマート ロックアウトは、正規のユーザーと攻撃者を区別するのに、過去のサインイン動作、ユーザーのデバイスとブラウザー、その他のシグナルを使用します。 このアルゴリズムは常に改善されています。

パススルー認証はパスワード検証要求をオンプレミスの Active Directory に転送するので、攻撃者がユーザーの Active Directory アカウントをロックアウトするのを防ぐ必要があります。 Active Directory には独自のアカウント ロックアウト ポリシーがあります。具体的には[アカウント ロックアウトのしきい値](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)と[アカウント ロックアウト カウンターのリセット](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)ポリシーです。 Azure AD のロックアウトのしきい値とロックアウト期間の値を適切に構成し、クラウド内の攻撃がオンプレミスの Active Directory に到達する前にフィルター処理します。

>[!NOTE]
>スマート ロックアウト機能は無料で、すべてのユーザーに対して既定で_オン_になっています。 ただし、Graph API を使って Azure AD のロックアウトしきい値とロックアウト期間の値を変更するには、テナントが Azure AD Premium P2 のライセンスを少なくとも 1 つ所有している必要があります。 パススルー認証を使ったスマート ロックアウト機能を利用するために "_ユーザーごと_" の Azure AD Premium P2 ライセンスは必要ありません。

ユーザーのオンプレミスの Active Directory アカウントを適切に保護するには、以下のようにする必要があります。

   * Azure AD のロックアウトしきい値が、Active Directory アカウント ロックアウトしきい値より_小さい_。 Active Directory アカウント ロックアウトしきい値が Azure AD のロックアウトしきい値より少なくとも 2 - 3 倍長くなるように値を設定します。
   * Azure AD のロックアウト期間 (秒単位) が、Active Directory のロックアウト カウンターのリセットの期間 (分単位) より _長い_。

>[!IMPORTANT]
>現時点では、ユーザーのクラウド アカウントがスマート ロックアウト機能によってロックされている場合、管理者はロックを解除できません。 管理者はロックアウト期間が期限切れになるまで待つ必要があります。

## <a name="verify-your-active-directory-account-lockout-policies"></a>Active Directory アカウント ロックアウト ポリシーを確認する

Active Directory アカウント ロックアウト ポリシーを確認するには、次のようにします。

1.  グループ ポリシー管理ツールを開きます。
2.  **既定のドメイン ポリシー**など、すべてのユーザーに適用されるグループ ポリシーを編集します。
3.  **[コンピューターの構成]** > **[ポリシー]** > **[Windows 設定]** > **[セキュリティ設定]** > **[アカウント ポリシー]** > **[アカウント ロックアウト ポリシー]** の順に移動します。
4.  **[アカウント ロックアウトのしきい値]** と **[ロックアウト カウンターのリセット]** の値を確認します。

![Active Directory アカウント ロックアウト ポリシー](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Graph API を使用してテナントのスマート ロックアウトの値を管理する (Premium ライセンスが必要)

>[!IMPORTANT]
>Graph API を使って Azure AD のロックアウトしきい値とロックアウト期間の値を変更するのは、Azure AD Premium P2 の機能です。 また、テナントのグローバル管理者である必要があります。

[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用して、Azure AD のスマート ロックアウトの値を読み取り、設定し、更新できます。 これらの操作はプログラムでも実行できます。

### <a name="view-smart-lockout-values"></a>スマート ロックアウトの値を表示する

テナントのスマート ロックアウトの値を表示するには、こちらの手順のようにします。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. **[アクセス許可の変更]** を選択して、アクセス許可 **Directory.ReadWrite.All** を選択します。
3. Graph API の要求で、バージョンを **BETA** に、要求の種類を **GET** に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings` に設定します。
4. **[クエリの実行]** を選択して、テナントのスマート ロックアウトの値を確認します。 それまでにテナントの値を設定していない場合、空のセットが表示されます。

### <a name="set-smart-lockout-values"></a>スマート ロックアウトの値を設定する

テナントのスマート ロックアウトの値を設定するには、次の手順のようにします (初回のみ必要)。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. **[アクセス許可の変更]** を選択して、アクセス許可 **Directory.ReadWrite.All** を選択します。
3. Graph API の要求で、バージョンを **BETA** に、要求の種類を **POST** に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings` に設定します。
4. 次の JSON 要求をコピーし、**Request Body** フィールドに貼り付けます。
5. **[クエリの実行]** を選択して、テナントのスマート ロックアウトの値を設定します。

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>これらの値を使っていない場合は、**BannedPasswordList** および **EnableBannedPasswordCheck** をそれぞれ、空 (**""**) および **false** のままにします。

「[スマート ロックアウトの値を表示する](#view-smart-lockout-values)」の手順を使って、テナントのスマート ロックアウトの値が正しく設定されていることを確認します。

### <a name="update-smart-lockout-values"></a>スマート ロックアウトの値を更新する

テナントのスマート ロックアウトの値を更新するには、次の手順のようにします (以前に設定してある場合)。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. **[アクセス許可の変更]** を選択して、アクセス許可 **Directory.ReadWrite.All** を選択します。
3. [テナントのスマート ロックアウトの値を表示するには、こちらの手順のようにします](#view-smart-lockout-values)。 **displayName** が **PasswordRuleSettings** である項目の `id` 値 (GUID) をコピーします。
4. Graph API の要求で、バージョンを **BETA** に、要求の種類を **PATCH** に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` に設定します。 手順 3 の GUID を `<id>` に使用します。
5. 次の JSON 要求をコピーし、**Request Body** フィールドに貼り付けます。 スマート ロックアウトの値を適切に変更します。
6. **[クエリの実行]** を選択して、テナントのスマート ロックアウトの値を更新します。

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

「[スマート ロックアウトの値を表示する](#view-smart-lockout-values)」の手順を使って、テナントのスマート ロックアウトの値が正しく更新されていることを確認します。

## <a name="next-steps"></a>次のステップ
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。
