---
title: Azure portal の設定を管理する
description: 既定のサブスクリプション/ディレクトリ、タイムアウト、メニュー　モード、コントラスト、テーマ、通知、言語/地域など、Azure portal の設定を変更します。
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741315"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Azure portal の設定を管理する

独自の基本設定を実現するために Azure portal の既定の設定を変更できます。

ほとんどの設定は、グローバル ページ ヘッダーの右上のセクションにある **[設定]** メニューから使用できます。

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="グローバル ページ ヘッダーの設定アイコンを示すスクリーンショット。":::

> [!NOTE]
> 現在、すべてのユーザーを、このトピックで説明している最新の設定エクスペリエンスに移行中です。 以前のエクスペリエンスの詳細については、「[Azure portal の設定を管理する (旧バージョン)](original-preferences.md)」を参照してください。

## <a name="directories--subscriptions"></a>[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)

**[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページでは、ディレクトリの管理とサブスクリプション フィルターの設定を行うことができます。

### <a name="switch-and-manage-directories"></a>ディレクトリの切り替えおよび管理

**[ディレクトリ]** セクションに、(現在サインインしている) **現在のディレクトリ** が表示されます。

**[スタートアップ ディレクトリ]** に、Azure portal にサインインしたときの既定のディレクトリが表示されます。 別のスタートアップ ディレクトリを選択するには、 **[変更]** を選択して、[[Appearance + startup views]\(外観 + スタートアップ表示\)](#appearance--startup-views) ページに移動します。ここでこのオプションを変更できます。

アクセスできるディレクトリの完全な一覧を表示するには、 **[すべてのディレクトリ]** を選択します。

ディレクトリをお気に入りとしてマークするには、その星型アイコンを選択します。 それらのディレクトリが **[お気に入り]** セクションに一覧表示されます。

別のディレクトリに切り替えるには、作業するディレクトリを選択し、その行の **[切り替え]** ボタンを選択します。

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="[ディレクトリ] 設定ウィンドウが表示されているスクリーンショット。":::

### <a name="subscription-filters"></a>サブスクリプション フィルター

Azure portal にサインインするときに、既定でフィルター処理されるサブスクリプションを変更できます。 これは、主に使用するサブスクリプションのリストがある一方で、他のものも時々使用する場合に役立つことがあります。

カスタマイズされたフィルターを使用するには、 **[高度なフィルター]** を選択します。 続行する前に、確認を求めるプロンプトが表示されます。

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="[高度なフィルター] の確認ダイアログ ボックスが表示されているスクリーンショット。":::

これで **[高度なフィルター]** ページが有効になり、複数のサブスクリプション フィルターを作成して管理できます。 現在選択されているサブスクリプションは、再使用可能なインポート済みフィルターとして保存されます。 高度なフィルターの使用を停止する場合は、トグルをもう一度選択して、既定のサブスクリプション ビューに戻します。 作成したカスタム フィルターはすべて保存され、今後 **[高度なフィルター]** を有効にすると使用できるようになります。

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="[高度なフィルター] を無効にするための確認ダイアログ ボックスが表示されているスクリーンショット。":::

## <a name="advanced-filters"></a>高度なフィルター

**[高度なフィルター]** ページでは、サブスクリプション フィルターを作成、変更、削除できます。

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="[高度なフィルター] スクリーンが表示されているスクリーンショット。":::

**[既定]** のフィルターには、アクセス可能なすべてのサブスクリプションが表示されます。 このフィルターは、他のフィルターがない場合、またはアクティブなフィルターによってサブスクリプションを含めることができない場合に使用されます。

**[Imported-filter]\(インポートされたフィルター\)** という名前のフィルターも表示されていることが分かります。これには、以前に選択されたすべてのサブスクリプションが含まれています。

現在使用されているフィルターを変更するには、 **[高度なフィルター]** ドロップダウン ボックスからそのフィルターを選択します。 また、 **[Modify advanced filters]\(高度なフィルターを変更する\)** を選択して **[高度なフィルター]** ページに移動し、そこでフィルターを作成、変更、削除できます。

### <a name="create-a-filter"></a>フィルターを作成する

新しいフィルターを作成するには、 **[Create a filter]\(フィルターを作成する\)** を選択します。 最大 10 個のフィルターを作成できます。

各フィルターには、8 から 50 文字の一意の名前を指定する必要があります。名前には、文字、数字、ハイフンのみを含める必要があります。

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="[Create a filter]\(フィルターを作成する\) オプションが表示されているスクリーンショット。":::

フィルターに名前を付けたら、少なくとも 1 つの条件を入力します。 **[フィルターの種類]** フィールドで、 **[サブスクリプション名]** 、 **[サブスクリプション ID]** 、または **[Subscription state]\(サブスクリプションの状態\)** のいずれかを選択します。 次に、演算子を選択し、フィルター処理する値を入力します。

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="フィルター作成用の演算子の一覧を示すスクリーンショット。":::

条件の追加が完了したら、 **[追加]** を選択します。 フィルターが **[Active filters]\(アクティブなフィルター\)** の一覧に表示されます。

### <a name="modify-or-delete-a-filter"></a>フィルターを変更または削除する

既存のフィルターを変更または名前変更するには、そのフィルターの行で鉛筆アイコンを選択します。 変更を行ってから、 **[適用]** を選択します。

> [!NOTE]
> 現在アクティブなフィルターを変更し、その変更によってサブスクリプションが 0 になる場合は、代わりに **[既定]** のフィルターがアクティブになります。 サブスクリプションをまったく含まないフィルターをアクティブにすることはできません。

フィルターを削除するには、そのフィルターの行でごみ箱アイコンを選択します。 **[既定]** のフィルターと現在アクティブなフィルターはいずれも削除できません。

## <a name="appearance--startup-views"></a>[Appearance + startup views]\(外観 + スタートアップ表示\)

**[Appearance + startup views]\(外観 + スタートアップ表示\)** ウィンドウには 2 つのセクションがあります。 **[外観]** セクションでは、メニューの動作、配色テーマ、ハイ コントラストのテーマを使用するかどうかを選択できます。また、 **[Startup views]\(スタートアップ表示\)** セクションでは、Azure portal への初回サインイン時に表示される内容に関するオプションを設定できます。

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="[Appearance + startup views]\(外観 + スタートアップ表示\) の [外観] セクションを表示するスクリーンショット。":::

### <a name="set-menu-behavior"></a>メニューの動作を設定する

**[Menu behavior]\(メニューの動作\)** セクションでは、既定の Azure portal メニューの動作方法を選択できます。

- **ポップアップ**: メニューは、必要になるまで非表示になります。 左上隅のメニューアイコンを選択して、メニューを開いたり閉じたりすることができます。
- **ドッキング**: メニューは常に表示されます。 メニューを折りたためば、より広い作業領域を確保できます。

### <a name="choose-a-theme-or-enable-high-contrast"></a>テーマを選択するか、ハイ コントラストを有効にする

選択するテーマは、Azure portal に表示される背景とフォント色に影響を及ぼします。 **[テーマ]** セクションでは、4 つのプリセット配色テーマのいずれかを選択できます。 各サムネイルを選択し、最適なテーマを見つけます。

または、 **[ハイ コントラストのテーマ]** セクションからテーマを選択できます。 これらのテーマを使用すると、特に視覚障害がある場合に、Azure portal を読みやすくすることができます。 白または黒のいずれかのハイ コントラストのテーマを選択すると、その他のテーマの選択がオーバーライドされます。

### <a name="startup-page"></a>スタートアップ ページ

初めて Azure portal にサインインしたときに表示されるページに対して、次のいずれかのオプションを選択します。

- **ホーム**: 人気のある Azure サービスへのショートカット、最近使用したリソースの一覧、ツールやドキュメントなどへの便利なリンクが含まれたホーム ページが表示されます。
- **ダッシュボード**: 直前に使用したダッシュボードが表示されます。 ダッシュボードをカスタマイズし、自分専用に設計されたワークスペースを作成することができます。 たとえば、プロジェクト、タスク、またはロールに焦点を合わせたダッシュボードを作成できます。 詳細については、「[Azure Portal でのダッシュボードの作成と共有](azure-portal-dashboards.md)」を参照してください。

### <a name="startup-directory"></a>スタートアップ ディレクトリ

初めて Azure portal にサインインしたときに作業するディレクトリに対して、次のいずれかのオプションを選択します。

- **[最後にアクセスしたディレクトリにサインインする]** : Azure portal にサインインすると、最後に作業していたディレクトリから開始します。
- **[ディレクトリを選ぶ]** : いずれかのディレクトリを選択するには、このオプションを選択します。 最後に別のディレクトリで作業していた場合でも、Azure portal にサインインするたびに、そのディレクトリから開始します。

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="[Appearance + startup views]\(外観 + スタートアップ表示\) の [スタートアップ] セクションを表示するスクリーンショット。":::

## <a name="language--region"></a>Language + region (言語 + 地域)

日付や通貨などのデータの Azure portal での表示方法に影響を与える言語と地域設定を選択します。

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="[Language + region]\(言語 + 地域\) の設定ページを示すスクリーンショット。":::

> [!NOTE]
> これらの言語と地域の設定は、Azure portal にのみ影響を与えます。 新しいタブまたはウィンドウで開かれるドキュメント リンクでは、ブラウザーの設定が使用され、表示する言語が決定されます。

### <a name="language"></a>言語

ドロップダウン リストを使用して、使用可能な言語の一覧から選択します。 この設定は、Azure portal 全体でテキストが表示される言語を制御します。

### <a name="regional-format"></a>地域の形式

Azure portal での日付、時刻、数値、通貨の表示方法を制御するオプションを選択します。

**[地域設定]** ドロップダウン リストに表示されるオプションは、 **[言語]** に対して選択したオプションに基づいて変化します。 たとえば、使用する言語として **[英語]** を選択してから、地域設定として **[英語 (米国)]** を選択すると、通貨は米ドルで表示されます。 言語として **[英語]** を選択してから地域設定として **[英語 (ヨーロッパ)]** を選択すると、通貨はユーロで表示されます。

**[適用]** を選択し、言語と地域の形式設定を更新します。

## <a name="my-information"></a>My information (本人情報)

**[My information]\(本人情報\)** ページでは、Azure サービス、課金、サポート、セキュリティの問題に関する更新に使用されるメール アドレスを更新できます。 また、Microsoft Azure や他の製品およびサービスに関するその他のメール アドレスからオプトイン/アウトすることもできます。

**[My information]\(本人情報\)** ページの上部付近に、設定をエクスポート、復元、削除するためのオプションが表示されます。

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="[My information]\(本人情報\) 設定ページのスクリーンショット。":::

### <a name="export-user-settings"></a>ユーザー設定をエクスポートする

カスタム設定に関する情報は、Azure に格納されます。 次のユーザー データをエクスポートできます。

- Azure portal のプライベート ダッシュボード
- お気に入りのサブスクリプションまたはディレクトリなどのユーザーの設定
- テーマとその他のカスタム ポータル設定

設定を削除する予定の場合は、それをエクスポートして確認することをお勧めします。 ダッシュボードの再構築や設定のやり直しには、時間がかかる場合があります。

ポータル設定をエクスポートするには、設定の **[概要]** ウィンドウの上部から **[設定のエクスポート]** を選択します。 これにより、ユーザー設定データを含む *.json* ファイルが作成されます。

ユーザー設定の動的な性質とデータが破損するリスクのため、*.json* ファイルから設定をインポートすることはできません。

### <a name="restore-default-settings"></a>既定の設定に戻す

Azure portal の設定に変更を加えた場合に、それらの変更を破棄するには、設定の **[概要]** ウィンドウの上部から **[既定の設定に戻す]** を選択します。 この操作を確認するプロンプトが表示されます。 これを実行すると、Azure portal の設定に対して行った変更はすべて失われます。 このオプションは、ダッシュボードのカスタマイズには影響しません。

### <a name="delete-user-settings-and-dashboards"></a>ユーザー設定とダッシュボードを削除する

カスタム設定に関する情報は、Azure に格納されます。 次のユーザー データを削除できます。

- Azure portal のプライベート ダッシュボード
- お気に入りのサブスクリプションまたはディレクトリなどのユーザーの設定
- テーマとその他のカスタム ポータル設定

設定を削除する前に、エクスポートして確認するのはよいことです。 [ダッシュボード](azure-portal-dashboards.md)の再構築やカスタム設定のやり直しには、時間がかかる場合があります。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

ポータルの設定を削除するには、 **[My information]\(本人情報\)** ページの上部から **[すべての設定とプライベート ダッシュボードを削除]** を選択します。 削除を確認するプロンプトが表示されます。 これを実行すると、すべての設定のカスタマイズが既定の設定に戻り、すべてのプライベート ダッシュボードが失われます。

## <a name="signing-out--notifications"></a>Signing out + notifications (サインアウト + 通知)

このウィンドウでは、ポップアップ通知とセッション タイムアウトを管理できます。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="[Signing out + notifications]\(サインアウト + 通知\) ウィンドウを示すスクリーンショット。":::

### <a name="signing-out"></a>サインアウト

非アクティブ タイムアウト設定は、ワークステーションのセキュリティ保護を忘れた場合にリソースを未承認のアクセスから保護するのに役立ちます。 ユーザーはしばらくの間アイドル状態であった後、Azure portal セッションから自動的にサインアウトされます。 個人として、自分でタイムアウト設定を変更することができます。 管理者の場合は、ディレクトリ内のすべてのユーザーに対してディレクトリ レベルで設定できます。

### <a name="change-your-individual-timeout-setting-user"></a>個々のタイムアウト設定を変更する (ユーザー)

**[非アクティブなときにサインアウトする]** の横にあるドロップダウン メニューで、アイドル状態の場合に Azure portal セッションがサインアウトされるまでの期間を選択します。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="ユーザー タイムアウト設定オプションを示すスクリーンショット。":::

**[適用]** を選択して変更を保存します。 その後、ポータル セッション中にユーザーが非アクティブの場合に、Azure portal は設定された期間が経過するとサインアウトします。

管理者が非アクティブ タイムアウト ポリシーを有効にしている場合でも、ディレクトリ レベルの設定よりも短い限り、独自の設定を行うことができます。 これを行うには、 **[ディレクトリの非アクティブ タイムアウト ポリシーをオーバーライドする]** を選択してから、 **[Override value]\(オーバーライド値\)** に時間間隔を入力します。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="ディレクトリの非アクティブ タイムアウトのオーバーライド設定を示すスクリーンショット。":::

### <a name="change-the-directory-timeout-setting-admin"></a>ディレクトリ タイムアウト設定を変更する (管理者)

[グローバル管理者ロール](../active-directory/roles/permissions-reference.md#global-administrator)の管理者は、セッションがサインアウトされるまでの最大アイドル時間を適用できます。この非アクティブ タイムアウト設定は、ディレクトリ レベルで適用されます。 この設定は、新しいセッションに対して有効になります。 既にサインインしているユーザーには、すぐには適用されません。 ディレクトリの詳細については、「[Active Directory Domain Services の概要](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。

グローバル管理者として、Azure portal のすべてのユーザーに対してアイドル タイムアウト設定を適用する場合は、 **[Enable directory level idle timeout]\(ディレクトリ レベルのアイドル タイムアウトを有効にする\)** を選択して、この設定をオンにします。 次に、ユーザーが非アクティブになってからそのセッションが自動的にサインアウトされるまでの最大時間を **[時間]** と **[分]** に入力します。 **[適用]** を選択すると、この設定はディレクトリ内のすべてのユーザーに適用されます。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="ディレクトリ レベルのアイドル タイムアウト オプションを示すスクリーンショット。":::

非アクティブ タイムアウト ポリシーが正しく設定されていることを確認するには、グローバル ページ ヘッダーから **[通知]** を選択し、成功通知が表示されていることを確認します。

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="非アクティブ タイムアウト ポリシーが成功した場合の通知を示すスクリーンショット。":::

### <a name="enable-or-disable-pop-up-notifications"></a>ポップアップ通知を有効または無効にする

通知は、現在のセッションに関連するシステム メッセージです。 現在のクレジット残高の表示、最後のアクションの確認、作成したリソースの状態の通知など、各種情報が提供されます。 ポップアップ通知がオンになっているときには、画面の上隅にメッセージが短時間表示されます。

ポップアップ通知を有効または無効にするには、 **[ポップアップ通知を有効にする]** をオンまたはオフにします。

現在のセッション中に受信したすべての通知を読むには、グローバル ヘッダーから **[通知]** を選択します。

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="グローバル ヘッダーの [通知] アイコンを示すスクリーンショット。":::

以前のセッションからの通知を表示するには、アクティビティ ログでイベントを検索します。 詳細については、「[アクティビティ ログを表示する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure portal のキーボード ショートカットについて学習する](azure-portal-keyboard-shortcuts.md)
- [サポートされているブラウザーとデバイスを表示する](azure-portal-supported-browsers-devices.md)
- [お気に入りの追加、削除、並べ替え](azure-portal-add-remove-sort-favorites.md)
- [カスタム ダッシュボードの作成と共有](azure-portal-dashboards.md)
- [Azure portal のハウツービデオを視聴する](azure-portal-video-series.md)
