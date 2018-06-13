---
title: クラシック ポータルから Azure Portal への移行に関する FAQ | Microsoft Docs
description: クラシック ポータルから Azure Portal への StorSimple デバイスの移行についてよく寄せられる質問に対する回答を提供します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
ms.locfileid: "27785181"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>クラシック ポータルから Azure Portal への StorSimple デバイス マネージャー サービスの移行: よく寄せられる質問 (FAQ)

## <a name="overview"></a>概要

Azure クラシック ポータルで実行されている StorSimple デバイス マネージャー サービスを Azure Portal に移行する場合に生じる可能性のある質問と回答を次に示します。

質問と回答は次の各カテゴリに分類されます。

* StorSimple デバイス マネージャー サービスの移行
* ストレージ アカウントの移行
* Azure Resource Manager ベースのコマンドレットの使用
* StorSimple Data Manager サービスの使用
* その他

## <a name="moving-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの移行

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Azure Portal への移行後、StorSimple Manager サービスをクラシック ポータルで作成することはできますか?


いいえ。 StorSimple Manager サービスを Azure Portal に移行した後は、クラシック ポータルで新しいサービスを作成できません。 また、[クラシック ポータルは 2018 年 1 月 8 日から使用できなくなります](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users)。 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>クラシック ポータルで複数の StorSimple Manager を実行しています。 Azure Portal に移行するものを選択できますか?


いいえ。 Azure Portal に移行する StorSimple Manager を選択することはできません。 サブスクリプションのすべての StorSimple Manager が移行されます。


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>新しい Azure Portal へのサービスの移行を開始しました。 クラシック ポータルから StorSimple Manager を削除する必要がありますか? 


いいえ。 クラシック ポータルから移行したサービスは削除しないでください。 移行が完了するまで待ち、すべての StorSimple Manager が新しいポータルに移行された後、クラシック ポータルからサービスを削除する必要はありません。 最終的に、クラシック ポータルは使用されなくなります。

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Azure Portal で StorSimple デバイス マネージャー サービスの名前を変更できますか?


いいえ。 サービス名は、Azure Portal でサービスを作成した後に変更することはできません。 これは、他のエンティティ (デバイス、ボリューム、ボリューム コンテナー、バックアップ ポリシーなど) でも同様です。

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Azure Resource Manager デプロイメント モデルで 8010/8020 StorSimple Cloud Appliance を作成することはできますか?

はい。 Azure Resource Manager デプロイメント モデルで新しい 8010/8020 StorSimple Cloud Appliance を作成できます。 これらのクラウド アプライアンスの基になる VM は、Resource Manager デプロイメント モデルにもあります。

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Azure Portal へのサブスクリプションの移行時に、StorSimple Cloud Appliance の基になる VM も Azure Resource Management デプロイメント モデルに移行されますか?

StorSimple サービスの移行は、StorSimple Cloud Appliance の VM の管理に依存しません。 StorSimple Cloud Appliance の VM は、現在もクラシック ポータルと Azure Portal の両方で完全に管理できます。

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Azure Portal から既存のクラシック 8010/8020 StorSimple Cloud Appliance を管理できますか?

はい。 既存の 8010/8020 クラウド アプライアンスに関連付けられている VM は、Azure Portal から管理できます。

Update 3.0 以上を実行している StorSimple Cloud Appliances モデル 8010/8020 を作成した場合は、新しい Azure Portal に移行するサービスの影響を受けません。 クラウド アプライアンスを問題なく完全に管理することができます。 

クラシック ポータルで Update 3.0 より前のバージョンを実行しているクラウド アプライアンスがある場合は、制限された機能のみを使用できます。 詳しくは、[Update 3 より前のバージョンを実行しているデバイスでサポートされていない操作の一覧](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)に関するページをご覧ください。

クラウド アプライアンスは更新できません。 最新バージョンのソフトウェアを使用して新しいクラウド アプライアンスを作成してから、既存のボリューム コンテナーを新しく作成したクラウド アプライアンスにフェールオーバーします。 詳しくは、「[クラウド アプライアンスへのフェールオーバー](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)」をご覧ください


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>StorSimple 8000 シリーズ デバイスで Update 2.0 を実行しています。 サービスを新しい Azure Portal に移行しました。 デバイスは正常に接続されましたが、自分のデバイスを完全には管理できないように見えます。 解決するにはどうすればよいですか?

新しい Azure Portal では、Update 3.0 以降を実行している StorSimple デバイスのみがサポートされます。 デバイスで Update 2.0 を実行していた場合、このデバイスで使用できる機能が制限されます。 詳しくは、[Update 3 より前のバージョンを実行しているデバイスでサポートされていない操作の一覧](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)に関するページをご覧ください。

デバイスを完全に管理するには、デバイスに最新の更新プログラムをインストールします。 詳しくは、[Update 5 のインストール](storsimple-8000-install-update-5.md)に関するページをご覧ください。

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Azure Portal に StorSimple Manager サービスを移行しました。 デバイスに関連するアラートがいくつか表示されます。 この動作は移行に関連するものですか?


いいえ。 移行自体はエラーや警告の原因になりません。 アラートを解決するには、アラートの推奨事項に従ってください。

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>StorSimple 5000/7000 シリーズ デバイスを使用しています。 これらも Azure Portal でサポートされますか?


いいえ。 StorSimple 5000/7000 シリーズ デバイスは Azure Portal ではサポートされていません。

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>StorSimple 5000/7000 シリーズ デバイスから StorSimple 8000 シリーズ デバイスへのデータの移行を計画しています。 Azure Portal へのサービスの移行は、データの移行にどのように影響しますか? 

StorSimple 5000/7000 シリーズ デバイスから、Azure Portal で実行している StorSimple 8000 シリーズ デバイスにデータを移行できます。 5000/7000 シリーズから 8000 シリーズへのデータの移行を有効にするには、[Microsoft サポートにサポート チケットを記録](storsimple-8000-contact-microsoft-support.md)する必要があります。


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>サブスクリプション、ストレージ アカウント、リソース グループの移行

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>StorSimple デバイス マネージャーをあるサブスクリプションから Azure Portal の別のサブスクリプションに移行できますか?


いいえ。 StorSimple デバイス マネージャー サービスをサブスクリプション間で移動することはサポートされていません。 次の手順で構成される手動プロセスを実行することができます。

* StorSimple デバイスからデータを移行します。
* デバイスを工場出荷時の状態にリセットします。これにより、デバイス上のローカル データがすべて削除されます。
* StorSimple デバイス マネージャー サービスに新しいサブスクリプションでデバイスを登録します。
* データをデバイスに移行します。

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>ストレージ アカウントを Azure Resource Manager デプロイメント モデルに移行する必要がありますか?


いいえ。 従来のストレージ アカウントは、Azure Portal から完全に管理することができます。 StorSimple サービスを Azure Portal に移行すると、ストレージ アカウントは以前と同様に動作し続けます。

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>サービスが Azure Portal に移行された後、ストレージ アカウントはどうなりますか?

サービスの移行は、ストレージ アカウントの管理とは無関係です。 クラシックと Azure Resource Manager の両方のストレージ アカウントを Azure Portal 内で完全に管理することができます。 Azure Portal にサービスを移行した後、デバイスはこのストレージ アカウントで実行を続け、データへの影響はありません。

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>StorSimple デバイス マネージャーをリソース グループ間で移行することはできますか?


いいえ。 あるリソース グループで作成された StorSimple デバイス マネージャーを別のリソース グループに移行することはできません。

## <a name="using-azure-resource-manager-based-cmdlets"></a>Azure Resource Manager ベースのコマンドレットの使用

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>新しい Azure Portal に移行しました。 Azure クラシック デプロイメント モデルの PowerShell コマンドレットに基づくスクリプトは機能しますか? 何をする必要がありますか?

既存の Azure クラシック デプロイメント モデルの PowerShell コマンドレットは、Azure Portal ではサポートされていません。 Azure Resource Manager からデバイスを管理するには、スクリプトを更新します。 スクリプトの更新の詳細については、[新しい Azure Portal のサンプル](https://github.com/anoobbacker/storsimpledevicemgmttools)のページをご覧ください。

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Azure Portal に移行し、サービス データ暗号化キーをロール オーバーする必要があります。 Azure Portal で、このオプションはどこにありますか?

Azure Portal には、サービス データ暗号化キーをロール オーバーするオプションがありません。 Azure Portal でこのロールオーバーを行う方法について詳しくは、「[サービス データ暗号化キーを変更する](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)」をご覧ください。

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>StorSimple 用 Windows PowerShell コマンドレットを StorSimple デバイスで使用して、サポート パッケージの抽出などの操作を実行しています。 新しい Azure Portal に移行すると、これらのコマンドレットは影響を受けますか?


いいえ。 新しい Azure Portal に移行するサービスで、オンプレミスの StorSimple デバイス (これ自体は移行の影響を受けません) に関連付けられている StorSimple 用 Windows PowerShell コマンドレットに影響はありません。 引き続きこれらのコマンドレットを使用して、Azure Portal 上でも問題なくサポート パッケージを作成できます。 Azure クラシック デプロイメント モデルの PowerShell コマンドレットだけが、この移行の影響を受けます。

## <a name="moving-storsimple-data-manager-service"></a>StorSimple Data Manager サービスの移行

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>StorSimple Data Manager サービスをクラシック Azure ポータルで使用しています。 この移行を進めるにはどうすればよいですか?

StorSimple Data Manager サービスを使用している場合は、自動的に Azure Portal に移行されています。

## <a name="miscellaneous"></a>その他

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>8000 シリーズ デバイスの StorSimple Snapshot Manager を実行しています。 Azure Portal に移行すると、StorSimple Snapshot Manager に影響がありますか?


いいえ。 Azure Portal にサービスを移行するときに、StorSimple Snapshot Manager への影響はありません。 デバイスと StorSimple Snapshot Manager は、以前と同様に動作し続けます。

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>StorSimple デバイス、ボリューム コンテナー、またはボリュームの名前を変更することはできますか?


いいえ。 Azure Portal でデバイス、ボリューム、ボリューム コンテナーまたはバックアップ ポリシーの名前を変更することはできません。

## <a name="next-steps"></a>次の手順

[Update 5.0 より前のバージョンを実行しているデバイスでサポートされている操作](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50)について参照します。



