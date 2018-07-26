---
title: Azure AD パスワード保護プレビューのトラブルシューティングとログイン
description: Azure AD パスワード保護プレビューのログと一般的なトラブルシューティングについて説明します
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1eea6380d4276644db0c7681f23a4b0c5e79ff09
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187351"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>プレビュー: Azure AD パスワード保護の監視、レポート、トラブルシューティング

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Azure AD のデプロイ後、パスワード保護の監視と報告は重要なタスクです。 この記事では、各サービスが情報をログに記録する場所と、Azure AD パスワード保護の使用について報告する方法を理解できるように詳しく説明しています。

## <a name="on-premises-logs-and-events"></a>オンプレミスのログとイベント

### <a name="dc-agent-service"></a>DC エージェント サービス

各ドメイン コントローラーで、DC エージェント サービス ソフトウェアは、パスワード検証の結果 (およびその他の状態) をローカルのイベント ログ (\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin) に書き込みます。

イベントは、以下の範囲を使用して、さまざまな DC エージェント コンポーネントによってログに記録されます。

|コンポーネント |イベント ID の範囲|
| --- | --- |
|DC エージェントのパスワード フィルター DLL| 10000 から 19999|
|DC エージェント サービスのホスティング プロセス| 20000 から 29999|
|DC エージェント サービス ポリシー検証ロジック| 30000 から 39999|

パスワード検証操作が適切に実行されるために、通常、DC エージェントのパスワード フィルター dll から 1 つのイベントがログに記録されます。 パスワード検証操作が失敗した場合、通常、DC エージェント サービスのイベントと DC エージェント パスワード フィルター DLL のイベントの 2 つがログに記録されます。

これらの状況をキャプチャする各イベントは、次の要因に基づいてログに記録されます。

* 特定のパスワードが設定されているか、変更されているか。
* 特定のパスワードの検証に合格したか、失敗したか。
* Microsoft グローバル ポリシーと組織ポリシーのどちらのために検証が失敗したか。
* 現在のパスワード ポリシーで、監査のみモードが現在オンかオフか。

主なパスワード検証関連イベントは次のとおりです。

|   |パスワードの変更 |パスワードの設定|
| --- | :---: | :---: |
|合格 |10014 |10015|
|失敗 (顧客のパスワード ポリシーに合格しなかった)| 10016、30002| 10017、30003|
|失敗 (Microsoft のパスワード ポリシーに合格しなかった)| 10016、30004| 10017、30005|
|監査のみの合格 (顧客のパスワード ポリシーに失敗)| 10024、30008| 10025、30007|
|監査のみの合格 (Microsoft のパスワード ポリシーに失敗)| 10024、30010| 10025、30009|

> [!TIP]
> 受信したパスワードは、まず Microsoft のグローバル パスワード リストに対して検証されます。それに失敗すると、以降の処理は実行されません。 これは Azure のパスワード変更に対して実行される動作と同じです。

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>イベント ID 10014 の成功したパスワード設定のサンプル イベント ログ メッセージ

The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>イベント ID 10017 および 30003 の失敗したパスワード設定のサンプル イベント ログ メッセージ

10017:

The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185 FullName:

30003:

The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185 FullName:

注意が必要なその他の主なイベント ログ メッセージは次のとおりです。

#### <a name="sample-event-log-message-for-event-id-30001"></a>イベント ID 30001 のサンプル イベント ログ メッセージ

The password for the specified user was accepted because an Azure password policy is not available yet

UserName: <user> FullName: <user>

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.

#### <a name="sample-event-log-message-for-event-id-30006"></a>イベント ID 30006 のサンプル イベント ログ メッセージ

The service is now enforcing the following Azure password policy.

 AuditOnly: 1

 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z

 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z

 Enforce tenant policy: 1

#### <a name="dc-agent-log-locations"></a>DC エージェントのログの場所

DC エージェント サービスは、操作関連のイベント ログも \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational に記録します

DC エージェント サービスは、詳細なデバッグレベルのトレース イベント ログも \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace に記録することもできます

> [!WARNING]
> Trace ログは既定で無効です。 有効にすると、このログは大量のイベントを受け取り、ドメイン コントローラーのパフォーマンスに影響を与える可能性があります。 そのため、この拡張ログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD パスワード保護プロキシ サービス

パスワード保護プロキシ サービスは、イベント ログ \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational に最低限のイベント セットを発行します

パスワード保護プロキシ サービスは、ログ \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace に詳細なデバッグレベルのトレース イベントを発行することもできます。

> [!WARNING]
> Trace ログは既定で無効です。 有効にすると、このログは大量のイベントを受け取り、プロキシ ホストのパフォーマンスに影響を与える可能性があります。 そのため、このログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

### <a name="dc-agent-discovery"></a>DC エージェントの検出

`Get-AzureADPasswordProtectionDCAgent` コマンドレットを使用すると、ドメインまたはフォレスト内で実行されているさまざまな DC エージェントに関する基本的な情報を表示できます。 この情報は、実行中の DC エージェント サービスによって登録された serviceConnectionPoint オブジェクトから取得されます。 このコマンドレットの出力例は次のとおりです。

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

さまざまなプロパティは、各 DC エージェント サービスによって約 1 時間ごとに更新されます。 さらに、データは Active Directory レプリケーションの待機時間の影響を受けます。

コマンドレットのクエリの範囲は、-Forest パラメーターまたは -Domain パラメーターの使用の影響を受ける可能性があります。

### <a name="emergency-remediation"></a>緊急時の修復

DC エージェント サービスが問題の原因である残念な状況が発生した場合、DC エージェント サービスは直ちにシャットダウンされる可能性があります。 DC エージェントのパスワード フィルター dll が実行中ではないサービスを呼び出そうとすると、警告イベント (10012、10013) がログに記録されますが、その間にすべての受信パスワードが承認されます。 DC エージェント サービスは、必要に応じて Windows サービス コントロール マネージャーを使用してスタートアップの種類を "無効" に構成することもできます。

### <a name="performance-monitoring"></a>パフォーマンスの監視

DC エージェント サービス ソフトウェアによって、**Azure AD password protection** というパフォーマンス カウンター オブジェクトがインストールされます。 現在、次のパフォーマンス カウンターを使用できます。

|パフォーマンス カウンター名 | 説明|
| --- | --- |
|Passwords processed |このカウンターには、前回の再起動以降に処理された (承認または拒否された) パスワードの合計数が表示されます。|
|Passwords accepted |このカウンターには、前回の再起動以降に承認されたパスワードの合計数が表示されます。|
|Passwords rejected |このカウンターには、前回の再起動以降に拒否されたパスワードの合計数が表示されます。|
|Password filter requests in progress |このカウンターには、現在進行中のパスワード フィルター要求の数が表示されます。|
|Peak password filter requests |このカウンターには、前回の再起動以降の同時パスワード フィルター要求のピーク数が表示されます。|
|Password filter request errors |このカウンターには、前回の再起動以降にエラーのために失敗したパスワード フィルター要求の合計数が表示されます。 Azure AD パスワード保護 DC エージェント サービスが実行されていないと、エラーが発生する可能性があります。|
|Password filter requests/sec |このカウンターには、パスワードの処理速度が表示されます。|
|Password filter request processing time |このカウンターには、パスワード フィルター要求の処理に必要な平均時間が表示されます。|
|Peak password filter request processing time |このカウンターには、前回の再起動以降のパスワード フィルター要求のピーク処理時間が表示されます。|
|Passwords accepted due to audit mode |このカウンターには、通常は拒否されるところ、パスワード ポリシーが監査モードに構成されていたために承認されたパスワードの合計数 (前回の再起動以降) が表示されます。|

## <a name="directory-services-repair-mode"></a>ディレクトリ サービスの修復モード

ドメイン コントローラーがディレクトリ サービスの修復モードで起動された場合、DC エージェント サービスでそれが検出され、現在アクティブなポリシー構成に関係なく、すべてのパスワード検証または適用アクティビティが無効にされます。

## <a name="domain-controller-demotion"></a>ドメイン コントローラーの降格

DC エージェント ソフトウェアを実行中でもドメイン コントローラーを降格させることができます。 管理者は、DC エージェント ソフトウェアの実行は維持され、降格手続き中は現在のパスワード ポリシーが継続的に適用されていることに注意してください。 新しいローカル管理者アカウントのパスワード (降格操作の一環で指定されます) は、他のパスワードと同様に検証されます。 DC の降格手続きの一環でローカル管理者アカウントに安全なパスワードを選択することをお勧めします。ただし、DC エージェント ソフトウェアによる新しいローカル管理者アカウントのパスワードの検証は、既存の降格操作手続きに影響する可能性があります。
降格が成功し、ドメイン コントローラーが再起動され、通常のメンバー サーバーとして改めて実行されると、DC エージェント ソフトウェアはパッシブ モードで動作するようになります。 このソフトウェアはいつでもアンインストールできます。

## <a name="removal"></a>削除

パブリック プレビュー ソフトウェアをアンインストールし、関連するすべての状態をドメインとフォレストからクリーンアップする場合、次の手順で実行できます。

> [!IMPORTANT]
> これらの手順は、順番に実行することが重要です。 パスワード保護プロキシ サービスのインスタンスを実行中のままにすると、定期的に serviceConnectionPoint オブジェクトが再作成されるだけでなく、定期的に sysvol 状態が再作成されます。

1. すべてのマシンからパスワード保護プロキシ ソフトウェアをアンインストールします。 この手順では、再起動する**必要はありません**。
2. すべてのドメイン コントローラーから DC エージェント ソフトウェアをアンインストールします。 この手順では、再起動する**必要があります**。
3. 各ドメイン名前付けコンテキストのすべてのプロキシ サービス接続ポイントを手動で削除します。 これらのオブジェクトの場所は、次の Active Directory Powershell コマンドで検出できます。
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 変数値の末尾のアスタリスク ("*") は省略しないでください。

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。 

4. 各ドメイン名前付けコンテキストに含まれるすべての DC エージェント接続ポイントを手動で削除します。 パブリック プレビュー ソフトウェアの展開の規模によっては、フォレスト内のドメイン コントローラーごとにこのようなオブジェクトが 1 つ存在することがあります。 そのオブジェクトの場所は、次の Active Directory Powershell コマンドで検出できます。

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。

5. フォレストレベルの構成状態を手動で削除します。 フォレストの構成状態は、Active Directory 構成の名前付けコンテキストのコンテナーに保持されます。 次のように検出および削除できます。

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. 次のフォルダーとそのすべての内容を手動で削除して、すべての sysvol 関連の状態を手動で削除します。

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   必要に応じて、特定のドメイン コントローラーのローカルでこのパスにアクセスすることもできます。既定の場所は次のようなパスになります。

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   sysvol 共有が既定以外の場所に設定されている場合は、別のパスになります。

## <a name="next-steps"></a>次の手順

グローバルおよびカスタムの禁止パスワード リストの詳細については、[不適切なパスワードの禁止](concept-password-ban-bad.md)に関する記事を参照してください。
