---
title: Azure Lab Services での VM の自動シャットダウンを構成する
description: この記事では、ラボ アカウントでの VM の自動シャットダウンを構成する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445816"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>ラボ アカウントの切断設定で VM の自動シャットダウンを構成する
リモート デスクトップ接続が切断された後の Windows ラボ VM (テンプレートまたは学生) の自動シャットダウンを有効または無効にできます。 Lab Services が自動的にシャットダウンされる前にユーザーの再接続を待つ時間の長さを指定することもできます。

## <a name="enable-automatic-shutdown"></a>自動シャットダウン処理を有効にする

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボの設定]** を選択します。
2. **[ユーザーが切断したときに仮想マシンを自動的にシャットダウンする]** オプションを選択します。
3. ユーザーが何分以内に再接続しなかったら VM が自動的にシャットダウンされるかを指定します。

    ![ラボ アカウントの自動シャットダウン設定](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    この設定は、ラボ アカウントで作成されたすべてのラボに適用されます。 ラボ作成者 (教師) は、ラボ レベルでこの設定をオーバーライドできます。 ラボ アカウントでこの設定を変更した場合、変更後に作成されたラボにのみ変更が適用されます。

    この設定を無効にするには、このページの **[ユーザーが切断したときに仮想マシンを自動的にシャットダウンする]** チェック ボックスをオフにします。 

## <a name="next-steps"></a>次のステップ
ラボ所有者がこの設定をラボ レベルで構成またはオーバーライドする方法については、[こちらの記事](how-to-enable-shutdown-disconnect.md)を参照してください
