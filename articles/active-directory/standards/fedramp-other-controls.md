---
title: FedRAMP High Impact に対応するようにその他のコントロールを構成する
description: FedRAMP High Impact レベルに対応するように追加のコントロールを構成する方法についての詳細なガイダンスです。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b9842214b107760bf6e4a677099a7415bc6ff2
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294125"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>FedRAMP High Impact レベルを達成するためにその他のコントロールを構成する

下記ファミリのコントロール (およびコントロールの強化) に関する次の一覧は、Azure AD テナントでの構成が必要になる場合があります。

次の表の各行は、コントロールやコントロールの強化に関して共有されている責任に対する組織の対応を策定するのに役立つ規範的なガイダンスを提供します。

## <a name="audit--accountability"></a>監査とアカウンタビリティ

* AU-02 イベントの監査

* AU-03 監査の内容
* AU-06 監査のレビュー、分析、報告


| コントロール ID とサブパート| お客様の責任とガイダンス |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| **AU-02 パート a で定義されたイベントをシステムが監査できることを確認し、組織の監査可能イベントのサブセットに含まれるその他エンティティとの調整を行い、事後調査をサポートします。監査レコードの集中管理を実装します。**<p>すべてのアカウント ライフサイクル操作 (アカウントの作成、変更、有効化、無効化、削除の各アクション) は、Azure AD 監査ログ内で監査されます。 すべての認証および認可イベントは Azure AD サインイン ログ内で監査され、検出されたリスクがあれば Identity Protection ログで監査されます。 これらの各ログは、Azure Sentinel などのセキュリティ情報イベント管理 (SIEM) ソリューションに直接ストリーミングできます。 または、Azure Event Hub を使用して、ログをサードパーティ製の SIEM ソリューションと統合します。<p>監査イベント<li> [Azure Active Directory ポータルの監査アクティビティ レポート](https://docs.microsoft.com///azure/active-directory/reports-monitoring/concept-audit-logs)<li> [Azure Active Directory ポータルのサインイン アクティビティ レポート](https://docs.microsoft.com///azure/active-directory/reports-monitoring/concept-sign-ins)<li>[方法: リスクの調査](https://docs.microsoft.com///azure/active-directory/identity-protection/howto-identity-protection-investigate-risk)<p>SIEM の統合<li> [Azure Sentinel : Azure Active Directory (Azure AD) からデータを接続する](https://docs.microsoft.com///azure/sentinel/connect-azure-active-directory)<li>[Azure イベント ハブやその他の SIEM へのストリーミング](https://docs.microsoft.com///azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| **少なくとも毎週 1 回監査レコードをレビューおよび分析し、不適切または異常なアクティビティを特定し、結果を適切な担当者に報告します。** <p>前出の AU-02 および AU-03 のガイダンスにより、監査レコードの毎週のレビューと適切な担当者への報告が可能になります。 Azure AD のみを使用してこれらの要件を満たすことはできません。 Azure Sentinel などの SIEM ソリューションも使用する必要があります。<p>[Azure Sentinel とは](https://docs.microsoft.com///azure/sentinel/overview) |

## <a name="incident-response"></a>インシデント対応

* IR-04 インシデント処理

* IR-05 インシデント監視

| コントロール ID とサブパート| お客様の責任とガイダンス |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| **インシデントの処理と監視の機能を実装します。これには、自動インシデント処理、動的再構成、運用の継続性、情報の相関、インサイダーの脅威、外部組織との相関、インシデントの監視と自動追跡などがあります。** <p>すべての構成変更は監査ログに記録されます。 認証および認可イベントはサインイン ログ内で監査され、検出されたリスクがあれば Identity Protection ログで監査されます。 これらの各ログは、Azure Sentinel などのセキュリティ情報イベント管理 (SIEM) ソリューションに直接ストリーミングできます。 または、Azure Event Hub を使用して、ログをサードパーティ製の SIEM ソリューションと統合します。 MSGraph や Azure AD PowerShell を使用して、SIEM 内のイベントに基づいて動的再構成を自動化します。<p>監査イベント<br><li>[Azure Active Directory ポータルの監査アクティビティ レポート](https://docs.microsoft.com///azure/active-directory/reports-monitoring/concept-audit-logs)<li>[Azure Active Directory ポータルのサインイン アクティビティ レポート](https://docs.microsoft.com///azure/active-directory/reports-monitoring/concept-sign-ins)<li>[方法: リスクの調査](https://docs.microsoft.com///azure/active-directory/identity-protection/howto-identity-protection-investigate-risk)<p>SIEM の統合<li>[Azure Sentinel : Azure Active Directory (Azure AD) からデータを接続する](https://docs.microsoft.com///azure/sentinel/connect-azure-active-directory)<li>[Azure イベント ハブやその他の SIEM へのストリーミング](https://docs.microsoft.com///azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)<p>動的再構成<li>[AzureAD モジュール](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)<li>[Microsoft Graph の概要](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) |


  
## <a name="personnel-security"></a>人的セキュリティ

* PS-04 職員の離職

| コントロール ID とサブパート| お客様の責任とガイダンス |
| - | - |
| PS-04<br>PS-04(2)| **システムへのアクセスの無効化に責任を負う担当者に自動的に通知します。** <p>8 時間以内に、アカウントを無効にし、関連付けられているすべての認証子と資格情報を取り消します。 <p>外部人事システムから、オンプレミスの Active Directory から、またはクラウド内で直接、Azure AD のアカウントのプロビジョニング (離職時の無効化を含む) を構成します。 既存のセッションを取り消して、すべてのシステム アクセスを終了します。 <p>アカウント プロビジョニング<li> AC-02 の詳細なガイダンスを参照してください。 <p>関連付けられているすべての認証子を取り消します。 <li> [Azure Active Directory で緊急時にユーザー アクセスを取り消す](https://docs.microsoft.com/azure/active-directory/enterprise-users/users-revoke-access) |


## <a name="system--information-integrity"></a>システムと情報の整合性

* SI-04 情報システムの監視

 コントロール ID とサブパート| お客様の責任とガイダンス |
| - | - |
| SI-04<br>SI-04(1)| **情報システム全体の監視と侵入検出システムを実装する**<p>すべての Azure AD ログ (監査、サインイン、ID 保護) を情報システム監視ソリューション内に含めます。 <p>Azure AD ログを SIEM ソリューションにストリーミングします (IA-04 を参照)。 |

## <a name="next-steps"></a>次の手順

[アクセス制御の構成](fedramp-access-controls.md)

[ID および認証コントロールの構成](fedramp-identification-and-authentication-controls.md)

[その他のコントロールの構成](fedramp-other-controls.md)
 
