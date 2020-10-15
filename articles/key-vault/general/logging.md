---
title: Azure Key Vault のログ記録 | Microsoft Docs
description: Azure Key Vault のログ記録を有効にすることで、お使いのキー コンテナーへのアクセスを監視する方法について説明します。これにより、指定した Azure ストレージ アカウントに情報が保存されます。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739784"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault のログ記録

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1 つまたは複数のキー コンテナーを作成したら、いつ、どのように、誰によってキー コンテナーがアクセスされるのかを監視するのが一般的です。 監視を行うには、Azure Key Vault のログ記録を有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 指定したストレージ アカウント用の **insights-logs-auditevent** という名前の新しいコンテナーが自動的に作成されます。 このストレージ アカウントを使用して複数のキー コンテナーのログを収集することができます。

キー コンテナーの操作を行ってから最大 10 分後には、ログ情報にアクセスできます。 ほとんどの場合は、これよりも早く確認できます。  ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。

Key Vault の概要については、「[Azure Key Vault とは](overview.md)」を参照してください。 Key Vault が使用可能な場所については、[価格に関するページ](https://azure.microsoft.com/pricing/details/key-vault/)をご覧ください。 [Azure Monitor for Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) の使用に関する詳細です。

## <a name="interpret-your-key-vault-logs"></a>Key Vault のログを解釈する

個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 ログ エントリの例を見てみましょう。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

次の表にフィールド名と説明を示します。

| フィールド名 | 説明 |
| --- | --- |
| **time** |日付と時刻 (UTC)。 |
| **resourceId** |Azure Resource Manager リソース ID。 Key Vault のログの場合は、常に Key Vault リソース ID となります。 |
| **operationName** |次の表に示すような操作の名前です。 |
| **operationVersion** |クライアントによって要求された REST API バージョン。 |
| **category** |結果の種類。 Key Vault のログの場合、`AuditEvent` は使用可能な唯一の値です。 |
| **resultType** |REST API 要求の結果です。 |
| **resultSignature** |HTTP の状態です。 |
| **resultDescription** |結果に関する追加の説明です (使用可能な場合)。 |
| **durationMs** |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| **callerIpAddress** |要求を行ったクライアントの IP アドレスです。 |
| **correlationId** |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| **identity** |REST API 要求に提示されたトークンからの ID です。 これは、通常、Azure PowerShell コマンドレットの実行結果として生じる要求の場合と同様に、"user"、"service principal"、または組み合わせ "user+appId" となります。 |
| **properties** |操作によって異なる情報です (**operationName**)。 ほとんどの場合、このフィールドには、クライアント情報 (クライアントから渡されたユーザー エージェント文字列)、正確な REST API 要求 URI、および HTTP 状態コードが含まれます。 さらに、要求 (**KeyCreate** や **VaultGet** など) を行った結果としてオブジェクトが返される場合、キーの URI (`id` として)、コンテナーの URI、またはシークレットの URI も含まれます。 |

**operationName** フィールドの値は、*ObjectVerb* 形式となります。 次に例を示します。

* キー コンテナーに関するすべての操作は、`Vault<action>` 形式となります (`VaultGet` や `VaultCreate` など)。
* キーに関するすべての操作は、`Key<action>` 形式となります (`KeySign` や `KeyList` など)。
* シークレットに関するすべての操作は、`Secret<action>` 形式となります (`SecretGet` や `SecretListVersions` など)。

次の表に、**operationName** の値と、対応する REST API コマンドを示します。

### <a name="operation-names-table"></a>操作名の表

| operationName | REST API コマンド |
| --- | --- |
| **認証** |Azure Active Directory エンドポイント経由で認証します |
| **VaultGet** |[キー コンテナーに関する情報を取得します](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[キー コンテナーを作成または更新します](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[キー コンテナーを削除します](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Key Vault を更新します](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[リソース グループ内のすべてのキー コンテナーを一覧表示します](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[キーを作成します](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[キーに関する情報を取得します](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[コンテナーにキーをインポートします](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[キーをバックアップします](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[キーを削除します](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[キーを復元します](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[キーで署名します](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[キーで確認します](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[キーをラップします](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[キーのラップを解除します](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[キーで暗号化します](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[キーで復号化します](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[キーを更新します](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[コンテナー内のキーを一覧表示します](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[キーのバージョンを一覧表示します](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[シークレットを作成します](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[シークレットを取得します](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[シークレットを更新します](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[シークレットを削除します](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[コンテナー内のシークレットを一覧表示します](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[シークレットのバージョンを一覧表示します](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | コンテナーのアクセス ポリシーが変更されたイベントが公開されました |
| **SecretNearExpiryEventGridNotification** |有効期限が近づいているシークレット イベントが公開されました |
| **SecretExpiredEventGridNotification** |期限切れのシークレット イベントが公開されました |
| **KeyNearExpiryEventGridNotification** |有効期限が近づいているキー イベントが公開されました |
| **KeyExpiredEventGridNotification** |期限切れのキー イベントが公開されました |
| **CertificateNearExpiryEventGridNotification** |有効期限が近づいている証明書イベントが公開されました |
| **CertificateExpiredEventGridNotification** |期限切れの証明書イベントが公開されました |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor ログの使用

Azure Monitor ログの Key Vault ソリューションを使用して、Key Vault の `AuditEvent` ログを調査することができます。 Azure Monitor ログでは、ログ クエリを使用してデータを分析し、必要な情報を取得します。 

この設定方法などの詳細については、[Azure Monitor の Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) に関するページをご覧ください。

## <a name="next-steps"></a><a id="next"></a>次のステップ

.NET Web アプリケーションでの Azure Key Vault の使用方法に関するチュートリアルについては、「[Web アプリケーションからの Azure Key Vault の使用](tutorial-net-create-vault-azure-web-app.md)」を参照してください。

プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](developers-guide.md)」を参照してください。

Azure Key Vault の Azure PowerShell 1.0 のコマンドレットの一覧については、[Azure Key Vault コマンドレット](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)に関するページを参照してください。
