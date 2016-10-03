<properties
   pageTitle="Azure ログ統合の FAQ | Microsoft Azure"
   description="この FAQ は、Azure ログ統合について寄せられる質問とその回答です。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# Azure ログ統合のよく寄せられる質問 (FAQ)

この FAQ では、Azure ログ統合に関する質問に回答します。このサービスを使用すると、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できます。この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これにより、アプリケーションに関連付けられているセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。

## Azure ログ統合が Azure VM を取得しているストレージ アカウントを表示するには、どうすればよいですか。

**azlog source list** コマンドを実行します。

## プロキシ構成を更新するには、どうすればよいですか。

プロキシ設定で Azure ストレージ アクセスが直接許可されていない場合は、**c:\\Program Files\\Microsoft Azure Log Integration** で **AZLOG.EXE.CONFIG** ファイルを開きます。ファイルを更新して、**defaultProxy** セクションに組織のプロキシ アドレスを追加します。更新の完了後、サービスを停止するには **net stop azlog** コマンドを、開始するには **net start azlog** コマンドを使用します。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## Windows イベントのサブスクリプション情報を表示するには、どうすればよいですか。

ソースを追加しているときに、**subscriptionid** を表示名に追加します。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

イベント XML には、次に示すサブスクリプション ID などのメタデータが含まれます。

![イベント XML][1]

## エラー メッセージ

### **azlog createazureid** コマンドを実行すると、次のエラーが表示されるのはなぜですか。

エラー:

  *Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'Insufficient privileges to complete the operation.'*

**Azlog createazureid** は、Azure ログインでアクセスできるサブスクリプションの、すべての Azure AD テナントにサービス プリンシパルを作成しようとします。その Azure AD テナントへの Azure ログインが、単なる Guest ユーザーとしてのログインである場合、コマンドは失敗し、[この操作を完了するのに十分な特権がありません] というメッセージが表示されます。 テナントのユーザーとしてアカウントを追加するようテナント管理者に要求してください。

### **azlog authorize** コマンドを実行すると、次のエラーが表示されるのはなぜですか。

エラー:

  *Warning creating Role Assignment - AuthorizationFailed: The client janedo@microsoft.com' with object id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

**Azlog authorize** コマンドは、(**Azlog createazureid** で作成された) Azure AD サービス プリンシパルの閲覧者のロールを、提供されたサブスクリプションに割り当てます。Azure ログインが、サブスクリプションの共同管理者または所有者としてのログインでない場合、ログインは失敗し、[承認に失敗しました] というエラー メッセージが表示されます。この操作を完了するには、共同管理者または所有者の Azure ロールベースのアクセス制御 (RBAC) が必要です。

## 監査ログのプロパティの定義はどこで確認できますか。

参照:

- [リソース マネージャーの監査操作](../resource-group-audit.md)
- [Azure Insights REST API でサブスクリプションの管理イベントの一覧を表示](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## Azure Security Center の警告の詳細はどこで確認できますか。

「[Azure Security Center でのセキュリティの警告の管理と対応](../security-center/security-center-managing-and-responding-alerts.md)」をご覧ください。

## VM 診断で収集した情報を変更するには、どうすればよいですか。

Windows の Azure 診断 *(WAD)* 構成を取得、変更、および設定する方法の詳細については、「[PowerShell を使用して Windows を実行している仮想マシンで Azure 診断を有効にする](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)」をご覧ください。サンプルを次に示します。

### WAD 構成の取得

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### WAD 構成の変更

次の例の構成では、EventID 4624 と EventID 4625 のみがセキュリティ イベント ログから収集されます。Microsoft マルウェア対策のイベントは、システム イベント ログから収集されます。XPath 式の使用の詳細については、[イベントの使用](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85\)) に関するページをご覧ください。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### WAD 構成の設定

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

変更後、ストレージ アカウントをチェックして、適切なイベントが収集されていることを確認します。

Azure ログ統合に関する質問がある場合は、[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com) 宛に電子メールを送信してください。

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->