---
title: HR プロビジョニングに関する属性の取得に関する問題のトラブルシューティング
description: HR プロビジョニングに関する属性の取得に関する問題をトラブルシューティングする方法について説明します
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: dd1d69bf170f8213aac052a51350265bec08c991
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283204"
---
# <a name="troubleshoot-hr-attribute-retrieval-issues"></a>HR 属性の取得に関する問題のトラブルシューティング

## <a name="provisioning-app-is-not-fetching-all-workday-attributes"></a>プロビジョニング アプリですべての Workday 属性がフェッチされない
**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング

| トラブルシューティング | 詳細 |
|-- | -- |
| **問題点** | Workday 受信プロビジョニング アプリを設定し、Workday テナント URL に正常に接続しました。 テスト同期を実行し、プロビジョニング アプリが Workday からすべての属性を取得していないのを確認しました。 一部の属性だけが読み取り、ターゲットにプロビジョニングされます。 |
| **原因** | 既定では、Workday プロビジョニング アプリには、Workday Web Services (WWS) v21.1 で動作する属性マッピングと XPATH 定義が含されています。 プロビジョニング アプリで Workday への接続を構成するときに、WWS API バージョン (例: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0`) を明示的に指定した場合、WWS API バージョンと XPATH 定義の不一致が原因で、この問題が発生する可能性があります。  |
| **解像度** | * *オプション 17*:URL から WWS API バージョン情報を削除し、既定の WWS API バージョン v21.1 を使用する <br> * *オプション 2*: XPATH API 式を手動で更新します。そのため、好みの WWS API バージョンと互換性があります。 「[Workday 属性のリファレンス](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)」セクションを参照して、 **[属性マッピング] -> [詳細オプション] -> [Edit attribute list for Workday]\(Workday の属性リストの編集\)** の下にある **[XPATH API 式]** を更新します。  |

## <a name="provisioning-app-is-not-fetching-workday-integration-system-attributes--calculated-fields"></a>プロビジョニング アプリが Workday 統合システム属性/計算フィールドをフェッチしていない
**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング

| トラブルシューティング | 詳細 |
|-- | -- |
| **問題点** | Workday 受信プロビジョニング アプリを設定し、Workday テナント URL に正常に接続しました。 Workday で構成された統合システムを使用し、Workday 統合システムの属性を指す XPATH を構成しました。 ただし、Azure AD プロビジョニング アプリでは、これらの統合システム属性または計算フィールドに関連付けられている値はフェッチされません。 |
| **原因** | これは、既知の制限です。 Workday プロビジョニング アプリでは、現在、計算フィールド/統合システム属性のフェッチはサポートされていません。  |
| **解像度** | この制限の回避策はありません。 |

## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](check-status-user-account-provisioning.md)
