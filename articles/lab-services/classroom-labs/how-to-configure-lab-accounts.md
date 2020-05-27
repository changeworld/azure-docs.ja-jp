---
title: Azure Lab Services での VM の自動シャットダウンを構成する
description: この記事では、ラボ アカウントでの VM の自動シャットダウンを構成する方法について説明します。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588209"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>ラボ アカウントの切断設定で VM の自動シャットダウンを構成する
リモート デスクトップ接続が切断された後の Windows ラボ VM (テンプレートまたは学生) の自動シャットダウンを有効または無効にできます。 Lab Services が自動的にシャットダウンされる前にユーザーの再接続を待つ時間の長さを指定することもできます。

## <a name="enable-automatic-shutdown"></a>自動シャットダウン処理を有効にする

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボの設定]** を選択します。
2. **[ユーザーが切断したときに仮想マシンを自動的にシャットダウンする]** オプションを選択します。
3. ユーザーが何分以内に再接続しなかったら VM が自動的にシャットダウンされるかを指定します。

    ![ラボ アカウントの自動シャットダウン設定](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    この設定は、ラボ アカウントで作成されたすべてのラボに適用されます。 ラボ作成者 (教師) は、ラボ レベルでこの設定をオーバーライドできます。 ラボ アカウントでこの設定を変更した場合、変更後に作成されたラボにのみ変更が適用されます。

    この設定を無効にするには、このページの **[ユーザーが切断したときに仮想マシンを自動的にシャットダウンする]** チェック ボックスをオフにします。 

## <a name="next-steps"></a>次のステップ
ラボ所有者がこの設定をラボ レベルで構成またはオーバーライドする方法については、[こちらの記事](how-to-enable-shutdown-disconnect.md)を参照してください
