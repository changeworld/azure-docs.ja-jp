---
title: Azure Security Center での ID とアクセスの監視 | Microsoft Docs
description: Azure Security Center の ID とアクセス機能を使用して、ユーザーのアクセス アクティビティと ID 関連の問題を監視する方法を説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552986"
---
# <a name="monitor-identity-and-access-preview"></a>ID とアクセスを監視する (プレビュー)
Security Center によって潜在的なセキュリティの脆弱性が識別されると、リソースを堅牢化および保護するために必要な管理を構成するプロセスを説明する推奨事項が作成されます。

この記事では、Azure Security Center のリソース セキュリティ セクションの **[ID およびアクセス]** ページについて説明します。

このページに記載されている推奨事項の完全な一覧については、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identity)」を参照してください。

> [!NOTE]
> ID とアクセスの監視はプレビュー段階であり、Security Center の Standard レベルでのみ利用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>

ID は企業のコントロール プレーンであり、ID の保護は最優先事項です。 セキュリティ境界は、ネットワーク境界から ID 境界へと発展してきました。 セキュリティにおいては、ネットワークの保護よりもデータの保護、アプリとユーザーのセキュリティ管理が重要になってきています。 最近では、クラウドに移行するデータやアプリが増加しているため、ID が新たな境界線となっています。

ID アクティビティを監視することにより、インシデントが発生する前に事前対応型のアクションを実行するか、攻撃が試みられた場合にそれを阻止する事後対応型のアクションを実行することができます。 [Identity & Access]\(ID およびアクセス\) ダッシュボードには、次のような推奨事項が表示されます。

- サブスクリプションで特権アカウントの MFA を有効にする
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する
- 外部の特権アカウントをサブスクリプションから削除する

> [!NOTE]
> サブスクリプションのアカウント数が 600 を超える場合、Security Center はサブスクリプションに対して ID の推奨事項を実行できません。 実行されない推奨事項については、後述する「利用できない評価」を参照してください。
Security Center は、クラウド ソリューション プロバイダー (CSP) パートナーの管理者エージェントに対して ID の推奨事項を実行できません。
>

## <a name="monitor-identity-and-access"></a>ID とアクセスを監視する

ID とアクセスに関する特定された問題の一覧を開くには、Security Center サイドバー ( **[リソース]** の下)、または [概要] ページで **[Identity & Access]\(ID およびアクセス\)** を選択します。 

**[Identity & Access]\(ID およびアクセス\)** には 2 つのタブがあります。

- **概要**: Security Center によって特定された推奨事項。
- **サブスクリプション**: サブスクリプションと各サブスクリプションの現在のセキュリティ状態の一覧。

[![[Identity & Access]\(ID およびアクセス\)](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>[概要] セクション
**[概要]** には、推奨事項の一覧が表示されます。 最初の列には、推奨事項が一覧表示されます。 2 列目には、その推奨事項の影響を受けるサブスクリプションの合計数が表示されます。 3 列目には、問題の重大度が表示されます。

1. 推奨事項を選択します。 推奨事項のウィンドウが開き、次の内容が表示されます。

   - 推奨事項の説明
   - 異常な状態と正常な状態のサブスクリプションの一覧
   - 評価に失敗したためにスキャンされなかったリソース、またはリソースが Free レベルで実行されているサブスクリプションのリソースで評価対象ではないリソースの一覧

    [![推奨事項ウィンドウ](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. さらに詳細を確認するには、一覧のサブスクリプションを選択します。

### <a name="subscriptions-section"></a>[サブスクリプション] セクション
**[サブスクリプション]** には、サブスクリプションの一覧が表示されます。 最初の列には、サブスクリプションが一覧表示されます。 2 列目には、各サブスクリプションの推奨事項の合計数が表示されます。 3 列目には、問題の重大度が表示されます。

[![[サブスクリプション] タブ](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. サブスクリプションを選択します。 3 つのタブがある概要ビューが開きます。

   - **推奨事項**: Security Center によって実行され、失敗した評価に基づいて表示されます。
   - **評価に合格しました**: Security Center によって実行され、合格した評価の一覧。
   - **利用できない評価**: エラーのため、またはアカウント数が 600 を超えるサブスクリプションのため、実行に失敗した評価の一覧。

   **[推奨事項]** には、選択したサブスクリプションの推奨事項と各推奨事項の重大度の一覧が表示されます。

   [![選択したサブスクリプションの推奨事項](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. 推奨事項を選択すると、推奨事項の説明、異常な状態と正常な状態のサブスクリプションの一覧、スキャンされていないリソースの一覧が表示されます。

   [![推奨事項の説明](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   **[評価に合格しました]** には、合格した評価の一覧が表示されます。  これらの評価の重大度は常に緑色です。

   [![評価に合格しました](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. 一覧から合格した評価を選択すると、評価の説明と正常な状態のサブスクリプションの一覧が表示されます。 異常な状態のサブスクリプションのタブがあり、失敗したすべてのサブスクリプションの一覧が表示されます。

   [![評価に合格しました](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> MFA を必要とするが、除外が設定された条件付きアクセス ポリシーを作成している場合、そのポリシーは一部のユーザーを MFA なしで Azure にサインインできるようにするため、Security Center MFA の推奨事項の評価では非準拠と見なされます。

## <a name="next-steps"></a>次のステップ
その他の Azure リソースの種類に適用される推奨事項の詳細については、次の記事をご覧ください。

- [Azure Security Center でのマシンとアプリケーションの保護](security-center-virtual-machine-protection.md)
- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
- [Azure Security Center での Azure SQL サービスとデータの保護](security-center-sql-service-recommendations.md)