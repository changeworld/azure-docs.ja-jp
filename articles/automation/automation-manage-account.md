---
title: Azure Automation アカウントを管理する
description: この記事では、証明書の更新、削除、誤った構成など、Automation アカウントの構成を管理する方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be1b35d2e7dc3d3e2efab825f318983e2943b0d2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="manage-azure-automation-account"></a>Azure Automation アカウントを管理する
証明書は、Automation アカウントの有効期限が切れる前のどこかの時点で更新する必要があります。 また、実行アカウントが侵害されたと思われる場合は、実行アカウントを削除して再作成することができます。 このセクションでは、これらの操作を実行する方法について説明します。

## <a name="self-signed-certificate-renewal"></a>自己署名証明書の書き換え
実行アカウント用に作成された自己署名証明書は、作成日から 1 年後に有効期限が切れます。 期限切れになる前に、いつでも書き換えることができます。 証明書を書き換えるとき、実行待ちまたは実行中の Runbook が悪影響を受けないようにすると共に、その実行アカウントでの認証に支障をきたさないよう、現行の有効な証明書は保持されます。 証明書は、有効期限日を迎えるまで存在し続けます。

> [!NOTE]
> エンタープライズ証明機関によって発行された証明書を使用するように Automation 実行アカウントを構成している場合、このオプションを使用すると、そのエンタープライズ証明書は自己署名証明書に置き換えられます。

証明書を書き換えるには、次の手順に従います。

1. Azure Portal で Automation アカウントを開きます。

2. **[Automation アカウント]** で、 
3. **[アカウントのプロパティ]** ウィンドウの **[アカウントの設定]** で **[実行アカウント]** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/automation-manage-account/automation-account-properties-pane.png)
3. **実行アカウント**のプロパティ ページで、証明書を書き換える実行アカウントまたはクラシック実行アカウントを選択します。

4. 選択したアカウントの **[プロパティ]** ウィンドウで、**[証明書の書き換え]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除
このセクションでは、実行アカウントまたはクラシック実行アカウントを削除して再作成する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 削除した実行アカウントまたはクラシック実行アカウントは、Azure Portal で再作成できます。

1. Azure Portal で Automation アカウントを開きます。

2. **[Automation アカウント]** ページで **[実行アカウント]** を選択します。

3. **実行アカウント**のプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。 次に、選択したアカウントの **[プロパティ]** ウィンドウで **[削除]** をクリックします。

 ![Azure 実行アカウントを削除する](media/automation-manage-account/automation-account-delete-runas.png)

4. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

5. 削除したアカウントは、**実行アカウント**のプロパティ ページで再作成できます。このとき、作成オプションとして **[Azure 実行アカウント]** を選択します。

 ![Automation 実行アカウントの再作成](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>誤った構成
実行アカウントまたはクラシック実行アカウントが正しく機能するうえで必要な構成項目が削除されたり、初期セットアップ中に適切に作成されなかったりすることがあります。 たとえば、次のような項目です。

* 証明書資産
* 接続資産
* 共同作業者ロールからの実行アカウントの削除
* Azure AD のサービス プリンシパルまたはアプリケーション

このような誤った構成については、Automation アカウントによって変更が検出され、対応する**実行アカウント**のプロパティ ウィンドウに *[不完全]* 状態として表示されます。

![実行アカウントの構成が不完全な状態](media/automation-manage-account/automation-account-runas-incomplete-config.png)

この実行アカウントを選択すると、アカウントの **[プロパティ]** ウィンドウに次のエラー メッセージが表示されます。

![実行構成が不完全であることを示す警告メッセージ](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png)が必要です。

こうした実行アカウントの問題は、アカウントを削除してから再作成すればすぐに解決できます。

## <a name="next-steps"></a>次の手順
* サービス プリンシパルの詳細については、 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)に関するページを参照してください。
* Azure Automation におけるロールベースのアクセス制御の詳細については、「 [Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* 証明書と Azure サービスの詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。