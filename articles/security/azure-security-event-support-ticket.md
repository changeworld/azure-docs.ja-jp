---
title: "セキュリティ イベント サポート チケットをログに記録する方法 - Azure | Microsoft Docs"
description: "セキュリティ イベントの可能性があるイベントを確認したため、Azure Marketplace を利用する販売者として適切なチケットをログに記録する方法を知る必要があります。"
services: security
documentationcenter: na
author: DavidBosland
manager: lakoch
editor: v-dabosl
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: v-dabosl
ms.openlocfilehash: 753d6abcdd063d5c092b0c90770396d176bc52e8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-log-a-security-event-support-ticket"></a>セキュリティ イベント サポート チケットをログに記録する方法

1. [発行者サポート](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636450758943226673)に移動し、Microsoft 資格情報でサインインします。
2. [問題の種類] として [セキュリティ イベント] を選択し、[セキュリティ インシデント] と [脆弱性] のカテゴリのどちらかを選択します。

    ![イベントの種類と定義][1]

3. 問題の種類とカテゴリを選択したら、'**[Start request]\(要求の開始\)**' をクリックします。 Microsoft が問題を適切に把握するために、次の情報を提供してください。

    i. どんな問題および/または脆弱性ですか。

    ii. 脆弱性については CVE (mitre.org) または CVSS3 v3 計算機 (https://www.first.org/cvss/calculator/3.0) を使用して情報を提供してください。

    iii. 解決策または軽減策はありますか。 はいの場合は、修復の手順をご提供ください。

    iv. ユーザーに送信するメッセージはありますか。 Microsoft がお客様と連携して適切なメッセージを作成します (該当する場合)。

4. 送信の確認 - -送信した問題は 1 営業日以内に受信確認が行われ、問題に優先順位と重大度が割り当てられます。

    - 問題について Microsoft に確認する必要がある場合は、すべてのやり取りに確認番号を使用してください。
    - また、問題に関する進捗はいつでも確認できます。

5. 次に行われる処理。 問題と重大度に応じて、次の手順が行われることがあります。

    - 評価結果をご連絡します。 結果によってはお客様のサービスを削除したり、変更をお願いしたりする場合があります。 その際にはサービスの中断による顧客への影響を最小限に抑えられるよう、お客様と連携します。
    - Microsoft はお客様との共通の顧客のために、連携してインシデント/脆弱性の影響を軽減できるようお手伝いします。


[1]: ./media/azure-security-event-support-ticket/chart.png
