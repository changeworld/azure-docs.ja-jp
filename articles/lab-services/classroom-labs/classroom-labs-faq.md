---
title: Azure Lab Services のクラスルーム ラボ — FAQ | Microsoft Docs
description: この記事では、Azure Lab Services のクラスルーム ラボについてよく寄せられる質問 (FAQ) への回答を示します。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443501"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure Lab Services のクラスルーム ラボ — よく寄せられる質問 (FAQ)
Azure Lab Services のクラスルーム ラボについて特に多く寄せられる質問にお答えします。 

## <a name="quotas"></a>Quotas (クォータ)

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>クォータは、ユーザーごと、1 週間ごと、またはラボの全期間のいずれに対して指定されますか。 
ラボに設定するクォータは、ラボの全期間について、各学生を対象としています。 また、[スケジュールされている VM の実行時間](how-to-create-schedules.md)は、ユーザーに割り当てられるクォータにカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。  クォータの詳細については、「[ユーザーのクォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)」を参照してください。

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>教授が学生の VM をオンにすると、学生のクォータに影響しますか？ 
いいえ。 影響しません。 教授が学生の VM をオンにすると、学生に割り当てられたクォータには影響しません。 

## <a name="schedules"></a>スケジュール

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>スケジュールを設定すると、ラボ内のすべての VM が自動的に開始されますか。 
いいえ。 すべての VM ではありません。 スケジュールに基づいてユーザーに割り当てられている VM のみです。 ユーザーに割り当てられていない VM は自動的に開始されません。 これは仕様です。 

## <a name="lab-accounts"></a>ラボ アカウント

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>アドレス範囲が利用できないためにラボを作成できないのはなぜですか。 
クラスルーム ラボでは、Azure portal でラボ アカウントを作成するときに指定する IP アドレス範囲内にラボ VM を作成できます。 アドレス範囲が指定されている場合、各ラボは、ラボ VM 用に 512 個の IP アドレスが割り当てられた後に作成されます。 ラボ アカウントのアドレス範囲は、ラボ アカウントで作成するすべてのラボを格納するのに十分な大きさである必要があります。 

たとえば、/19 - 10.0.0.0/19 のブロックがある場合、このアドレス範囲は、8192 個の IP アドレスと 16 個のラボ (8192/512 = 16 個のラボ) に対応します。 この場合、ラボの作成は 17 個目のラボの作成で失敗します。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH 経由でラボ仮想マシンに接続するために組織のファイアウォール設定で開く必要があるのは、どのポート範囲ですか。

ポートは、49152 ～ 65535 です。 クラスルーム ラボは、ロード バランサーの背後にあります。 各ラボには 1 つのパブリック IP アドレスがあり、ラボ内の各仮想マシンには一意のポートがあります。 

各仮想マシンのプライベート IP アドレスは、Azure portal のラボのホームページの **[仮想マシンプール]** タブにも表示されます。 ラボを再発行しても、ラボのパブリック IP アドレスは変更されませんが、ラボ内の各仮想マシンのプライベート IP とポート番号は変更される可能性があります。 詳細については、次の記事を参照してください。[Azure Lab Services のファイアウォール設定](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH 経由でラボ仮想マシンに接続するために組織のファイアウォール設定で開く必要があるのは、どのパブリック IP アドレス範囲ですか。
「[Azure IP Ranges and Service Tags — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519)」を参照してください。Azure のデータセンターのパブリック IP アドレス範囲が記載されています。 ラボ アカウントが存在するリージョンの IP アドレスを開くことができます。

## <a name="virtual-machine-images"></a>仮想マシン イメージ

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>ラボの作成者として、新しいラボの作成時に仮想マシンイメージのドロップダウンで追加のイメージオプションを有効にできないのはなぜですか？

管理者がラボ アカウントにラボ作成者として追加すると、ラボを作成するためのアクセス許可が付与されます。 ただし、有効な仮想マシンイメージの一覧を含む、ラボ アカウント内の任意の設定を編集するアクセス許可がありません。 追加のイメージを有効にするには、ラボ アカウント管理者に連絡して追加のイメージを有効にしてもらうか、ラボ アカウントに共同作成者ロールとして追加するよう管理者に依頼してください。 共同作成者ロールによって、ラボ アカウントの仮想マシンイメージの一覧を編集するためのアクセス許可が付与されます。

## <a name="users"></a>ユーザー

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>クラスルーム ラボには、何人のユーザーを追加できますか。
クラスルーム ラボには、最大 400 ユーザーを追加できます。 

## <a name="blog-post"></a>ブログ記事
[Azure Lab Services ブログ](https://azure.microsoft.com/blog/tag/azure-lab-services/)に登録してください。

## <a name="update-notifications"></a>更新通知
Lab Services の新機能について最新情報を入手するには、[Lab Services の更新情報](https://azure.microsoft.com/updates/?product=lab-services)に登録してください。

## <a name="general"></a>全般
### <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
ご自分の質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

- この FAQ の末尾で質問を投稿してください。 
- さらに多くの人々と交流するには、[Azure Lab Services — Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-lab-services)に質問を投稿してください。 
- 機能を要求する場合は、要求とアイデアを [Azure Lab Services のユーザーの声](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)に送信してください。

