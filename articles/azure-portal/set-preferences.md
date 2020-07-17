---
title: Azure portal の基本設定を設定する | Microsoft Docs
description: 独自の基本設定を実現するために Azure portal の既定の設定を変更できます。 設定には、非アクティブ セッションのタイムアウト、既定のビュー、メニュー モード、コントラスト、テーマ、通知、言語と地域の形式が含まれます
services: azure-portal
keywords: 設定, タイムアウト, 言語, 地域
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310681"
---
# <a name="set-your-azure-portal-preferences"></a>Azure portal の基本設定を設定する

独自の基本設定を実現するために Azure portal の既定の設定を変更できます。 次の一覧に示した各設定を変更できます。

* [非アクティブ セッションのタイムアウト](#change-the-idle-duration-for-inactive-sign-out)
* [既定のビュー](#choose-your-default-view)
* [ポータルのメニュー モード](#choose-a-portal-menu-mode)
* [配色およびハイコントラスト テーマ](#choose-a-theme)
* [ポップアップ通知](#enable-or-disable-pop-up-notifications)
* [言語と地域の形式](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>ポータルの全般設定を変更する

1. [Azure portal](https://portal.azure.com) にサインインする
2. グローバル ページ ヘッダーから **[設定]** を選択します。

    ![設定が強調表示されたグローバル ページ ヘッダー アイコンを示すスクリーンショット](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>非アクティブ時のサインアウトのアイドル時間を変更する

非アクティブ タイムアウト設定を利用すると、ワークステーションのセキュリティ保護を忘れた場合にリソースを未承認のアクセスから保護できます。 ユーザーはしばらくの間アイドル状態であった後、Azure portal セッションから自動的にサインアウトされます。

**[非アクティブのときにサインアウトする]** の下のドロップダウンを選択します。 アイドル状態になっている場合に Azure portal セッションがサインアウトするまでの時間を選択します。

   ![非アクティブ タイムアウト設定が強調表示されたポータル設定を示すスクリーンショット](./media/set-preferences/inactive-signout-user.png)

変更は自動的に保存されます。 アイドル状態になっている場合は、設定した時間の経過後、Azure portal セッションがサインアウトします。

最大アイドル時間を適用するために、管理者がディレクトリ レベルでこの設定を行うこともできます。 管理者がディレクトリ レベルのタイムアウト設定を行った場合でも、ユーザーが独自の非アクティブ時のサインアウト時間を設定できます。 ディレクトリ レベルの設定よりも短い時間設定を選択してください。

管理者が非アクティブ タイムアウト ポリシーを有効にしている場合は、 **[ディレクトリの非アクティブ タイムアウト ポリシーを上書きする]** チェックボックスをオンにします。 ポリシー設定よりも短い時間間隔を設定します。

   ![ディレクトリの非アクティブ タイムアウトを上書きするポリシー設定が強調表示されたポータル設定を示すスクリーンショット](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 管理者として Azure portal のすべてのユーザーに対して非アクティブ タイムアウト設定を適用する場合は、[Azure portal のユーザーにディレクトリ レベルの非アクティブ タイムアウトを設定する](admin-timeout.md)ことに関するページを参照してください
>

### <a name="choose-your-default-view"></a>既定のビューを選択する 

Azure portal にサインインすると既定で開かれるページを変更できます。

   ![既定のビューが強調表示された Azure portal 設定を示すスクリーンショット](./media/set-preferences/default-view.png)

既定のビューの設定では、サインイン時にどの Azure portal ビューが表示されるかを制御します。 既定で Azure ホームを開くか、ダッシュボード ビューを開くかを選択できます。

* **ホーム**はカスタマイズできません。  よく使用される Azure サービスへのショートカットが表示され、最近使用したリソースが一覧表示されます。 Microsoft Learn や Azure ロードマップなどのリソースへの便利なリンクも表示されます。
* ダッシュボードをカスタマイズし、自分専用に設計されたワークスペースを作成することができます。 たとえば、プロジェクト、タスク、またはロールに焦点を合わせたダッシュボードを作成できます。 **[ダッシュボード]** を選択した場合、既定のビューは最近使用したダッシュボードに移動します。

### <a name="choose-a-portal-menu-mode"></a>ポータルのメニュー モードを選択する

ポータル メニューの既定のモードでは、ページ上でポータル メニューがどれだけの領域を占めるかを制御します。

* ポータル メニューは**ポップアップ** モードの場合、必要になるまで表示されません。 メニュー アイコンを選択して、メニューを開いたり閉じたりします。
* ポータル メニューに対して**ドッキング** モードを選択した場合は、常に表示されます。 メニューを折りたためば、より広い作業領域を確保できます。 

### <a name="choose-a-theme"></a>テーマを選択する

選択するテーマは、Azure portal に表示される背景とフォント色に影響を及ぼします。 4 つのプリセット配色テーマから選択できます。 各サムネイルを選択し、最適なテーマを見つけます。

   ![テーマが強調表示された Azure portal 設定を示すスクリーンショット](./media/set-preferences/theme.png)

ハイコントラスト テーマのいずれかを代わりに選択できます。 ハイコントラストの設定にすると、視覚障碍のあるユーザーにとって Azure portal が読みやすくなり、その他すべてのテーマの選択が上書きされます。 詳細については、[ハイコントラストの有効化やテーマの変更](azure-portal-change-theme-high-contrast.md)に関するページを参照してください。

### <a name="enable-or-disable-pop-up-notifications"></a>ポップアップ通知を有効または無効にする

通知は、現在のセッションに関連するシステム メッセージです。 これらは、たとえば、作成したばかりのリソースが利用可能になったときや、最後のアクションを確認するときに、現在のクレジット残高のような情報を提供します。 ポップアップ通知がオンになっているときには、画面の上隅にメッセージが短時間表示されます。 

ポップアップ通知を有効または無効にするには、 **[ポップアップ通知を有効にする]** チェックボックスをオンまたはオフにします。

   ![ポップアップ通知が強調表示された Azure portal の設定を示すスクリーンショット](./media/set-preferences/popup-notifications.png)

現在のセッション中に受信したすべての通知を読むには、グローバル ヘッダーから **[通知]** を選択します。

   ![通知が強調表示された Azure portal のグローバル ヘッダーを示すスクリーンショット](./media/set-preferences/read-notifications.png)

以前のセッションからの通知を読む場合は、アクティビティ ログでイベントを検索します。 詳細については、「[Azure アクティビティ ログ イベントを表示して取得する](/azure/azure-monitor/platform/activity-log-view)」を参照してください。

### <a name="settings-under-useful-links"></a>便利なリンクの下の設定

Azure portal の設定に変更を加えて、それらを破棄する場合は、 **[既定の設定に戻す]** を選択します。 ポータルの設定に加えた変更はすべて失われます。 このオプションは、ダッシュボードのカスタマイズには影響を与えません。

**[すべての設定をエクスポート]** や **[すべての設定とプライベート ダッシュボードを削除]** の詳細については、「[ユーザー設定のエクスポートまたは削除](azure-portal-export-delete-settings.md)」を参照してください。

## <a name="change-language-and-regional-settings"></a>地域と言語の設定を選択する

Azure portal でテキストをどのように表示するかを制御する設定は 2 つあります。 **[言語]** 設定では、Azure portal 内のテキストが表示される言語を制御します。 **[地域設定]** では、日付、時刻、数値、通貨の表示方法を制御します。

Azure portal で使用される言語を変更するには、ドロップダウンを使用して、使用可能な言語の一覧から選択します。

地域設定を選択すると、選択した言語についてのみ、地域オプションを表示するように変更されます。 自動選択を変更するには、ドロップダウンを使用して、必要な地域設定を選択します。

たとえば、使用する言語として [英語] を選択してから地域設定として [米国] を選択すると、通貨は米ドルで表示されます。 言語として [英語] を選択してから地域設定として [ヨーロッパ] を選択すると、通貨はユーロで表示されます。

**[適用]** を選択し、言語と地域の形式設定を更新します。

   ![言語と地域の形式設定が表示されたスクリーンショット](./media/set-preferences/language.png)

>[!NOTE]
>これらの言語と地域の設定は、Azure portal にのみ影響を与えます。 新しいタブまたはウィンドウで開かれるドキュメント リンクでは、ブラウザーの言語設定を使用して、表示する言語が決定されます。
>

## <a name="next-steps"></a>次のステップ

* [カスタム ダッシュボードの作成と共有](azure-portal-dashboards.md)
* [Azure portal の使い方に関するビデオ シリーズ](azure-portal-video-series.md)
