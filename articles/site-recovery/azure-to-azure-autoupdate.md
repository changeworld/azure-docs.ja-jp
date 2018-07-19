---
title: Azure から Azure へのディザスター リカバリーのモビリティ サービスの自動更新 | Microsoft Docs
description: Azure Site Recovery を使用した Azure VM のレプリケーションに使用されるモビリティ サービスの自動更新の概要を説明します。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 81dbb61d696da84febc89563f946581315fdf527
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37922750"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure から Azure へのレプリケーションに使用されるモビリティ サービスの自動更新

Azure Site Recovery は、月 1 回のリリース周期で、既存の機能の機能強化や新機能が追加され、既知の問題がある場合は修正されます。 そのため、サービスを最新の状態に維持するには、月 1 回の周期に合わせてこれらのパッチのデプロイを計画する必要があります。 アップグレードに伴うオーバーヘッドを回避するために、ユーザーは代わりに Site Recovery でコンポーネントの更新を管理することもできます。 Azure から Azure へのディザスター リカバリーのための[アーキテクチャー リファレンス](azure-to-azure-architecture.md)で詳しく説明されているように、モビリティ サービスはレプリケーションが有効なすべての Azure 仮想マシンにインストールされ、仮想マシンを別の Azure リージョンにレプリケートします。 自動更新を有効にすると、モビリティ サービスの拡張機能は、新しいリリースが発行されるたびに更新されます。 このドキュメントでは、次について詳しく説明します。

- 自動更新のしくみ
- 自動更新を有効にする
- 一般的な問題とトラブルシューティング
 
## <a name="how-does-automatic-update-work"></a>自動更新のしくみ

Site Recovery で更新を管理できるようにすると、コンテナーと同じサブスクリプションに作成された Automation アカウントを介してグローバル Runbook (Azure サービスによって使用されます) がデプロイされます。 特定のコンテナーに対して 1 つの Automation アカウントが使用されます。 この Runbook は、自動更新がオンになっているコンテナー内の各 VM をチェックし、新しいバージョンが利用可能であれば、モビリティ サービス拡張機能のアップグレードを開始します。 Runbook の既定のスケジュールでは、レプリケートされた仮想マシンの geo のタイム ゾーンに従って毎日午前 12 時に実行されます。 Runbook のスケジュールは、必要に応じて、ユーザーが Automation アカウントを介して変更することもできます。 

> [!NOTE]
> 自動更新を有効しても、Azure VM を再起動する必要はないため、実行中のレプリケーションに影響しません。

> [!NOTE]
> Automation アカウントによって使われるジョブの請求は、1 か月間に使われたジョブ実行時間の分数に基づき、既定では Automation アカウントの無料ユニットとして 500 分が含まれます。 1 日あたりのジョブの実行は、**数秒から 1 分程度**であり、**無料クレジットの範囲内**です。

含まれている無料ユニット (1 か月あたり)**   価格ジョブ実行時間    500 分 ₹0.14/分

## <a name="enable-automatic-updates"></a>自動更新を有効にする

Site Recovery による更新の管理は次の方法で選択できます。

- [レプリケーションを有効にする手順の一環として](#as-part-of-the-enable-replication-step)
- [コンテナー内の拡張機能の更新設定の切り替え](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>レプリケーションを有効にする手順の一環として:

[仮想マシン ビューから](azure-to-azure-quickstart.md)、または [Recovery Services コンテナーから](azure-to-azure-how-to-enable-replication.md)仮想マシンのレプリケーションを有効にするときに、Site Recovery 拡張機能の更新を Site Recovery で 管理するか、手動で管理するかを選択できます。

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>コンテナー内の拡張機能の更新設定の切り替え

1. コンテナー内で、**[管理]** -> **[Site Recovery インフラストラクチャ]** の順に移動します。
2. **[For Azure virtual Machines]\(Azure 仮想マシン\)** -> **[Extension Update Settings]\(拡張機能の更新設定\)** で、トグルをクリックして、"*更新を ASR で管理する*" か、"*手動で管理する*" かを選択します。 **[Save]** をクリックします。

![vault-toggle-autuo-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> "*ASR での管理*" を選択した場合は、対応するコンテナー内のすべての仮想マシンに設定が適用されます。


> [!Note] 
> どちらのオプションでも、更新の管理に使用される Automation アカウントが通知されます。 コンテナーでこの機能を初めて有効にする場合は、新しい Automation アカウントが作成されます。 それ以降は、同じコンテナー内でレプリケーションを有効にすると、以前に作成されたアカウントが使用されます。

## <a name="common-issues--troubleshooting"></a>一般的な問題とトラブルシューティング

自動更新で問題が発生した場合は、コンテナー ダッシュボードの [構成の問題] でその問題が通知されます。 

自動更新を有効にしようとして失敗した場合は、以下を参照してトラブルシューティングを行ってください。

**エラー**: Azure 実行アカウント (サービス プリンシパル) を作成し、サービス プリンシパルに共同作成者ロールを付与するためのアクセス許可がありません。 
- 推奨される操作: ログインしたアカウントに "共同作成者" が割り当てられていることを確認してから、操作を再試行します。 適切なアクセス許可の割り当ての詳細については、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)のドキュメントをご覧ください。
 
自動更新をオンにすると、Site Recovery サービスによってほとんどの問題を修正できます。それには、**[修復]** ボタンをクリックする必要があります。

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

[修復] ボタンが使用できない場合は、拡張機能の設定ウィンドウに表示されているエラー メッセージを参照してください。

 - **エラー**: 実行アカウントに Recovery Services リソースにアクセスするためのアクセス許可がありません。

    **推奨される操作**: 実行アカウントを削除してから[再作成](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)するか、Automation 実行アカウントの Azure Active Directory アプリケーションが Recovery Services リソースにアクセスできることを確認します。

- **エラー**: 実行アカウントが見つかりません。 Azure Active Directory アプリケーション、サービス プリンシパル、ロール、Automation 証明書資産、Automation 接続資産のいずれかが削除されたか、作成されていません。または、証明書と接続の拇印が一致しません。 

    **推奨される操作**: 実行アカウントを削除してから[再作成](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)します。
