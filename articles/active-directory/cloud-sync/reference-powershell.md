---
title: Azure AD Connect クラウド同期用 AADCloudSyncTools PowerShell モジュール
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/03/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d45c331c8af6dda32b7a47dbdb517adf1d8a4d93
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500300"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期用 AADCloudSyncTools PowerShell モジュール

AADCloudSyncTools モジュールには、Azure AD Connect クラウド同期のデプロイを管理するために使用できる一連の便利なツールが用意されています。

## <a name="prerequisites"></a>前提条件
以下の前提条件が必要です。

- このモジュールのすべての前提条件は、`Install-AADCloudSyncToolsPrerequisites` を使用して自動的にインストールすることができます。
- このモジュールでは MSAL 認証が使用されるため、MSAL.PS モジュールがインストールされている必要があります。 確認するには、PowerShell ウィンドウで `Get-module MSAL.PS -ListAvailable` を実行します。 モジュールが正しくインストールされている場合は、応答が返されます。 `Install-AADCloudSyncToolsPrerequisites` を使用して、MSAL.PS の最新バージョンをインストールできます
- AzureAD PowerShell モジュールは、このモジュールの機能の前提条件となるものではありませんが、あると便利なため、`Install-AADCloudSyncToolsPrerequisites` を使用すると自動的にインストールされます。 
- PowerShell ギャラリーからモジュールをインストールするには、TLS 1.2 の適用が必要です。 コマンドレット `Install-AADCloudSyncToolsPrerequisites` を使用すると、すべての前提条件をインストールする前に TLS 1.2 の適用が設定されます。 モジュールを手動で確実にインストールできるようにするには、`Install-Module` を使用する前に、PowerShell セッションで次のように設定します。
  ```
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>AADCloudSyncTools PowerShell モジュールをインストールする
AADCloudSyncTools モジュールをインストールして使用するには、次の手順を実行します。

1. 管理者特権で Windows PowerShell を開きます
2. `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"` を入力するか、コピーして貼り付けます
3. Enter キーを押します。
4. モジュールがインポートされたことを確認するには、`Get-module AADCloudSyncTools` を入力するか、コピーして貼り付けます
5. モジュールに関する情報が表示されます。
6. 次に、AADCloudSyncTools モジュールの前提条件をインストールするには、`Install-AADCloudSyncToolsPrerequisites` を実行します
7. 最初の実行時に、PoweShellGet モジュールが存在しない場合は、インストールされます。 新しい PowershellGet モジュールを読み込むには、PowerShell ウィンドウを閉じ、管理者特権で新しい PowerShell セッションを開きます。 
8. 手順 2 を使用してモジュールを再度インポートします。
9. `Install-AADCloudSyncToolsPrerequisites` を実行して MSAL および AzureAD モジュールをインストールします
11. すべての前提要件が正常にインストールされているはずです ![モジュールのインストール](media/reference-powershell/install-1.png)
12. 新しい PowerShell セッションで AADCloudSyncTools モジュールを使用する場合は、毎回、次を入力するかコピーして貼り付けます。
```
Import-module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
```


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools コマンドレット
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
MSAL.PS モジュールを使用して、Azure AD 管理者が Microsoft Graph にアクセスするためのトークンを要求します 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
次のように、圧縮ファイルにすべてのトラブルシューティング データをエクスポートしてパッケージ化します。
 1. 詳細トレースを設定し、プロビジョニング エージェントからのデータ収集を開始します (`Start-AADCloudSyncToolsVerboseLogs` と同じです)
 <br>これらのトレース ログは フォルダー `C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace` にあります </br>
 2. 3 分後にデータ収集を停止し、詳細トレースを無効にします (`Stop-AADCloudSyncToolsVerboseLogs` と同じです)
 <br>`-TracingDurationMins` を使用して別の時間を指定したり、`-SkipVerboseTrace` を使用して詳細トレースを完全にスキップしたりできます </br>
 3. 過去 24 時間のイベント ビューアー ログが収集されます
 4. すべてのエージェント ログ、詳細ログ、およびイベント ビューアー ログが、圧縮された ZIP ファイルとしてユーザーのドキュメント フォルダーに圧縮されます
 <br>`-OutputPath <folder path>` を使用して別の出力フォルダーを指定することもできます </br>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Azure AD テナントの詳細と内部変数の状態を表示します

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブの情報を返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのスケジュールを返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのスキーマを返します。

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Graph を使用して指定された同期ジョブ ID の同期ジョブのスキーマを取得し、すべてのフィルター グループのスコープを出力します。

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブの設定を返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのステータスを返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Graph を使用して AD2AAD または SyncFabric のサービス プリンシパルを取得します。
パラメーターを指定しない場合、AD2AAD サービス プリンシパルだけが返されます。

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
PowerShellGet v2.2.4.1 以降、および Azure AD と MSAL.PS モジュールが存在するかどうかを確認し、無い場合はインストールします。

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
パラメーターとして指定された URI、メソッド、および本文の Web 要求を呼び出します

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Azure AD PowerShell を使用して現在のアカウント (存在する場合) を削除し、Azure AD の同期アカウント認証を新しい同期アカウントでリセットします。

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
完全同期を再開します。

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
前のウォーターマークから同期を続行します。

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
'AADConnectProvisioningAgent.exe.config' を変更して詳細トレースを有効にし、AADConnectProvisioningAgent サービスを再起動します。-SkipServiceRestart を使用してサービスの再起動を阻止することはできますが、構成の変更は有効になりません。  これらのトレース ログは、C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace フォルダーにあります。

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
'AADConnectProvisioningAgent.exe.config' を変更して詳細トレースを無効にし、AADConnectProvisioningAgent サービスを再起動します。 -SkipServiceRestart を使用してサービスの再起動を阻止することはできますが、構成の変更は有効になりません。

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
同期を一時停止します。

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
