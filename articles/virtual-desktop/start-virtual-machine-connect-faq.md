---
title: Azure Virtual Desktop Start VM on Connect のよくあるご質問 - Azure
description: Start VM on Connect 機能の使用に関してよく寄せられる質問とベスト プラクティスです。
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444070"
---
# <a name="start-vm-on-connect-faq"></a>Start VM on Connect のよくあるご質問

この記事では、Azure Virtual Desktop ホスト プール用 Start Virtual Machine (VM) on Connect 機能についてのよくある質問について説明します。

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>VM は、ユーザーが使用を停止したときに自動的に割り当てが解除されますか?

いいえ。 ユーザーをセッションからサインアウトし、Azure 自動化スクリプトを実行して VM の割り当てを解除するには、追加のポリシーを構成する必要があります。

次の手順で割り当て解除ポリシーを構成します。

1. ポリシーを設定する VM にリモート接続します。

2. **[グループ ポリシー エディター]** を開き、 **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[セッションの時間制限]** に移動します。

3. **[切断されたセッションの制限時間を設定する]** というポリシーを探し、その値を **[有効]** に変更します。

4. ポリシーを有効にしたら、 **[切断されたセッションを終了する]** を選択します。

>[!NOTE]
>[切断されたセッションを終了する] ポリシーの制限時間は、5 分を超える値に設定してください。 制限時間を短くすると、ネットワークの接続が長時間失われた場合にユーザーのセッションが終了し、作業が失われる可能性があります。

ユーザーのサインアウトによって VM の割り当てが解除されることはありません。 VM の割り当てを解除する方法については、個人用ホスト プールの場合は[オフ時間中の VM の開始または停止](../automation/automation-solution-vm-management.md)に関する記事を、プールされたホスト プールの場合は「[Azure Automation を使用してセッション ホストをスケーリングする](set-up-scaling-script.md)」を参照してください。

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>ユーザーは、クライアントから VM をオフにできますか?

はい。 ユーザーは、物理マシンの場合と同様に、セッション内の [スタート] メニューを使用して VM をシャットダウンできます。 ただし、VM をシャットダウンしても VM の割り当ては解除されません。 VM の割り当てを解除する方法については、個人用ホスト プールの場合は[オフ時間中の VM の開始または停止](../automation/automation-solution-vm-management.md)に関する記事を、プールされたホスト プールの場合は「[Azure Automation を使用してセッション ホストをスケーリングする](set-up-scaling-script.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Start VM on Connect を構成する方法については、「[接続時に仮想マシンを起動](start-virtual-machine-connect.md)」をご覧ください。

Azure Virtual Desktop に関する一般的な質問がある場合は、一般的な[よくあるご質問](faq.yml)を確認してください。
