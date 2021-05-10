---
title: Windows Virtual Desktop Start VM on Connect のよくあるご質問 - Azure
description: Start VM on Connect 機能の使用に関してよく寄せられる質問とベスト プラクティスです。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445586"
---
# <a name="start-vm-on-connect-faq-preview"></a>Start VM on Connect のよくあるご質問 (プレビュー)

> [!IMPORTANT]
> 現在、接続時に仮想マシンを起動機能はパブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Windows Virtual Desktop ホスト プール用 Start Virtual Machine (VM) on Connect (プレビュー) 機能についてよく寄せられる質問について説明します。

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>VM は、ユーザーが使用を停止したときに自動的に割り当てが解除されますか?

いいえ。 ユーザーをセッションからサインアウトし、Azure 自動化スクリプトを実行して VM の割り当てを解除するには、追加のポリシーを構成する必要があります。

次の手順で割り当て解除ポリシーを構成します。

1. ポリシーを設定する VM にリモート接続します。

2. **[グループ ポリシー エディター]** を開き、 **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[セッションの時間制限]** に移動します。

3. **[切断されたセッションの制限時間を設定する]** というポリシーを探し、その値を **[有効]** に変更します。

4. ポリシーを有効にしたら、 **[切断されたセッションを終了する]** を選択します。

>[!NOTE]
>[切断されたセッションを終了する] ポリシーの制限時間は、5 分を超える値に設定してください。 制限時間を短くすると、ネットワークの接続が長時間失われた場合にユーザーのセッションが終了し、作業が失われる可能性があります。

ユーザーのサインアウトによって VM の割り当てが解除されることはありません。 VM の割り当てを解除する方法については、「[勤務時間外の VM の起動/停止](../automation/automation-solution-vm-management.md)」をご覧ください。

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>ユーザーは、クライアントから VM をオフにできますか?

はい。 ユーザーは、物理マシンの場合と同様に、セッション内の [スタート] メニューを使用して VM をシャットダウンできます。 ただし、VM をシャットダウンしても VM の割り当ては解除されません。 VM の割り当てを解除する方法については、「[勤務時間外の VM の起動/停止](../automation/automation-solution-vm-management.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

Start VM on Connect を構成する方法については、「[接続時に仮想マシンを起動 (プレビュー)](start-virtual-machine-connect.md)」をご覧ください。

Windows Virtual Desktop に関する一般的な質問がある場合は、一般的な[よくあるご質問](faq.md)を確認してください。
