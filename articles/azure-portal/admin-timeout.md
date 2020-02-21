---
title: Azure portal のユーザーに対するディレクトリ レベルの非アクティブ タイムアウトの設定 | Microsoft Docs
description: 管理者は、セッションがサインアウトするまでの最大アイドル時間を適用できます。非アクティブ タイムアウト ポリシーは、ディレクトリ レベルで設定されます。
services: azure-portal
keywords: 設定、タイムアウト
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 950580048f0496fd8436901938a5b6768c61bab6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132089"
---
# <a name="set-directory-level-inactivity-timeout"></a>ディレクトリ レベルの非アクティブ タイムアウトの設定

非アクティブ タイムアウト設定を利用すると、ワークステーションのセキュリティ保護を忘れた場合に、リソースを未承認のアクセスから保護できます。 ユーザーがしばらくの間アイドル状態になると、Azure portal セッションが自動的にサインアウトします。[グローバル管理者ロール](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)の管理者は、セッションがサインアウトするまでの最大アイドル時間を適用できます。非アクティブ タイムアウト ポリシーは、ディレクトリ レベルで適用されます。 ディレクトリの詳細については、「[Active Directory Domain Services の概要](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。

## <a name="configure-the-inactive-timeout-setting"></a>非アクティブ タイムアウト設定の構成

グローバル管理者として、Azure portal のすべてのユーザーに対してアイドル タイムアウト設定を適用する場合は、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. グローバル ページ ヘッダーから **[設定]** を選択します。
3. リンク テキスト「**ディレクトリ レベルのタイムアウトの構成**」を選択します。

    ![リンク テキストが強調表示されたポータル設定を示すスクリーンショット](./media/admin-timeout/settings.png)

4. 新しいページが開きます。 **[ディレクトリ レベルの非アクティブ タイムアウトの構成]** ページで、 **[Azure portal のディレクトリ レベルのアイドル タイムアウトを有効にする]** を選択して設定をオンにします。
5. 次に、セッションが自動的にサインアウトになる前までのユーザーの最大アイドル時間について **[時間]** と **[分]** を入力します。
6. **[適用]** を選択します。

    ![ディレクトリ レベルの非アクティブ タイムアウトの設定を示すページのスクリーンショット](./media/admin-timeout/configure.png)

非アクティブ タイムアウト ポリシーが設定されていることを確認するには、グローバル ページ ヘッダーから **[通知]** を選択します。 成功通知が一覧表示されていることを確認します。

  ![ディレクトリ レベルの非アクティブ タイムアウトの成功通知を示すスクリーンショット](./media/admin-timeout/confirmation.png)

この設定は、新しいセッションに対して有効になります。 既にサインインしているユーザーには、すぐには適用されません。

> [!NOTE]
> 管理者がディレクトリ レベルのタイムアウト設定を構成している場合、ユーザーはポリシーをオーバーライドして、独自の非アクティブ サインアウト期間を設定できます。 ただし、ユーザーは、ディレクトリ レベルで設定されている時間よりも短い時間間隔を選択する必要があります。
>

## <a name="next-steps"></a>次のステップ

* [Azure portal の基本設定を設定する](set-preferences.md)
* [ユーザー設定のエクスポートまたは削除](azure-portal-export-delete-settings.md)
* [ハイコントラストの有効化またはテーマの変更](azure-portal-change-theme-high-contrast.md)
