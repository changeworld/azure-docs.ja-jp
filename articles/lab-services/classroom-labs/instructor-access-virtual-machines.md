---
title: Azure Lab Services 内の VM にアクセスする講師
description: この記事では、講師が講師ビューから学生の VM にアクセスする方法について説明します。 たとえば、補助教員があるクラスの講師になり、他のクラスで学生になることがあります。
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81642604"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>講師ビューから学生として仮想マシンにアクセスする
この記事では、講師が学生として参加するクラスの VM にアクセスする方法について説明します。 

この機能は、たとえば、次のシナリオで役立ちます。 補助教員が、あるクラスでは講師であるが、他のクラスでは学生である。 その講師が、自分が所有するラボを表示する講師ビューから学生 VM を表示してアクセスしたい。 

## <a name="access-vms-from-instructor-view"></a>講師ビューから VM にアクセスする

1. [Azure Lab Services Web サイト](https://labs.azure.com)にサインインします。 自分が所有しているラボが表示されます。 これらのラボは、自分で作成したラボ、または管理者によって所有者として割り当てられたラボです。 詳細については、[既存のラボに所有者を追加する方法](how-to-add-user-lab-owner.md)に関するページを参照してください。
2. 学生として参加しているクラスの VM にアクセスするには、右上隅にあるコンピューター アイコンを選択します。 学生としてアクセスできる VM が表示されることを確認します。 次の例のユーザーは、Python ラボでは補助教員ですが、Java ラボでは学生です。 そのため、このユーザーは、ドロップダウン リストの Java ラボから VM を表示できます。 このユーザーはその VM を起動して接続できます。 
    
    ![学生の VM にアクセスする](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [VM に接続する](how-to-use-classroom-lab.md#connect-to-the-vm)
- [MAC から RDP を使用して VM に接続する](connect-virtual-machine-mac-rdp.md)
- [Linux 仮想マシン用リモート デスクトップを使用する](how-to-use-remote-desktop-linux-student.md)
