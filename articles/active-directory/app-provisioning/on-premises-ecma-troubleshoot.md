---
title: ECMA Connector Host と Azure AD に関する問題のトラブルシューティング
description: ECMCA Connector Host のインストール時や使用時に遭遇する可能性のあるさまざまな問題をトラブルシューティングする方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61bc113d03e1c05a1569e96bb340436ce35e23d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963969"
---
# <a name="troubleshooting-ecma-connector-host-issues"></a>ECMA Connector Host に関する問題のトラブルシューティング

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。


## <a name="troubleshoot-test-connection-issues"></a>テスト接続の問題をトラブルシューティングする 
ECMA ホストとプロビジョニング エージェントを構成したら、Azure AD プロビジョニング サービスからプロビジョニング エージェント > ECMA ホスト > アプリケーションへの接続をテストします。 このエンド ツー エンド テストは、Azure portal からアプリケーションの [test connection]\(テスト接続\) をクリックすることで実行できます。 テスト接続に失敗した場合は、次のトラブルシューティング手順を試してください。

 1. エージェントと ECMA ホストが実行中であることを確認します。
     1. エージェントがインストールされているサーバーの **スタート** ボタンをクリックし、 **[ファイル名を指定して実行]** で「**Services.msc**」と入力して、 **[サービス]** を開きます。
     2. **[サービス]** に **Microsoft Azure AD Connect Agent Updater**、**Microsoft Azure AD Connect Provisioning Agent**、**Microsoft ECMA2Host** の各サービスが存在し、その状態が *[実行中]* になっていることを確認します。 
![ECMA サービスが実行中](./media/on-premises-ecma-troubleshoot/tshoot-1.png)

 2. ECMA Host がインストールされているフォルダーに移動し、Troubleshooting、Scripts、TestECMA2HostConnection の順に移動して、スクリプトを実行します。  ECMA Connector Host が動作していて要求に応答することを確認するために、このスクリプトから SCIM GET 要求または POST 要求が送信されます。
    ECMA Connector Host サービス自体と同じコンピューターで実行されている必要があります。
 3. Azure portal からアプリケーションに移動してエージェントがアクティブであることを確認し、[admin connectivity]\(管理者接続\)、[agent]\(エージェント\) ドロップダウンの順にクリックして、エージェントがアクティブであることを確認します。
 4. 提供されたシークレット トークンがオンプレミスのシークレット トークンと同じであるかどうかを確認します (オンプレミスに移動し、再度シークレット トークンを指定してから、Azure portal にそれをコピーする必要があります)。
 5. Azure portal で、少なくとも 1 つのエージェントがアプリケーションに割り当てられていることを確認します。
 6. エージェントの割り当て後、登録が完了するまで 10 分から 20 分待つ必要があります。  登録が完了するまで、接続テストは機能しません。
 7. 有効な証明書を使用していることを確認します。 ECMA ホストの [設定] タブに移動すると、新しい証明書を生成できます。
 8. VM 上のタスク バーに移動し、Microsoft Azure AD Connect プロビジョニング エージェントを検索して、プロビジョニング エージェントを再起動します。 右クリックで停止してから開始してください。
 9. Azure portal でテナントの URL を指定するときは、必ず次のパターンに従ってください。 localhost はホスト名に置き換え可能ですが、必須ではありません。 "connectorName" は、ECMA ホストで指定したコネクタの名前に置き換えます。
    ```
    https://localhost:8585/ecma2host_connectorName/scim
    ```

## <a name="unable-to-configure-ecma-host-view-logs-in-event-viewer-or-start-ecma-host-service"></a>ECMA ホストの構成、イベント ビューアーでのログの表示、または ECMA ホスト サービスの起動ができない

#### <a name="the-following-issues-can-be-resolved-by-running-the-ecma-host-as-an-admin"></a>以下の問題は、ECMA ホストを管理者として実行することで解決できます。

* ECMA ホスト ウィザードを開くときにエラーが発生する![ECMA ウィザード エラー](./media/on-premises-ecma-troubleshoot/tshoot-2.png)

* ECMA ホスト ウィザードは構成できたが、ECMA ホスト ログを表示できない。 この場合は、ホストを管理者として開き、コネクタをエンド ツー エンドでセットアップする必要があります。 これは、既存のコネクタをエクスポートしてから再度インポートすることで簡単に行えます。 

   ![ホスト ログ](./media/on-premises-ecma-troubleshoot/tshoot-3.png)

* ECMA ホスト ウィザードは構成できたが、ECMA ホスト サービスを開始できない![ホスト サービス](./media/on-premises-ecma-troubleshoot/tshoot-4.png)


## <a name="turning-on-verbose-logging"></a>詳細ログをオンにする 

既定では、ECMA Connector Host の swithValue は Error に設定されています。  つまり、ログに記録されるのはエラー イベントだけです。  ECMA ホスト サービスと ECMA ホスト ウィザードの詳細ログを有効にするには、 次に示すとおり、両方の場所で "switchValue" を Verbose に設定します。

サービスの詳細ログのファイルの場所: c:\program files\Microsoft ECMA2Host\Service\Microsoft.ECMA2Host.Service.exe.config
  ```
  <?xml version="1.0" encoding="utf-8"?> 
  <configuration> 
      <startup>  
          <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6" /> 
      </startup> 
      <appSettings> 
        <add key="Debug" value="true" /> 
      </appSettings> 
      <system.diagnostics> 
        <sources> 
      <source name="ConnectorsLog" switchValue="Verbose"> 
            <listeners> 
              <add initializeData="ConnectorsLog" type="System.Diagnostics.EventLogTraceListener, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ConnectorsLog" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack"> 
                <filter type=""/> 
              </add> 
            </listeners> 
          </source> 
          <!-- Choose one of the following switchTrace:  Off, Error, Warning, Information, Verbose --> 
          <source name="ECMA2Host" switchValue="Verbose"> 
            <listeners>  
              <add initializeData="ECMA2Host" type="System.Diagnos
  ```

ウィザードの詳細ログのファイルの場所: C:\Program Files\Microsoft ECMA2Host\Wizard\Microsoft.ECMA2Host.ConfigWizard.exe.config
  ```
        <source name="ConnectorsLog" switchValue="Verbose"> 
          <listeners> 
            <add initializeData="ConnectorsLog" type="System.Diagnostics.EventLogTraceListener, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ConnectorsLog" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack"> 
              <filter type=""/> 
            </add> 
          </listeners> 
        </source> 
        <!-- Choose one of the following switchTrace:  Off, Error, Warning, Information, Verbose --> 
        <source name="ECMA2Host" switchValue="Verbose"> 
          <listeners> 
            <add initializeData="ECMA2Host" type="System.Diagnostics.EventLogTraceListener, System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ECMA2HostListener" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack" /> 
  ```

## <a name="target-attribute-missing"></a>ターゲット属性がない 
ターゲット アプリケーションの属性は、プロビジョニング サービスによって自動的に検出されます。 Azure portal のターゲット属性リストにターゲット属性が存在しないことがわかったら、次のトラブルシューティング手順を実行してください。

 1. Azure portal への公開対象としてその属性が選択されていることを、ECMA ホスト構成の [Select Attributes]\(属性の選択\) ページを見て確認します。
 2. ECMA ホスト サービスが有効になっていることを確認します。 
 3. ECMA ホストのログを見て、/schemas 要求が実行されたことを確認し、その応答で属性を確認します。 これは、問題のトラブルシューティングをサポートするうえで有益な情報です。 

## <a name="collect-logs-from-event-viewer-as-a-zip-file"></a>イベント ビューアーから ZIP ファイルとしてログを収集する
ECMA ホストがインストールされているフォルダーに移動し、Troubleshooting、Scripts の順に移動します。 `CollectTroubleshootingInfo` スクリプトを管理者として実行します。これで、ログを ZIP ファイルに収集してエクスポートすることができます。  

## <a name="reviewing-events-in-the-event-viewer"></a>イベント ビューアーでイベントを確認する

ECMA Connector Host のスキーマ マッピングが構成されたら、サービスを開始して受信接続をリッスンします。  そのうえで、受信要求を監視します。 これを行うには、次を実行します。

  1. スタート メニューをクリックし、「**イベント ビューアー**」と入力して、[イベント ビューアー] をクリックします。 
  2. **イベント ビューアー** で、 **[アプリケーションとサービス ログ]** を展開し、 **[Microsoft ECMA2Host Logs]\(Microsoft ECMA2Host ログ\)** を選択します。     
  3. コネクタ ホストで変更が受信されると、イベントがアプリケーション ログに書き込まれます。 



## <a name="understanding-incoming-scim-requests"></a>受信 SCIM 要求について 

Azure AD からプロビジョニング エージェントやコネクタ ホストに対して行われる要求には、SCIM プロトコルが使用されます。 ホストからアプリに対して行われる要求には、アプリでサポートされるプロトコルが使用され、ホストからエージェントへ、さらに Azure AD に対して行われる要求には SCIM が使用されます。 SCIM の実装の詳細については、[こちら](use-scim-to-provision-users-and-groups.md)を参照してください。  

通常、Azure AD プロビジョニング サービスは、各プロビジョニング サイクルの最初やオンデマンド プロビジョニングの実行前、テスト接続の実行時に、ターゲット エンドポイントが利用可能であること、また SCIM に準拠した応答を返すことを確認するために、[ダミー ユーザー](use-scim-to-provision-users-and-groups.md#request-3)の get user 呼び出しを実行します。 


## <a name="how-do-i-troubleshoot-the-provisioning-agent"></a>プロビジョニング エージェントをトラブルシューティングする方法
### <a name="agent-failed-to-start"></a>エージェントが起動しない

次の内容のエラー メッセージが表示されることがあります。

**Service 'Microsoft Azure AD Connect Provisioning Agent' failed to start.Verify that you have sufficient privileges to start the system. (サービス 'Microsoft Azure AD Connect Provisioning Agent' を開始できませんでした。システム サービスを起動するために十分な特権を持っていることを確認してください。)** 

この問題は、通常、インストーラー (NT SERVICE\AADConnectProvisioningAgent) によって作成されたローカル NT サービスのログオン アカウントにアクセス許可が適用されないようにするグループ ポリシーが原因で発生します。 サービスを開始するには、これらのアクセス許可が必要です。

この問題を解決するには、次の手順に従ってください。

1. 管理者アカウントでサーバーにサインインします。
1. **[サービス]** を開きます。これには、そこに直接移動するか、**スタート** ボタンをクリックし、 **[ファイル名を指定して実行]** で「**Services.msc**」と入力します。
1. **[サービス]** で **[Microsoft Azure AD Connect プロビジョニング エージェント]** をダブルクリックします。
1. **[ログオン]** タブで、 **[このアカウント]** をドメイン管理者に変更します。その後、サービスを再起動します。 

このテストでは、エージェントがポート 443 を介して Azure と通信できることを確認します。 ブラウザーを開き、エージェントがインストールされているサーバーから前の URL に移動します。

### <a name="agent-times-out-or-certificate-is-invalid"></a>エージェントがタイムアウトになるか証明書が無効である

エージェントを登録しようとしたときに、次のエラー メッセージが表示されることがあります。

![エージェントのタイムアウト](./media/on-premises-ecma-troubleshoot/tshoot-5.png)

この問題は、通常、エージェントがハイブリッド ID サービスに接続できないことが原因で発生し、HTTP プロキシを構成する必要があります。 この問題を解決するには、送信プロキシを構成します。 

プロビジョニング エージェントでは、送信プロキシの使用がサポートされます。 構成するには、エージェントの構成ファイル *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* を編集します。ファイルの末尾近くにある `</configuration>` の終了タグの直前に次の行を追加します。
`[proxy-server]` 変数と `[proxy-port]` 変数をお使いのプロキシ サーバー名とポート値に置き換えてください。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="agent-registration-fails-with-security-error"></a>セキュリティ エラーが発生してエージェントの登録が失敗する

クラウド プロビジョニング エージェントのインストール時にエラー メッセージが表示されることがあります。

この問題は、通常、ローカルの PowerShell 実行ポリシーのせいで、エージェントで PowerShell 登録スクリプトを実行できないことが原因で発生します。

この問題を解決するには、サーバー上の PowerShell 実行ポリシーを変更します。 マシンとユーザーのポリシーを *Undefined* または *RemoteSigned* に設定する必要があります。 *Unrestricted* として設定されている場合、このエラーが表示されます。 詳細については、[PowerShell 実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)に関する記事を参照してください。 

### <a name="log-files"></a>ログ ファイル

既定では、エージェントによって発行されるエラー メッセージとスタック トレース情報は最小限に抑えられています。 これらのトレース ログは、**C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace** フォルダーにあります。

エージェント関連の問題をトラブルシューティングするための追加の詳細情報を収集するには、次の手順に従います。

1.  [こちら](../../active-directory/cloud-sync/reference-powershell.md#install-the-aadcloudsynctools-powershell-module)の説明に従って、AADCloudSyncTools PowerShell モジュールをインストールします。
2. `Export-AADCloudSyncToolsLogs` PowerShell コマンドレットを使用して情報をキャプチャします。  データ収集は、次のスイッチを使用して微調整できます。
      - SkipVerboseTrace: 詳細ログをキャプチャせずに現在のログのみをエクスポートします (既定値 = false)
      - TracingDurationMins: 別のキャプチャ期間を指定します (既定値 = 3 分)
      - OutputPath: 別の出力パスを指定します (既定値 = ユーザーのドキュメント)

---------------------

Azure AD では、クラウドでプロビジョニング サービスを監視するだけでなく、オンプレミスでログを収集することもできます。 プロビジョニング サービスは、同期プロセスの一環として評価された各ユーザーのログを出力します。 それらのログは、[Azure portal の UI、API、Log Analytics](../reports-monitoring/concept-provisioning-logs.md) を通じて利用することができます。 また、ECMA ホストは、受信した各プロビジョニング要求および Azure AD に送信した応答を示すログをオンプレミスで生成します。

### <a name="agent-installation-fails"></a>エージェントのインストールの失敗
* エラー "`System.ComponentModel.Win32Exception: The specified service already exists` (System.ComponentModel.Win32Exception: 指定されたサービスは既に開始されています)" は、以前の ECMA ホストが正しくアンインストールされなかったことを示します。 ホスト アプリケーションをアンインストールしてください。 Program Files に移動し、ECMA ホストのフォルダーを削除します。 バックアップ用に構成ファイルを保存することもできます。 
* 次のエラーは、前提条件が満たされていないことを示しています。 .NET 4.7.1 がインストールされていることを確認してください。

  ```
    Method Name : <>c__DisplayClass0_1 : 
    RegisterNotLoadedAssemblies Error during load assembly: System.Management.Automation.resources.dll
    --------- Outer Exception Data ---------
    Message: Could not load file or assembly 'file:///C:\Program Files\Microsoft ECMA2Host\Service\ECMA\System.Management.Automation.resources.dll' or one of its dependencies. The system cannot find the file specified.

  ```


## <a name="next-steps"></a>次の手順

- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)
- [チュートリアル: ECMA Connector Host 汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)