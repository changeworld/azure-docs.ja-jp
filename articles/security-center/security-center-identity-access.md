---
title: Azure Security Center での ID とアクセスの監視 | Microsoft Docs
description: Azure Security Center の ID とアクセス機能を使用して、ユーザーのアクセス アクティビティと ID 関連の問題を監視する方法を説明します。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774830"
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Azure Security Center での ID とアクセスの監視
この記事は、Azure Security Center を使用してユーザーの ID とアクセス アクティビティを監視する際に役立ちます。

## <a name="why-monitor-identity-and-access"></a>ID とアクセスを監視する理由
ID は企業のコントロール プレーンであり、ID の保護は最優先事項です。 これまで、組織の周りには境界が存在し、その境界が主な防衛線の 1 つとなっていました。 最近では、クラウドに移行するデータやアプリが増加しているため、ID が新たな境界線となっています。

ID アクティビティを監視することにより、インシデントが発生する前に事前対応型のアクションを実行するか、攻撃が試みられた場合にそれを阻止する事後対応型のアクションを実行することができます。 [Identity & Access]\(ID およびアクセス\) ダッシュボードには、次のように、ID の状態の概要が示されます。

* ログオン試行に失敗した回数。 
* これらの試行中に使用されたユーザーのアカウント。
* ロックアウトされたアカウント。
* パスワードが変更またはリセットされたアカウント。 
* ログインしているアカウントの現在の数。

## <a name="monitor-identity-and-access-activities"></a>ID およびアクセス アクティビティの監視
ID およびアクセスに関連した現在のアクティビティを確認するには、**[Identity & Access]\(ID およびアクセス\)** ダッシュボードにアクセスする必要があります。

1. **[Security Center]** ダッシュボードを開きます。

2. 左側のウィンドウの **[防止]** で **[Identity & Access]\(ID およびアクセス\)** を選択します。 複数のワークスペースがある場合は、ワークスペースのセレクターが表示されます。

    ![ワークスペースの選択](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 右端の列に **[アップグレード プラン]** と表示されている場合、このワークスペースでは無料のサブスクリプションが使用されています。 この機能を使用するには、Standard サブスクリプションにアップグレードしてください。 右端の列に **[REQUIRES UPDATE]\(更新が必要\)** と表示されている場合は、この機能を使用するために [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を更新してください。 価格プランの詳細については、Security Center の価格を確認してください。 
    > 
3. 調査するワークスペースが複数ある場合は、**[失敗したログオン]** 列に従って調査に優先順位を付けることができます。 このワークスペースで失敗したログオン試行の現在の数が表示されます。 使用するワークスペースを選択すると、**[Identity & Access]\(ID およびアクセス\)** ダッシュボードが表示されます。

    ![[Identity & Access]\(ID およびアクセス\)](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. このダッシュボードに表示される情報は、潜在的な疑わしいアクティビティを特定するのにすぐに役立ちます。 このダッシュボードは、3 つの主要な領域に分かれています。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[ID ポスチャ]**。 このワークスペースで発生する ID 関連のアクティビティが示されます。

    b. **[失敗したログオン]**。 失敗したログオン試行の主な原因をすばやく特定するのに役立ちます。 ログオンに最も多く失敗した上位 10 のアカウントの一覧が表示されます。

    c. **[ログオン (時系列)]**。 時間の経過に伴うログオン数をすばやく特定する際に役立ちます。 上位のコンピューター アカウントのログオン試行の一覧が表示されます。
    
選択するタイルにかかわらず、表示されるダッシュボードはログ検索クエリに基づいています。 唯一の違いは、クエリの種類と結果です。 それでも、コンピューターなどの項目を選択して、関連するデータを表示することができます。 

## <a name="see-also"></a>関連項目
この記事では、Security Center で ID とアクセスを監視する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ アラートの管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。 
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

