---
title: 条件付きアクセス ポリシーの変更のトラブルシューティング - Azure Active Directory
description: Azure AD 監査ログを使用して、条件付きアクセス ポリシーの変更を診断します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7683b64449a32013d7ac5e548ea9acaf85c51666
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561624"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>条件付きアクセス ポリシーの変更のトラブルシューティング

Azure Active Directory (Azure AD) 監査ログは、環境内で条件付きアクセス ポリシーの変更が発生した理由とその方法のトラブルシューティングを行う際の貴重な情報源です。

監査ログ データは既定で 30 日間のみ保持されます。これは、すべての組織に対して十分な長さではない可能性があります。 組織では、Azure AD 内の診断設定を次のように変更することで、より長い期間にわたりデータを保存できます。 

- データを Log Analytics ワークスペースに送信する
- データをストレージ アカウントにアーカイブする
- データをイベント ハブにストリーミングする
- データをパートナー ソリューションに送信する
 
これらのオプションは、**Azure portal** >  **[Azure Active Directory]** 、 **[診断設定]**  >  **[設定の編集]** にあります。 診断設定がない場合は、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../../azure-monitor/essentials/diagnostic-settings.md)」の記事に記載されている手順に従って作成します。 

## <a name="use-the-audit-log"></a>監査ログの使用

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[監査ログ]** の順に移動します。
1. クエリを実行する **日付** 範囲を選択します。
1. **[アクティビティ]** を選択し、次のいずれかを選択します
   1. **Add conditional access policy (条件付きアクセス ポリシーの追加)** - このアクティビティでは、新しく作成されたポリシーを一覧表示します
   1. **Update conditional access policy (条件付きアクセス ポリシーの更新)** - このアクティビティでは、変更されたポリシーを一覧表示します
   1. **Delete conditional access policy (条件付きアクセス ポリシーの削除)** - このアクティビティでは、削除されたポリシーを一覧表示します

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="条件付きアクセス ポリシーの古いおよび新しい JSON 値を示す監査ログ エントリ" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>Log Analytics の使用

Log Analytics を使用すると、組織では組み込みクエリまたはカスタムで作成された Kusto クエリを使用してデータを照会できます。詳細については、「[Azure Monitor でログ クエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)」を参照してください。

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="新しいおよび古い値の場所を示す条件付きアクセス ポリシーの更新に対する Log Analytics のクエリ" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

有効にしたら、**Azure portal** > **Azure AD** > **Log Analytics** でLog Analytics へのアクセスを探します。 条件付きアクセス管理者にとって最も関心のあるテーブルは、**AuditLogs** です。

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

変更は、**TargetResources** > **modifiedProperties** にあります。

## <a name="reading-the-values"></a>値の読み取り

監査ログと Log Analytics の古いおよび新しい値は JSON 形式です。 2 つの値を比較して、ポリシーに対する変更を確認します。

古いポリシーの例:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

更新されたポリシーの例:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

上記の例では、更新されたポリシーには許可コントロールの使用条件が含まれていません。

### <a name="restoring-conditional-access-policies"></a>条件付きアクセス ポリシーの復元

Microsoft Graph API を使用して条件付きアクセス ポリシーをプログラムで更新する方法の詳細については、[条件付きアクセス: プログラムによるアクセス](howto-conditional-access-apis.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory の監視とは](../reports-monitoring/overview-monitoring.md)
- [Azure Active Directory ログ分析用のビューのインストールと使用](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [条件付きアクセス:プログラムによるアクセス](howto-conditional-access-apis.md)
