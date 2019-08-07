---
title: Azure DevTest Labs で VM の自動シャットダウン設定を構成する | Microsoft Docs
description: 仮想マシン (VM) が使用されていないときに自動的にシャットダウンされるように、VM の自動シャットダウン設定を構成する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361275"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Azure DevTest Labs で VM の自動シャットダウン設定を構成する
Azure DevTest Labs では、各ラボのポリシー (設定) を管理することで、ラボのコストを制御し、無駄を最小限に抑えることができます。 この記事では、ラボ アカウントに対して自動シャットダウン ポリシーを構成する方法、およびラボ アカウントのラボに対して自動シャットダウンの設定を構成する方法を示します。 すべてのラボ ポリシーを設定する方法については、[Azure DevTest Labs でのラボ ポリシーの定義](devtest-lab-set-lab-policy.md)に関する記事をご覧ください。  

## <a name="autoshutdown-policy-for-a-lab"></a>ラボの自動シャットダウン ポリシー
ラボ所有者は、ラボ内のすべての VM のシャットダウン スケジュールを構成できます。 これを行うことで、使用中ではない (アイドル状態の) マシンを実行するコストを節約できます。 すべてのラボの VM 上にあるシャットダウン ポリシーを一元的に適用できるだけでなく、ラボ ユーザーが個々のマシンのスケジュールを設定する手間もかかりません。 この機能を利用して、制御権なしから完全な制御権の提供まで、ラボ ユーザーにラボ スケジュール上でのポリシーを設定できます。 ラボ所有者は、次の手順を実行してこのポリシーを構成できます。

1. ラボの [ホーム] ページで、 **[構成とポリシー]** を選択します。
2. 左側のメニューの **[スケジュール]** セクションにある **[自動シャットダウン ポリシー]** を選択します。
3. いずれかのオプションを選択します。 以下のセクションでは、これらのオプションについてさらに詳しく説明します。設定されたポリシーは、ラボで新しく作成される VM に対してのみ適用され、既存の VM には適用されません。 

    ![自動シャットダウン ポリシーのオプション](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>ラボの自動シャットダウン設定を構成する
自動シャットダウン ポリシーでは、このラボの VM をシャットダウンする時刻を指定できるので、ラボの無駄を最小限に抑えるのに役立ちます。

ラボのポリシーを表示 (および変更) するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。   
4. **[Configuration and policies (構成とポリシー)]** を選択します。

    ![ポリシー設定ウィンドウ](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. ラボの **[構成とポリシー]** ウィンドウで、 **[スケジュール]** の **[自動シャットダウン]** を選択します。
   
    ![自動シャットダウン](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。
7. このポリシーを有効にした場合は、現在のラボのすべての VM をシャットダウンする時刻 (およびタイム ゾーン) を指定します。
8. 指定した自動シャットダウン時刻の 15 分前に通知を送信するオプションに対して、 **[はい]** または **[いいえ]** を指定します。 **[はい]** を選択した場合は、通知が投稿または送信される Webhook URL のエンドポイントまたは電子メール アドレスを入力します。 ユーザーは通知を受信し、シャットダウンを遅らせるオプションが表示されます。 詳しくは、「[通知](#notifications)」セクションをご覧ください。 
9. **[保存]** を選択します。

    このポリシーを有効にすると、既定では現在のラボのすべての VM にこのポリシーが適用されます。 この設定を特定の VM から削除するには、その VM の管理ウィンドウを開いて、 **[自動シャットダウン]** の設定を変更します。

## <a name="configure-autoshutdown-settings-for-a-vm"></a>VM の自動シャットダウン設定を構成する

### <a name="user-sets-a-schedule-and-can-opt-out"></a>ユーザーがスケジュールを設定し、オプトアウトできる
このポリシー オプションがラボに設定されている場合、ユーザーはラボ スケジュールをオーバーライドまたはオプトアウトできます。 このオプションでは、VM の自動シャットダウン スケジュールの完全な制御権をラボ ユーザーに付与します。 ラボ ユーザーの VM 自動シャットダウン スケジュールのページには、変更は表示されません。

![自動シャットダウン ポリシーのオプション - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>ユーザーがスケジュールを設定し、オプトアウトできない
このポリシー オプションがラボに設定されている場合、ユーザーはラボ スケジュールをオーバーライドできます。 ただし、自動シャットダウン ポリシーは無効にできません。 このオプションでは、ラボ内のすべてのマシンが、自動シャットダウン スケジュール下にあることを保証します。 ラボ ユーザーは、VM の自動シャットダウン スケジュールを更新して、シャットダウンの通知を設定できます。

![自動シャットダウン ポリシーのオプション - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>ユーザーはラボ管理者が設定したスケジュールを制御できない
このポリシー オプションがラボに設定されている場合、ユーザーはラボ スケジュールをオーバーライドまたはオプトアウトできません。 このオプションでは、ラボ内のすべてのマシンのスケジュールに対する完全な制御権をラボ管理者に付与します。 ラボ ユーザーは、各自の VM の自動シャットダウンの通知を設定できるだけです。

![自動シャットダウン ポリシーのオプション - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>通知
ラボ所有者が自動シャットダウンを設定すると、影響を受ける VM のユーザーに対し、自動シャットダウンがトリガーされる 15 分前に通知が送信されます。 このオプションにより、ラボ ユーザーはシャットダウンされる前に作業を保存する機会があります。 また、通知では、各 VM に対して次のアクションのためのリンクが提供されます。

- この時間の自動シャットダウンをスキップします
- VM での作業を続けられるように、自動シャットダウンを 1 時間または 2 時間だけ一時停止します。

通知は、構成されている Web hook エンドポイントを介して、または自動シャットダウンの設定でラボ所有者によって指定されたメール アドレスに、送信されます。 Webhook を使用すると、特定のイベントをサブスクライブする統合を構築または設定できます。 これらのイベントのいずれかがトリガーされると、DevTest Labs では、Webhook で構成されている URL に HTTP POST ペイロードが送信されます。 webhook の詳細については、「[webhook または API Azure Function を作成する](../azure-functions/functions-create-a-web-hook-or-api-function.md)」を参照してください。 

さまざまなアプリ (たとえば、Slack、Azure Logic Apps など) で広範にサポートされていて、独自の通知送信方法を実装できるため、Web hook を使うことをお勧めします。 Azure Logic Apps を使用してメールから自動シャットダウン通知を受信する例については、「[メール通知を受信するロジック アプリを作成する](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications)」を参照してください。 


## <a name="next-steps"></a>次の手順
「[Azure DevTest Labs でラボの自動シャットダウン ポリシーを管理する](devtest-lab-auto-shutdown.md)」を参照してください
