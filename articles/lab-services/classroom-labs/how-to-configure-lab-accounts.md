---
title: Azure Lab Services でラボ アカウントを構成する | Microsoft Docs
description: この記事では、Azure Lab Services で、ラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443425"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure Lab Services でラボ アカウントを構成する 
Azure Lab Services では、ラボ アカウントは、クラスルーム ラボなどのマネージド ラボ型のコンテナーです。 管理者は、Azure Lab Services を使用してラボ アカウントを設定し、アカウントにラボを作成できるラボ所有者にアクセスを提供します。 

この記事では、次のタスクを実行する方法について説明します。 

- ラボ内の VM に使用するアドレスの範囲を指定する
- 切断時の VM の自動シャットダウンを構成する

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>ラボ内の VM に使用するアドレスの範囲を指定する
次の作業手順では、ラボ内の VM に使用するアドレスの範囲を指定します。 以前に指定した範囲を更新した場合、変更後のアドレスの範囲が適用されるのは、変更後に作成された VM だけです。 

アドレスの範囲を指定する際は、留意すべき制限がいくつかあります。 

- プレフィックスは 23 以下でなければなりません。 
- ラボ アカウントに対して仮想ネットワークがピアリングされている場合、ピアリングされた仮想ネットワークと重複するアドレスの範囲を指定することはできません。

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボの設定]** を選択します。
2. ラボに作成される VM のアドレスの範囲を **[アドレスの範囲]** フィールドに指定します。 アドレスの範囲は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります (例: 10.20.0.0/23)。 ラボ内の仮想マシンはこのアドレスの範囲に作成されます。
3. ツールバーの **[保存]** を選択します。 

    ![アドレスの範囲を構成する](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>切断時の VM の自動シャットダウン
リモート デスクトップ接続が切断された後の Windows ラボ VM (テンプレートまたは学生) の自動シャットダウンを有効または無効にできます。 VM が自動的にシャットダウンされる前にユーザーの再接続を待つ時間を指定することもできます。

![ラボ アカウントの自動シャットダウン設定](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

この設定は、ラボ アカウントで作成されたすべてのラボに適用されます。 ラボ所有者は、ラボ レベルでこの設定をオーバーライドできます。 ラボ アカウントでこの設定を変更した場合、変更後に作成されたラボにのみ変更が適用されます。

ラボ所有者がこの設定をラボ レベルで構成する方法については、[こちらの記事](how-to-enable-shutdown-disconnect.md)を参照してください

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ作成者にラボの場所の選択を許可する](allow-lab-creator-pick-lab-location.md)
- [ラボのネットワークとピア仮想ネットワークを接続する](how-to-connect-peer-virtual-network.md)
- [ラボに共有イメージ ギャラリーをアタッチする](how-to-attach-detach-shared-image-gallery.md)
- [ユーザーをラボ所有者として追加する](how-to-add-user-lab-owner.md)
- [ラボのファイアウォール設定の表示](how-to-configure-firewall-settings.md)
