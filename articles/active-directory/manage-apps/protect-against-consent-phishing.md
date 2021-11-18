---
title: 同意フィッシングからの保護
titleSuffix: Azure AD
description: Azure AD を使用してアプリベースの同意フィッシング攻撃を軽減する方法について説明します。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: cad0102bf76cd63553049045ea55e0e7fb8f16d0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550686"
---
# <a name="protecting-against-consent-phishing"></a>同意フィッシングからの保護

生産性がプライベート ネットワークに限定されなくなり、作業がクラウド サービスに大きく移行しました。 クラウド アプリケーションを使用すると、従業員はリモートで生産性を高めることができますが、攻撃者もアプリケーションベースの攻撃を使用して重要な組織データにアクセスできます。 メールのフィッシングや資格情報の侵害など、ユーザーに焦点を当てた攻撃にはなじみがあるかもしれません。 ***同意フィッシング*** は、注意が必要なもう 1 つの脅威ベクトルです。
この記事では、同意フィッシングの概要、ユーザーを保護するための Microsoft の取り組み、組織の安全を維持するための手順について説明します。

## <a name="what-is-consent-phishing"></a>同意フィッシングとは

同意フィッシング攻撃では、悪意のあるクラウド アプリにアクセス許可を付与するようにユーザーを誘導します。 その後、これらの悪意のあるアプリで、ユーザーの正当なクラウド サービスとデータにアクセスできます。 資格情報の侵害とは異なり、同意フィッシングを実行する "*脅威アクター*" は、個人または組織のデータへのアクセスを直接許可できるユーザーを標的にします。 同意画面には、アプリが受け取るすべてのアクセス許可が表示されます。 アプリケーションは正当なプロバイダー (Microsoft の ID プラットフォームなど) によってホストされているため、疑いを持たないユーザーは条項に同意します ([*同意する*] をクリックします)。これにより、悪意のあるアプリケーションに対して、要求されているユーザーまたは組織のデータへのアクセス許可が付与されます。

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="ユーザーの同意を必要とする [アクセス許可が要求されています] ウィンドウを示すスクリーンショット。":::

*さまざまなアクセス許可へのアクセスを要求している OAuth アプリの例。*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>Azure AD を使用した同意フィッシング攻撃の軽減

管理者、ユーザー、または Microsoft のセキュリティ研究者は、疑わしい動作を示す OAuth アプリケーションにフラグを付けることができます。 フラグ付けされたアプリケーションは Microsoft によって審査され、そのアプリがサービス利用規約に違反するかどうかが判断されます。 違反が確認された場合、Azure AD ではそのアプリケーションが無効化され、すべての Microsoft サービスでその後の使用が禁止されます。

Azure AD で OAuth アプリケーションが無効化されると、いくつかのことが発生します。
- 悪意のあるアプリケーションとそれに関連するサービス プリンシパルは、完全に無効な状態になります。 新しいトークン要求または更新トークンの要求は拒否されますが、既存のアクセス トークンは有効期限が切れるまで引き続き有効です。
- Microsoft Graph の関連する [application](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true) および [servicePrincipal](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true) リソースの種類については、*disabledByMicrosoftStatus* という名前の公開プロパティを使用して無効状態が表示されます。
- Microsoft による無効化の前にアプリケーションに同意したユーザーが組織内にいた可能性があるグローバル管理者は、セキュリティ体制を調査して改善するために実行されたアクションと実行可能な推奨される手順を反映したメールを受け取ります。

## <a name="recommended-response-and-remediation"></a>推奨される対応と修復

組織が Microsoft によって無効化されたアプリケーションの影響を受けた場合は、環境のセキュリティを保護するために、直ちに次の手順を実行することをお勧めします。

1. 次を含む、無効化されたアプリケーションのアプリケーション アクティビティを調べます。
    - アプリケーションによって要求された、委任されたアクセス許可またはアプリケーションのアクセス許可。
    - アプリケーションによるアクティビティとアプリケーションを使用する権限を持つユーザーのサインイン アクティビティに関する Azure AD 監査ログ。
1. Microsoft クラウド製品の[不正な同意付与からの保護に関するガイダンス](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)を確認して実装します。これには、無効化されたアプリケーションの監査のアクセス許可と同意や、審査中に検出されたその他の疑わしいアプリが含まれます。
1. 以下で説明する、同意フィッシングに対するセキュリティ強化のベスト プラクティスを実装します。


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>同意フィッシング攻撃に対するセキュリティ強化のベスト プラクティス

Microsoft では、組織内でアプリケーションを許可および使用する方法を制御するための適切な分析情報と機能を提供することで、管理者に主導権を与えたいと考えています。 攻撃者は決してその手を緩めませんが、組織のセキュリティ体制を改善するために実行できる手順があります。 従うべきベスト プラクティスのいくつかを、次に示します。

* アクセス許可と同意フレームワークのしくみについて組織を教育する
    - アプリケーションから求められるデータとアクセス許可を理解し、 [アクセス許可と同意](../develop/v2-permissions-and-consent.md)がプラットフォーム内でどのように機能するかを理解します。
    - 管理者が [同意要求を管理および評価する](./manage-consent-requests.md)方法を理解しているか確認します。
    - 定期的に組織内の[アプリと同意済みのアクセス許可を監査](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) して、使用されているアプリケーションが必要なデータにのみアクセスしており、最小特権の原則に準拠していることを確認します。
* 一般的な同意フィッシングの戦術を特定してブロックする方法を知る
    - スペルや文法に誤りがあるか確認します。 メール メッセージまたはアプリケーションの同意画面にスペルミスや文法エラーがある場合、それは疑わしいアプリケーションである可能性があります。 その場合は、[同意プロンプト](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt)の [*こちらでご報告ください*] リンクを使用して直接報告できます。Microsoft では、それが悪意のあるアプリケーションかどうかを調べ、確認された場合は、それを無効化します。
    - アプリ名やドメイン URL を信頼性のソースとして利用しないでください。 攻撃者は、正当なサービスまたは企業のアプリケーションに見えるようにアプリ名を偽造し、悪意のあるアプリに同意させようとします。 代わりに、ドメイン URL のソースを検証し、可能であれば[確認済みの発行元](../develop/publisher-verification-overview.md)から入手したアプリケーションを使用します。
    - 攻撃者が組織内の既知のユーザーを偽装しているフィッシング キャンペーンから保護することで、[Microsoft Defender for Office 365 による同意フィッシング メール](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365)をブロックします。
    -  [アクティビティ ポリシー](/cloud-app-security/user-activity-policies)、 [異常検出](/cloud-app-security/anomaly-detection-policy)、 [OAuth アプリ ポリシー](/cloud-app-security/app-permission-policy) などの Microsoft Defender for Cloud アプリ ポリシーを構成すると、組織の異常なアプリケーション アクティビティを管理し、対応するアクションを実行するのに役立ちます。
    - [Microsoft 365 Defender を使用した高度な検出](/microsoft-365/security/defender/advanced-hunting-overview)に関するガイダンスに従って、同意フィッシング攻撃の調査と検出を行います。
* 信頼できるアプリへのアクセスを許可し、信頼できないアプリから保護する
    - 発行元が確認されたアプリケーションを使用します。 [発行元の確認](../develop/publisher-verification-overview.md)は、管理者とエンド ユーザーが、Microsoft がサポートする審査プロセスを通じてアプリケーション開発者の信頼性を理解するのに役立ちます。
    - 信頼できる特定のアプリケーション (所属組織や確認済みの発行元が開発したアプリケーションなど) にのみユーザーが同意できるように、[ユーザーの同意設定を構成](./configure-user-consent.md?tabs=azure-portal)します。
    - 一般的な疑わしいアプリの動作に対処するため、プロアクティブな[アプリ ガバナンス](/microsoft-365/compliance/app-governance-manage-app-governance) ポリシーを作成して、Microsoft 365 プラットフォーム上でサードパーティのアプリの動作を監視します。

## <a name="next-steps"></a>次のステップ

* [アプリ同意付与の調査](/security/compass/incident-response-playbook-app-consent)
* [アプリへのアクセスの管理](./what-is-access-management.md)
* [Azure AD でのユーザーの同意操作を制限する](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations)
