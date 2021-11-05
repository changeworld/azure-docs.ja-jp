---
title: Microsoft Defender for Cloud でユーザーデータを管理する |Microsoft Docs
description: Microsoft Defender for Cloud でユーザーデータを管理する方法について説明します。 ユーザー データの管理には、データへのアクセス、削除、またはエクスポート機能が含まれます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 24a40de1a5b085110571929c4e35295a5ff131dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092702"
---
# <a name="manage-user-data-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でユーザーデータを管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Defender for Cloud でユーザーデータを管理する方法について説明します。 ユーザー データの管理には、データへのアクセス、削除、またはエクスポート機能が含まれます。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

閲覧者、所有者、共同作成者、またはアカウント管理者の役割が割り当てられたクラウドユーザーの Defender は、ツール内の顧客データにアクセスできます。 アカウント管理者ロールの詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md)」を参照して閲覧者、所有者、共同作成者ロールを参照してください。 「[Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)」に関する記事を参照してください。

## <a name="searching-for-and-identifying-personal-data"></a>個人データの検索および特定
クラウドユーザーの Defender は、Azure portal を通じて自分の個人データを表示できます。 Defender for Cloud には、電子メールアドレスや電話番号などのセキュリティ連絡先の詳細情報のみ保存します。 詳細については、「 [Microsoft Defender For Cloud でセキュリティ連絡先の詳細を指定](configure-email-notifications.md)する」を参照してください。

Azure portal では、ユーザーは、クラウドのJust-In-Time VM アクセス機能に対して Defender を使用して許可された IP 構成を表示できます。 詳細については、「[Just-In-Time を使用した仮想マシン アクセスの管理](just-in-time-access-usage.md)」を参照してください。

Azure portal では、ユーザーは、IP アドレスや攻撃者の詳細を含む、Defender によって提供されるセキュリティアラートを表示できます。 詳細については、「 [Microsoft Defender For Cloud でのセキュリティの警告の管理と対応](managing-and-responding-alerts.md)」を参照してください。

## <a name="classifying-personal-data"></a>個人データの分類
クラウドのセキュリティ連絡先機能については、Defender で見つかった個人データを分類する必要はありません。 保存されるデータは、1 つまたは複数のメール アドレスと電話番号です。 [連絡先データ](configure-email-notifications.md) は、Defender によってクラウドに対して検証されます。

クラウドの [Just-In-Time](just-in-time-access-usage.md) 機能については、Defender によって保存された IP アドレスとポート番号を分類する必要はありません。

管理者の役割を割り当てられたユーザーのみが、Defender for Cloud で [アラートを表示](managing-and-responding-alerts.md) することによって個人データを分類できます。

## <a name="securing-and-controlling-access-to-personal-data"></a>個人データのセキュリティ保護とアクセス制御
閲覧者、所有者、共同作成者、またはアカウント管理者の役割が割り当てられているクラウドユーザーの Defender は、 [セキュリティ連絡先データ](configure-email-notifications.md)にアクセスできます。

閲覧者、所有者、共同作成者、またはアカウント管理者の役割が割り当てられているクラウドユーザーの Defender は、 [Just-In-Timeポリシーに](just-in-time-access-usage.md) アクセスできます。

閲覧者、所有者、共同作成者、またはアカウント管理者の役割が割り当てられているクラウドユーザーの Defender は、 [警告](managing-and-responding-alerts.md)を表示できます。

## <a name="updating-personal-data"></a>個人データの更新
所有者、共同作成者、またはアカウント管理者の役割を割り当てられたクラウドユーザーの Defender は、Azure portal 経由で [セキュリティ連絡先データ](configure-email-notifications.md) を更新することができます。

所有者、共同作成者、またはアカウント管理者のロールが割り当てられたクラウドユーザーの Defender は、 [Just-In-Timeポリシー](just-in-time-access-usage.md)を更新することができます。

アカウント管理者は、アラート インシデントを編集できません。 [アラート インシデント](managing-and-responding-alerts.md)はセキュリティ データと見なされ、読み取り専用です。

## <a name="deleting-personal-data"></a>個人データの削除
所有者、共同作成者、またはアカウント管理者の役割を割り当てられたクラウドユーザーの Defender は、Azure portal 経由で [セキュリティ連絡先データ](configure-email-notifications.md) を削除することができます。

所有者、共同作成者、またはアカウント管理者のロールが割り当てられたクラウドユーザーの Defender は、Azure portal を使用して [Just-In-Timeポリシー](just-in-time-access-usage.md) を削除することができます。

クラウドユーザーの Defender は、アラートインシデントを削除することができません。 セキュリティ上の理由により、[アラート インシデント](managing-and-responding-alerts.md)は読み取り専用データと見なされます。

## <a name="exporting-personal-data"></a>個人データのエクスポート
閲覧者、所有者、共同作成者、またはアカウント管理者の役割が割り当てられたクラウドユーザーの Defender は、次の方法で [セキュリティ連絡先データ](configure-email-notifications.md) をエクスポートすることができます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

アカウント管理者のロールが割り当てられているクラウドユーザーの Defender は、次の方法で IP アドレスを含む [Just-In-Timeポリシー](just-in-time-access-usage.md) をエクスポートすることができます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

アカウント管理者は、次の方法でアラートの詳細をエクスポートできます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

詳細については、[セキュリティ アラートの取得 (GET コレクション)](/previous-versions/azure/reference/mt704050(v=azure.100)) に関するページを参照してください。

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>同意のないプロファイルまたはマーケティングのための個人データの使用の制限
クラウドユーザーの Defender では、 [セキュリティ連絡先データ](configure-email-notifications.md)を削除することでオプトアウトを選択できます。

[Just In Time データ](just-in-time-access-usage.md)は、識別不可能なデータと見なされ、30 日間保持されます。

[アラート データ](managing-and-responding-alerts.md)は、セキュリティ データと見なされ、2 年間保持されます。

## <a name="auditing-and-reporting"></a>監査とレポート
セキュリティ連絡先、Just In Time、およびアラートの更新の監査ログは、[Azure の活動ログ](../azure-monitor/essentials/platform-logs-overview.md)で管理されます。
