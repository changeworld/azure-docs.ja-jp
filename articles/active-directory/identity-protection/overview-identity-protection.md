---
title: Azure Active Directory Identity Protection とは
description: Azure AD Identity Protection を使用したリスクの検出、修復、調査、および分析
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 06/15/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b624194285dd5ba7e0a54b8a15d3885a845d64dc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283242"
---
# <a name="what-is-identity-protection"></a>Identity Protection とは

Identity Protection は、組織が次の 3 つの主要なタスクを実行できるツールです。

- [ID ベースのリスクの検出と修復を自動化します](howto-identity-protection-configure-risk-policies.md)。
- ポータルのデータを使用して[リスクを調査](howto-identity-protection-investigate-risk.md)します。
- [リスク検出データを SIEM にエクスポートします](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)。

Identity Protection は、Azure AD により組織について、Microsoft アカウントによりコンシューマー領域について、そして Xbox によりゲームについて得られた学習内容を使用して、ユーザーを保護します。 Microsoft は、脅威を特定し顧客を保護するために、1 日あたり 6 兆 5000 億の信号を分析します。

Identity Protection によって生成された信号、受信した信号は、条件付きアクセスなどのツールにさらに送信してアクセスに関する決定を行うことができます。また、セキュリティ情報とイベント管理 (SIEM) ツールに送り戻して、組織の適用するポリシーに基づく詳細な調査を行うこともできます。

## <a name="why-is-automation-important"></a>自動化が重要な理由

Microsoft の ID セキュリティと保護チームを主導する Alex Weinert による[ 2018 年 10 月のブログ記事](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843)では、イベントのボリュームを処理するときに自動化が非常に重要な理由について説明しています。

> 機械学習およびヒューリスティック システムは、毎日 8 億の異なるアカウントに対して 180 億回のログイン試行のリスクスコアを提供します。そのうち 3 億は、明らかに敵対者 (例: 犯罪アクター、ハッカー) によるものです。
>
> 昨年の Ignite では、ID システムが受ける上位 3 つの攻撃について説明しました。 これらの攻撃の最近のボリュームは次のとおりです
>   
>   - **侵害のリプレイ**:2018 年 5 月に 46 億件の攻撃を検出
>   - **パスワード スプレー**:2018 年 4 月に 35 万件
>   - **フィッシング**:これは正確に定量化するのは困難ですが、2018 年 3 月には 2,300 万件のリスクイベントがあり、その多くはフィッシング関連です

## <a name="risk-detection-and-remediation"></a>リスクの検出と修復

Identity Protection によって次のようなさまざまな種類のリスクが特定されます。

- 匿名 IP アドレスの使用
- 特殊な移動
- マルウェアにリンクした IP アドレス
- 通常とは異なるサインイン プロパティ
- 漏洩した資格情報
- パスワード スプレー
- その他...

上記とその他のリスクに関する詳細 (計算方法や計算するタイミングなど) は「[リスクとは](concept-identity-protection-risks.md)」という記事に記載されています。

リスク シグナルは、Azure AD Multi-Factor Authentication の実行、セルフサービス パスワード リセットを使用したパスワードのリセット、管理者がアクションを実行するまでのブロックなど、修復作業をトリガーすることができます。

## <a name="risk-investigation"></a>リスクの調査

管理者は検出内容を確認し、必要に応じて手動で操作を行うことができます。 Identity Protection では、管理者が調査に使用する 3 つの主要なレポートがあります。

- 危険なユーザー
- リスクの高いサインイン
- リスク検出

詳細については以下に関する記事を参照してください。[リスクを調査する方法](howto-identity-protection-investigate-risk.md)。

### <a name="risk-levels"></a>リスク レベル

Identity Protection では、リスクを低、中、高の 3 つのレベルに分類します。 

Microsoft ではリスクの計算方法に関する具体的な詳細を公開していませんが、各レベルごとに、ユーザーまたはサインインが侵害されたという信頼度は高くなります。 たとえば、見慣れないサインイン プロパティのインスタンスが 1 つあるといったことは、資格情報の漏洩ほど脅威的ではない可能性があります。

## <a name="exporting-risk-data"></a>リスク データのエクスポート

Identity Protection からのデータを他のツールにエクスポートしてアーカイブしたり、さらに調査したり、関連付けしたりすることができます。 Microsoft Graph ベースの API を使用すると、組織はこのデータを収集して、SIEM などのツールでさらに処理することができます。 Identity Protection API へのアクセス方法の詳細については、「[Azure Active Directory Identity Protection と Microsoft Graph の基本](howto-identity-protection-graph-api.md)」を参照してください

Identity Protection の情報と Microsoft Azure Sentinel の統合に関する情報については、「[Azure AD Identity Protection からデータを接続する](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)」を参照してください。

また、組織では、Azure AD 内の診断設定を変更して、RiskyUsers と UserRiskEvents のデータを Log Analytics ワークスペースに送信すること、データをストレージ アカウントにアーカイブすること、データをイベント ハブにストリーミングすること、またはデータをパートナー ソリューションに送信することで、データの保存期間を長くすることができます。 その方法の詳細については、記事「[方法: リスク データをエクスポートする](howto-export-risk-data.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

Identity Protection にユーザーがにアクセスするためには、セキュリティ閲覧者、セキュリティ オペレーター、セキュリティ管理者、グローバル閲覧者、またはグローバル管理者である必要があります。

| ロール | できること | できないこと |
| --- | --- | --- |
| 全体管理者 | Identity Protection へのフル アクセス |   |
| セキュリティ管理者 | Identity Protection へのフル アクセス | ユーザーのパスワードをリセットする |
| セキュリティ オペレーター | すべての Identity Protection レポートと [概要] ブレードを表示する <br><br> ユーザー リスクを無視し、安全なサインインを確認して、セキュリティ侵害を確認する | ポリシーを構成または変更する <br><br> ユーザーのパスワードをリセットする <br><br> アラートを構成する |
| セキュリティ閲覧者 | すべての Identity Protection レポートと [概要] ブレードを表示する | ポリシーを構成または変更する <br><br> ユーザーのパスワードをリセットする <br><br> アラートを構成する <br><br> 検出に関するフィードバックを送信する |

現在のところ、セキュリティ オペレーター ロールでは危険なサインイン レポートにアクセスできません。

条件付きアクセス管理者は、条件としてサインイン リスクを考慮に入れるポリシーを作成することもできます。 詳細については、「[条件付きアクセス:条件](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)」を参照してください。

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| 機能 | 詳細  | Azure AD Free / Microsoft 365 Apps | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| リスク ポリシー | ユーザー リスク ポリシー (Identity Protection 経由)  | いいえ | いいえ |はい | 
| リスク ポリシー | サインイン リスク ポリシー (Identity Protection または条件付きアクセス経由)  | いいえ |  いいえ |はい |
| セキュリティ レポート | 概要 |  いいえ | いいえ |はい |
| セキュリティ レポート | 危険なユーザー  | 限定的な情報。 リスクの程度が中から高のユーザーのみが表示されます。 詳細ドロアーやリスクの履歴は提供されません。 | 限定的な情報。 リスクの程度が中から高のユーザーのみが表示されます。 詳細ドロアーやリスクの履歴は提供されません。 | フル アクセス|
| セキュリティ レポート | リスクの高いサインイン  | 限定的な情報。 リスクの詳細やリスク レベルは表示されません。 | 限定的な情報。 リスクの詳細やリスク レベルは表示されません。 | フル アクセス|
| セキュリティ レポート | リスク検出   | いいえ | 限定的な情報。 詳細ドロアーは提供されません。| フル アクセス|
| 通知 | 危険な状態のユーザーが検出されたアラート  | いいえ | いいえ |はい |
| 通知 | 週間ダイジェスト| いいえ | いいえ | はい | 
| | MFA 登録ポリシー | いいえ | いいえ | はい |

これらの高度なレポートの詳細については次の記事を参照してください。[リスクを調査する方法](howto-identity-protection-investigate-risk.md#navigating-the-reports)。

## <a name="next-steps"></a>次のステップ

- [セキュリティの概要](concept-identity-protection-security-overview.md)

- [リスクとは](concept-identity-protection-risks.md)

- [リスクを軽減するために使用できるポリシー](concept-identity-protection-policies.md)
