---
title: HR プロビジョニングに関するマネージャーの更新に関する問題のトラブルシューティング
description: HR プロビジョニングでのマネージャーの更新に関する問題をトラブルシューティングする方法について説明します
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: d67e528420b9907949202c1f597f933a74f7c801
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324557"
---
# <a name="troubleshoot-hr-manager-update-issues"></a>HR マネージャーの更新に関する問題のトラブルシューティング

**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング
* オンプレミスの Active Directory のユーザー プロビジョニングに対する SAP SuccessFactors
* Azure Active Directory のユーザー プロビジョニングに対する SAP SuccessFactors

## <a name="understanding-how-manager-reference-resolution-works"></a>マネージャー参照解決のしくみを理解する
Azure AD プロビジョニング サービスでは、マネージャーの情報が自動的に更新され、Azure AD 内のユーザーとマネージャー間のリレーションシップが常に HR データと同期されます。 *マネージャーの参照解決* と呼ばれるプロセスを使用して、*manager* 属性を正確に更新します。 プロセスの詳細に移る前に、マネージャーの情報が、Azure AD とオンプレミスの Active Directory に格納される方法を理解するのが重要です。 

* **オンプレミスの Active Directory** では、*manager* 属性は、マネージャーのアカウントの *distinguishedName (dn)* を AD に格納します。 
* **Azure AD** では、*manager* 属性は、Azure AD の DirectoryObject ナビゲーション プロパティです。 ユーザー レコードを Azure portal で表示すると、Azure AD のマネージャー レコードの *displayName* が表示されます。 

*マネージャー参照解決* は、次の 2 つのステップのプロセスです。 
* 手順 1: *source anchor* と *target anchor* と呼ばれる属性のペアを使用して、マネージャーの HR ソース レコードをマネージャーのターゲット アカウント レコードにリンクします。 
* 手順 2: スキーマで定義されているマネージャー参照属性を使用して、ターゲットのマネージャー属性を必要な形式で更新します。 

各アプリの既定のアンカー属性と参照属性を以下に示します。 

| アプリ名 | アンカー属性 | ユーザー プロファイルの参照属性 | 
|--|--|--| 
| Workday | WID | ManagerReference (マネージャー レコードの WID をポイントします) |
| SAP SuccessFactors | personIdExternal | manager (マネージャー レコードの personIdExternal を参照) |
| オンプレミスの Active Directory | objectGUID | manager (マネージャー レコードの DN を示します) |
| Azure AD | objectId | manager (マネージャーの Azure AD レコードを指す) |

## <a name="prerequisites-for-successful-manager-update"></a>マネージャーの更新を成功するための前提条件
マネージャーの参照解決が正常に機能するには、次の前提条件を満たしている必要があります。 
* プロビジョニング アプリは、上記の既定のソース アンカーとターゲット アンカーを使用するように構成する必要があります。 これらのアンカー属性と参照属性に関連付けられているメタデータ プロパティ (データ型、API 式) は変更されません。 
* マネージャー属性に関連付けられている API 式 (Workday の場合は XPATH、SuccessFactors の場合は JSONPath) は、有効な null 以外の値に解決されます。 
   * Workday ManagerReference の既定の XPATH API 式: `wd:Worker/wd:Worker_Data/wd:Management_Chain_Data/wd:Worker_Supervisory_Management_Chain_Data[position()=1]/wd:Management_Chain_Data[last()=position()]/wd:Manager_Reference/wd:ID[@wd:type='WID']/text()`
   * SuccessFactors マネージャーの既定の JSONPath API 式: `$.employmentNav.results[0].userNav.manager.empInfo.personIdExternal`
* マネージャー レコードもプロビジョニング ジョブのスコープ内に含む必要があります。 
* プロビジョニング アプリは、ユーザー レコードを処理する前にマネージャー レコードを処理している必要があります。 

## <a name="provision-on-demand-does-not-update-manager-attribute"></a>オンデマンド プロビジョニングでマネージャー属性が更新されない
| トラブルシューティング | 詳細 |
|--|--|
| **問題点** | 受信プロビジョニング アプリが正常に構成されました。 プロビジョニング オンデマンドとの同期をテストしています。 マネージャー属性は更新されず、"無効な値" というエラー メッセージが表示されます。  |
| **原因** | プロビジョニング ジョブが、[マネージャーの更新を成功するための前提条件](#prerequisites-for-successful-manager-update)の 1 つを満たしていません  |
| **解像度** | * 既定のマネージャー属性マッピングを変更した場合は、既定のマッピングを復元します。 <br> * マネージャー レコードがスコープ内であり、マネージャー API 式が有効な値に解決されます。 <br> * 最初にマネージャー レコードに対して provision-on-demand を実行し、次にユーザー レコードに対して provision-on-demand を実行します。  |

## <a name="full-sync-does-not-update-manager-attribute"></a>完全同期でマネージャー属性が更新されない
| トラブルシューティング | 詳細 |
|--|--|
| **問題点** | 受信プロビジョニング アプリが正常に構成されました。 スコープ フィルターを使用して、特定の HR レコードのみを処理しています。 一部のユーザーに対してマネージャーの解決が行っていないことが確認されます。  |
| **原因** | スコープ フィルターを使用している場合は、マネージャー レコードがスコープ内にはない可能性が高い。  |
| **解像度** | スコープ フィルターを更新して、スコープにマネージャー レコードを追加する  |

## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](check-status-user-account-provisioning.md)
