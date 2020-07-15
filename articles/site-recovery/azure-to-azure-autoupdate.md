---
title: Azure Site Recovery のモビリティ サービスの自動更新
description: Azure Site Recovery を使用した Azure VM のレプリケーションに使用されるモビリティ サービスの自動更新の概要。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135838"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure から Azure へのレプリケーションに使用されるモビリティ サービスの自動更新

Azure Site Recovery では、月 1 回のリリース周期で、問題修正、既存の機能の強化、新機能の追加がなされています。 サービスを最新の状態に維持するには、毎月パッチのデプロイを計画する必要があります。 毎回のアップグレードに伴うオーバーヘッドを回避するために、Site Recovery でコンポーネントの更新を管理することができます。

「[Azure から Azure へのディザスター リカバリー アーキテクチャ](azure-to-azure-architecture.md)」で説明されているように、Mobility Service は、1 つの Azure リージョンから別の Azure リージョンへのレプリケーションが有効になっているすべてのAzure 仮想マシン (VM) にインストールされます。 自動更新を使用すると、毎回の新しいリリースでモビリティ サービスの拡張機能が更新されます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>自動更新の仕組み

Site Recovery を使用して更新を管理する場合、コンテナーと同じサブスクリプションに作成された Automation アカウントを介して、グローバル Runbook (Azure サービスによって使用されます) がデプロイされます。 各コンテナーが 1 つの Automation アカウントを使用します。 コンテナー内の各 VM について、Runbook はアクティブな自動更新があるかどうかを確認します。 新しいバージョンの Mobility Service の拡張機能が使用可能な場合は、更新プログラムがインストールされます。

Runbook の既定のスケジュールは、レプリケートされる VM の地域のタイム ゾーンで毎日午前 12 時に実行されます。 Runbook のスケジュールは、Automation アカウントを介して変更することもできます。

> [!NOTE]
> [更新プログラム ロールアップ 35](site-recovery-whats-new.md#updates-march-2019) 以降では、更新に使用するために既存の Automation アカウントを選択できます。 更新プログラム ロールアップ 35 より前では、既定で Site Recovery によって Automation アカウントが作成されていました。 このオプションは、VM に対してレプリケーションを有効にする場合にのみ選択できます。 レプリケーションが既に有効になっている VM では使用できません。 選択した設定は、同じコンテナーで保護されているすべての Azure VM に適用されます。

自動更新を有効にしても、Azure VM を再起動する必要はないため、実行中のレプリケーションに影響しません。

Automation アカウントのジョブ料金は、1 か月のジョブ実行時間の分数に基づいて請求されます。 ジョブの実行に必要なのは毎日数秒から約 1 分ですので、無料ユニットでカバーできます。 以下の表に示すように、既定では 500 分が Automation アカウントの無料ユニットとして含まれています。

| 含まれる無料ユニット (毎月) | Price |
|---|---|
| ジョブ実行時間 500 分 | ₹0.14/分

## <a name="enable-automatic-updates"></a>自動更新を有効にする

Site Recovery で拡張機能の更新を管理するには、いくつかの方法があります。

- [レプリケーションを有効にする手順の一環として管理する](#manage-as-part-of-the-enable-replication-step)
- [コンテナー内の拡張機能の更新設定の切り替え](#toggle-the-extension-update-settings-inside-the-vault)
- [更新を手動で管理する](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>レプリケーションを有効にする手順の一環として管理する

[VM ビューから](azure-to-azure-quickstart.md)、または [Recovery Services コンテナーから ](azure-to-azure-how-to-enable-replication.md)VM のレプリケーションを有効にするときに、Site Recovery 拡張機能の更新を Site Recovery で管理するか、手動で管理するかを選択できます。

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="拡張機能の設定":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>コンテナー内の拡張機能の更新設定の切り替え

1. Recovery Services コンテナーで、 **[管理]**  >  **[Site Recovery インフラストラクチャ]** に移動します。
1. **[For Azure Virtual Machines]\(Azure 仮想マシン用\)**  >  **[拡張機能の更新の設定]**  >  **[Site Recovery に管理を許可]** で、 **[オン]** を選択します。

   拡張機能を手動で管理するには、 **[オフ]** を選択します。

1. **[保存]** を選択します。

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="拡張機能の更新の設定":::

> [!IMPORTANT]
> **[Site Recovery に管理を許可]** を選択した場合は、コンテナー内のすべての VM にこの設定が適用されます。

> [!NOTE]
> どちらのオプションでも、更新の管理に使用される Automation アカウントが通知されます。 コンテナーでこの機能を初めて使用する場合は、既定で新しい Automation アカウントが作成されます。 あるいは、この設定をカスタマイズして、既存の Automation アカウントを選択できます。 同じコンテナー内でレプリケーションを有効にするための後続のすべてのタスクでは、以前に作成された Automation アカウントが使用されます。 現在、ドロップダウン メニューには、コンテナーと同じリソース グループ内にある Automation アカウントのみが表示されます。

### <a name="manage-updates-manually"></a>更新を手動で管理する

1. VM にインストールされているモビリティ サービスに新しい更新プログラムがある場合、次の通知が表示されます。**Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。クリックしてインストールしてください。**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="[レプリケートされたアイテム] ウィンドウ":::

1. この通知を選択して、VM の選択ページを開きます。
1. アップグレードする VM を選択してから、 **[OK]** を選択します。 選択した VM ごとに、モビリティ サービスの更新が開始されます。

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="[レプリケートされたアイテム] VM リスト":::

## <a name="common-issues-and-troubleshooting"></a>一般的な問題とトラブルシューティング

自動更新で問題が発生した場合は、コンテナー ダッシュボードの **[構成の問題]** にエラー通知が表示されます。

自動更新を有効にできない場合は、次の一般的なエラーと推奨される操作を参照してください。

- **Error**: Azure 実行アカウント (サービス プリンシパル) を作成し、サービス プリンシパルに共同作成者ロールを付与するためのアクセス許可がありません。

  **推奨される操作:** サインインしたアカウントが共同作成者に割り当てられていることを確認してから、再試行します。 アクセス許可の割り当ての詳細については、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)」のガイダンスに従って、サービス プリンシパルを作成します。

  自動更新を有効にした後に発生する問題の大半を修正するには、 **[修復]** を選択します。 [修復] ボタンが使用できない場合は、拡張機能の更新設定ウィンドウに表示されているエラー メッセージを確認してください。

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="拡張機能の更新設定に表示される Site Recovery サービスの [修復] ボタン":::

- **Error**: 実行アカウントに Recovery Services リソースにアクセスするためのアクセス許可がありません。

  **推奨される操作:** 実行アカウントを削除してから[再作成](../automation/manage-runas-account.md)します。 または、Automation 実行アカウントの Azure Active Directory アプリケーションが Recovery Services リソースにアクセスできることを確認します。

- **Error**: 実行アカウントが見つかりません。 Azure Active Directory アプリケーション、サービス プリンシパル、ロール、Automation 証明書資産、Automation 接続資産のいずれかが削除されたか、作成されていません。または、証明書と接続の拇印が一致しません。

  **推奨される操作:** 実行アカウントを削除してから[再作成](../automation/manage-runas-account.md)します。

- **Error**: Automation アカウントで使用されている Azure 実行証明書の有効期限がまもなく切れます。

  実行アカウント用に作成された自己署名証明書は、作成日から 1 年後に有効期限が切れます。 期限切れになる前に、いつでも書き換えることができます。 メール通知にサインアップしている場合は、あなたの側でのアクションが必要なときにもメールを受信します。 このエラーは、有効期限日の 2 か月前に表示され、証明書の有効期限が切れた場合は重大なエラーに変わります。 証明書の期限が切れると、それを更新するまで自動更新は機能しません。

  **推奨される操作:** この問題を解決するには、 **[修復]** をクリックし、次に **[証明書の更新]** をクリックします。

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="証明書の更新":::

  > [!NOTE]
  > 証明書を更新した後、ページを更新して現在の状態を表示します。
