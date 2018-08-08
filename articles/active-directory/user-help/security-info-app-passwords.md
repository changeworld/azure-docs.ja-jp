---
title: セキュリティ情報を使用したアプリ パスワードの設定 - Azure Active Directory | Microsoft Docs
description: セキュリティ情報を使用して、通常のパスワードとは別に、各非ブラウザー アプリで使用するように自動生成パスワード (アプリ パスワード) を設定します。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 56d207dc47b9e055782568f1c4a4cedc4d19a288
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348020"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>セキュリティ情報を使用したアプリ パスワードの管理 (プレビュー)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Outlook 2010 などの特定の非ブラウザー アプリは、2 段階認証をサポートしていません。 このサポートの欠如は、2 段階認証を使用している場合、アプリが動作しないことを意味します。 この問題を回避するために、通常のパスワードとは別に、各非ブラウザー アプリで使用する自動生成パスワードを作成できます。

アプリ パスワードを使用する場合は、次の点に注意してください。

- アプリ パスワードは自動生成され、アプリごとに 1 回しか入力されない。

- ユーザー 1 人あたりのパスワード数の上限は 40 個である。 この上限を超えて作成を試行すると、新しいパスワードの作成が許可されるように、事前に既存のパスワードを削除するよう求められます。

- アプリ パスワードは、アプリごとではなく、デバイスごとに 1 つ使用します。 たとえば、ラップトップ上のすべてのアプリに対して単一のパスワードを作成してから、デスクトップ上のすべてのアプリに対してもう 1 つの別のパスワードを作成します。

    >[!Note]
    >Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートしており、2 段階認証を使用できます。 このサポートは、2 段階認証が有効になれば、以降は Office 2013 クライアントに対するアプリ パスワードは必要なくなることを意味します。 詳細については、「[Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)」記事を参照してください。

## <a name="create-and-delete-app-passwords-using-security-info"></a>セキュリティ情報を使用してアプリ パスワードを作成および削除する

職場または学校のアカウントで 2 段階認証を使用し、管理者によってセキュリティ情報エクスペリエンスが有効化されている場合は、My Apps ポータルを使用してアプリ パスワードを作成および削除できます。

管理者がセキュリティ情報エクスペリエンスを有効にしていない場合は、[2 段階認証用のアプリ パスワードの管理](multi-factor-authentication-end-user-app-passwords.md)に関するセクションの手順と情報に従う必要があります。

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>My Apps ポータルを使用してアプリ パスワードを作成するには

1. 職場または学校のアカウントにサインインします。

2. myapps.microsoft.com に進み、ページの右上隅から自分の名前を選択し、**[プロファイル]** を選択します。

3. **[アカウントの管理]** 領域で **[セキュリティ情報の編集]** を選択します。

    ![[セキュリティ情報の編集] リンクが強調表示されているプロファイル画面](media/security-info/security-info-profile.png)

4. **[アカウントのセキュリティ保護]** 画面で、**[セキュリティ情報の追加]** を選択します。

    ![セキュリティ情報画面。既存の編集可能な情報が表示されています](media/security-info/security-info-edit-add-info.png)

5. **[セキュリティ情報の追加]** 画面で、**[アプリ パスワード]** を選択します。

6. **[アプリ パスワードの作成]** 画面で、アプリ パスワードの名前を入力し、**[次へ]** を選択します。

    ![アプリ パスワードに名前を付ける画面](media/security-info/security-info-name-app-password.png)

7. **[コピー]** を選択してパスワードをクリップボードにコピーし、**[次へ]** を選択します。

    ![アプリ パスワードをコピーするための画面](media/security-info/security-info-create-app-password.png)
    
8. アプリ パスワードが **[アカウントのセキュリティ保護]** 画面に表示されることを確認します。

    ![セキュリティ保護の画面と、アプリ パスワード](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>My Apps ポータルを使用してアプリ パスワードを削除するには

1. **[アカウントのセキュリティ保護]** 画面で、アプリ パスワードの横の **[X]** を選択して削除します。

    ![セキュリティ保護の画面、アプリ パスワードの削除](media/security-info/security-info-keep-secure-delete-app-password.png)

2. **[アプリケーションのパスワードを削除]** 画面で、**[削除]** を選択します。

    ![アプリケーションのパスワードを削除画面](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>次の手順

- セキュリティ情報を更新する必要がある場合は、「[セキュリティ情報の管理](security-info-manage-settings.md)」にある説明に従ってください。

- セキュリティ情報と実行できることに関する一般的な情報については、「[セキュリティ情報の概要](user-help-security-info-overview.md)」を参照してください。 