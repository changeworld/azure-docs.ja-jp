---
title: 切断時の VM のシャットダウンを有効にする Azure Lab Services | Microsoft Docs
description: リモート デスクトップ接続が切断されたときの VM の自動シャットダウンを有効または無効にする方法について説明します。
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475237"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>切断時の VM の自動シャットダウンを有効にする
この記事では、リモート デスクトップ接続が切断された後の **Windows 10** ラボ VM (テンプレートまたは学生) の自動シャットダウンを有効または無効にする方法について説明します。 VM が自動的にシャットダウンされる前にユーザーの再接続を待つ時間の長さを指定することもできます。

ラボ アカウント管理者は、ラボを作成するラボ アカウントにこの設定を構成できます。 詳細については、[ラボ アカウントに切断時の VM の自動シャットダウンを構成する](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)方法に関するページを参照してください。 ラボ所有者は、ラボの作成時またはラボの作成後に、設定をオーバーライドできます。 

## <a name="configure-when-creating-a-lab"></a>ラボの作成時に構成する
ラボ作成ウィザードのステップ 3 のページで、この機能を有効または無効にすることができます。また、VM が自動的にシャットダウンされる前にユーザーの再接続を待つ時間の長さを指定することもできます。 

![ラボの作成時に構成する](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>ラボの作成後に構成する
この設定は、次の図に示すように、 **[設定]** ページで構成できます。 

![ラボの作成後に構成する](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [クラスルーム ラボ用のダッシュボード](use-dashboard.md)