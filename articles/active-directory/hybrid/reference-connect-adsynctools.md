---
title: Azure AD Connect:ADSyncTools PowerShell リファレンス | Microsoft Docs
description: このドキュメントでは、ADSyncTools.psm1 PowerShell モジュールの参照情報を示します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 768b037573d1114141ca9722feb6a9ecd510dd5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734524"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:ADSyncTools PowerShell リファレンス
次のドキュメントでは、Azure AD Connect に含まれる ADSyncTools.psm1 PowerShell モジュールの参照情報を示します。

## <a name="install-the-adsynctools-powershell-module"></a>ADSyncTools PowerShell モジュールをインストールする
ADSyncTools PowerShell モジュールをインストールするには、次の手順を実行します。

1.  管理者特権で Windows PowerShell を開きます
2.  次を入力するか、コピーして貼り付けます。 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Enter キーを押します。
4.  モジュールがインストールされたことを確認するには、次のように入力するか、コピーして貼り付けます。
    ```powershell
    Get-module AdSyncTools
    ```
5.  モジュールに関する情報が表示されます。


## <a name="clear-adsynctoolsmsdsconsistencyguid"></a>Clear-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory オブジェクトの mS-DS-ConsistencyGuid をクリアします
### SYNTAX
```
Clear-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
ターゲット Active Directory オブジェクトの mS-DS-ConsistencyGuid の値をクリアします。
マルチドメイン フォレスト内の Active Directory オブジェクトをサポートします。

### EXAMPLES
#### <a name="example-1"></a>例 1
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>例 2
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>例 3
```
'User1@Contoso.com' | Clear-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
mS-DS-ConsistencyGuid をクリアする AD 内のターゲット オブジェクト
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="connect-adsynctoolssqldatabase"></a>Connect-ADSyncToolsSqlDatabase
### SYNOPSIS
テスト目的で SQL データベースに接続します
### SYNTAX
```
Connect-ADSyncToolsSqlDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>]
 [[-Port] <String>] [[-UserName] <String>] [[-Password] <String>] [<CommonParameters>]
```
### DESCRIPTION
SQL 診断関連の関数とユーティリティ
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Database 'ADSync'
```
#### <a name="example-2"></a>例 2
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Instance 'INTANCE01' -Database 'ADSync'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-instance"></a>-Instance
SQL Server のインスタンス名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-database"></a>-Database
SQL Server データベース名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
SQL Server ポート (例:
49823)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-username"></a>-UserName
SQL Server ログイン ユーザー名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-password"></a>-Password
SQL Server ログイン パスワード
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="convertfrom-adsynctoolsaaddistinguishedname"></a>ConvertFrom-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Azure AD コネクタの DistinguishedName を ImmutableId に変換します
### SYNTAX
```
ConvertFrom-ADSyncToolsAadDistinguishedName [-DistinguishedName] <String> [<CommonParameters>]
```
### DESCRIPTION
Azure AD コネクタの DistinguishedName (たとえば、CN={514635484D4B376E38307176645973555049486139513D3D}) を受け取り、それぞれの base64 ImmutableID 値 (たとえば、QF5HMK7n80qvdYsUPIHa9Q==) に変換します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
ConvertFrom-ADSyncToolsAadDistinguishedName 'CN={514635484D4B376E38307176645973555049486139513D3D}'
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
Azure AD コネクタ スペースの DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="convertfrom-adsynctoolsimmutableid"></a>ConvertFrom-ADSyncToolsImmutableID
### SYNOPSIS
Base64 の ImmutableId (SourceAnchor) を GUID 値に変換します
### SYNTAX
```
ConvertFrom-ADSyncToolsImmutableID [-Value] <String> [<CommonParameters>]
```
### DESCRIPTION
ImmutableID の値を Base64 文字列から変換し、GUID 値を返します。Base64 文字列を GUID に変換できない場合は、バイト配列を返します。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
ConvertFrom-ADSyncToolsImmutableID 'iGhmiAEBERG7uxI0VniQqw=='
```
#### <a name="example-2"></a>例 2
```
'iGhmiAEBERG7uxI0VniQqw==' | ConvertFrom-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
Base64 形式の ImmutableId
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="convertto-adsynctoolsaaddistinguishedname"></a>ConvertTo-ADSyncToolsAadDistinguishedName
### SYNOPSIS
ImmutableId を Azure AD コネクタの DistinguishedName に変換します
### SYNTAX
```
ConvertTo-ADSyncToolsAadDistinguishedName [-ImmutableId] <String> [<CommonParameters>]
```
### DESCRIPTION
Azure AD コネクタの ImmutableId (SourceAnchor) (たとえば、QF5HMK7n80qvdYsUPIHa9Q==) を受け取り、それぞれの Azure AD コネクタの DistinguishedName 値 (たとえば、CN={514635484D4B376E38307176645973555049486139513D3D}) に変換します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
ConvertTo-ADSyncToolsAadDistinguishedName 'QF5HMK7n80qvdYsUPIHa9Q=='
```
### PARAMETERS
#### <a name="-immutableid"></a>-ImmutableId
ImmutableId (SourceAnchor)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="convertto-adsynctoolscloudanchor"></a>ConvertTo-ADSyncToolsCloudAnchor
### SYNOPSIS
Base64 のアンカーを CloudAnchor に変換します
### SYNTAX
```
ConvertTo-ADSyncToolsCloudAnchor [-Anchor] <String> [<CommonParameters>]
```
### DESCRIPTION
Base64 のアンカー (たとえば、VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==) を受け取り、それぞれの CloudAnchor 値 (たとえば、User_abc12345-1234-abcd-9876-ab0123456789) に変換します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
ConvertTo-ADSyncToolsCloudAnchor "VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA=="
```
#### <a name="example-2"></a>例 2
```
"VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==" | ConvertTo-ADSyncToolsCloudAnchor
```
### PARAMETERS
#### <a name="-anchor"></a>-Anchor
Base64 のアンカー
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="convertto-adsynctoolsimmutableid"></a>ConvertTo-ADSyncToolsImmutableID
### SYNOPSIS
GUID (ObjectGUID または ms-Ds-Consistency-Guid) を Base64 文字列に変換します
### SYNTAX
```
ConvertTo-ADSyncToolsImmutableID [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
GUID、GUID 文字列、またはバイト配列形式の値を Base64 文字列に変換します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
ConvertTo-ADSyncToolsImmutableID '88888888-0101-3333-cccc-1234567890cd'
```
#### <a name="example-2"></a>例 2
```
'88888888-0101-3333-cccc-1234567890cd' | ConvertTo-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
GUID、GUID 文字列、またはバイト配列
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="export-adsynctoolsaaddisconnectors"></a>Export-ADSyncToolsAadDisconnectors
### SYNOPSIS
Azure AD ディスコネクタ オブジェクトをエクスポートします
### SYNTAX
```
Export-ADSyncToolsAadDisconnectors [[-SyncObjectType] <Object>] [<CommonParameters>]
```
### DESCRIPTION
CSExport ツールを実行してすべてのディスコネクタを XML にエクスポートし、この XML 出力を取得し、UserPrincipalName、Mail、SourceAnchor、DistinguishedName、CsObjectId、ObjectType、ConnectorId、CloudAnchor が含まれた CSV ファイルに変換します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Export-ADSyncToolsDisconnectors -SyncObjectType 'PublicFolder'
```
すべての PublicFolder ディスコネクタ オブジェクトを CSV にエクスポートします
#### <a name="example-2"></a>例 2
```
Export-ADSyncToolsDisconnectors
```
すべてのディスコネクタ オブジェクトを CSV にエクスポートします
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
出力に含める ObjectType
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### INPUTS
特定のオブジェクトの種類のディスコネクタのみをエクスポートする場合は、ObjectType 引数を使用します
### OUTPUTS
以下のディスコネクタ オブジェクトが含まれた CSV ファイルをエクスポートします。 

UserPrincipalName、Mail、SourceAnchor、DistinguishedName、CsObjectId、ObjectType、ConnectorId、CloudAnchor

## <a name="export-adsynctoolshybridaadjoinreport"></a>Export-ADSyncToolsHybridAadJoinReport
### SYNOPSIS
Active Directory コンピューター オブジェクトに格納されている証明書 (具体的には、Hybrid Azure AD 参加機能によって発行された証明書) のレポートを生成します。
### SYNTAX
#### <a name="singleobject"></a>SingleObject
```
Export-ADSyncToolsHybridAadJoinReport [-DN] <String> [[-Filename] <String>] [<CommonParameters>]
```
#### <a name="multipleobjects"></a>MultipleObjects
```
Export-ADSyncToolsHybridAadJoinReport [-OU] <String> [[-Filename] <String>] [<CommonParameters>]
```
### DESCRIPTION
このツールでは、AD のコンピューター オブジェクトの UserCertificate プロパティに存在するすべての証明書を確認し、存在している、有効期限が切れていない各証明書について、その証明書が Hybrid Azure AD 参加機能に対して発行された (つまり、サブジェクト名が CN={ObjectGUID}) かどうかを検証します。
バージョン 1.4 より前では、Azure AD Connect は、証明書を少なくとも 1 つ含むすべてのコンピューターを Azure AD に同期していましたが、Azure AD Connect バージョン 1.4 以降では、ADSync エンジンが Hybrid Azure AD 参加証明書を特定できるようになり、有効な Hybrid Azure AD 参加証明書がない限り、コンピューター オブジェクトを Azure AD への同期から "cloudfilter" 処理 (除外) します。
既に AD に同期されていても、有効な Hybrid Azure AD 参加証明書がない Azure AD デバイス オブジェクトは、Azure AD Connect によって Azure AD から削除されます (CloudFiltered=TRUE)。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -DN 'CN=Computer1,OU=SYNC,DC=Fabrikam,DC=com'
```
#### <a name="example-2"></a>例 2
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -OU 'OU=SYNC,DC=Fabrikam,DC=com' -Filename "MyHybridAzureADjoinReport.csv" -Verbose
```
### PARAMETERS
#### <a name="-dn"></a>-DN
コンピューターの DistinguishedName
```yaml
Type: String
Parameter Sets: SingleObject
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-ou"></a>-OU
AD の OrganizationalUnit
```yaml
Type: String
Parameter Sets: MultipleObjects
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filename"></a>-Filename
出力 CSV ファイル名 (省略可能)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### RELATED LINKS
詳細情報: [Azure AD Connect 1.4.xx.x とデバイスの消失について理解する](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)

## <a name="export-adsynctoolsobjects"></a>Export-ADSyncToolsObjects
### SYNOPSIS
Azure AD Connect のオブジェクトを XML ファイルにエクスポートします
### SYNTAX
#### <a name="objectid"></a>ObjectId
```
Export-ADSyncToolsObjects [-ObjectId] <Object> [-Source] <Object> [-ExportSerialized] [<CommonParameters>]
```
#### <a name="distinguishedname"></a>DistinguishedName
```
Export-ADSyncToolsObjects [-DistinguishedName] <Object> [-ConnectorName] <Object> [-ExportSerialized]
 [<CommonParameters>]
```
### DESCRIPTION
Metaverse から内部 ADSync オブジェクトをエクスポートし、コネクタ スペースから関連する接続済みオブジェクトをエクスポートします
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Export-ADSyncToolsObjects -ObjectId '9D220D58-0700-E911-80C8-000D3A3614C0' -Source Metaverse
```
#### <a name="example-2"></a>例 2
```
Export-ADSyncToolsObjects -ObjectId '9e220d58-0700-e911-80c8-000d3a3614c0' -Source ConnectorSpace
```
#### <a name="example-3"></a>例 3
```
Export-ADSyncToolsObjects -DistinguishedName 'CN=User1,OU=ADSync,DC=Contoso,DC=com' -ConnectorName 'Contoso.com'
```
### PARAMETERS
#### <a name="-objectid"></a>-ObjectId
ObjectId は、それぞれのコネクタ スペースまたはメタバース内のオブジェクトの一意識別子です
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-source"></a>-Source
ソースは、オブジェクトが存在するテーブルです (ConnectorSpace または Metaverse)
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName は、それぞれのコネクタ スペース内のオブジェクトの識別子です
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-connectorname"></a>-ConnectorName
ConnectorName は、オブジェクトが存在するコネクタ スペースの名前です
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-exportserialized"></a>-ExportSerialized
ExportSerialized は、追加の XML ファイルをエクスポートします
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="export-adsynctoolsrunhistory"></a>Export-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect の実行履歴をエクスポートします
### SYNTAX
```
Export-ADSyncToolsRunHistory [-TargetName] <String> [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect の実行プロファイルと実行ステップの結果をそれぞれ CSV および XML 形式にエクスポートする関数。
結果の実行プロファイル CSV ファイルはスプレッドシートにインポートできます。また、実行ステップ XML ファイルは Import-Clixml を使用してインポートできます
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Export-ADSyncToolsRunHistory -TargetName MyADSyncHistory
```
### PARAMETERS
#### <a name="-targetname"></a>-TargetName
出力ファイルの名前
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="export-adsynctoolssourceanchorreport"></a>Export-ADSyncToolsSourceAnchorReport
### SYNOPSIS
ms-ds-Consistency-Guid レポートをエクスポートします
### SYNTAX
```
Export-ADSyncToolsSourceAnchorReport [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```
### DESCRIPTION
Import-ADSyncToolsSourceAnchor からのインポート CSV ファイルに基づいて ms-ds-Consistency-Guid レポートを生成します
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsSourceAnchorReport -Output ".\AllSyncUsers-Report"
```
#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-alternativeloginid"></a>-AlternativeLoginId
代替ログイン ID (メール) を使用します
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
CSV ファイルと LOG ファイルの出力ファイル名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="get-adsynctoolsaadobject"></a>Get-ADSyncToolsAadObject
### SYNOPSIS
指定された SyncObjectType の同期されたオブジェクトを取得します
### SYNTAX
```
Get-ADSyncToolsAadObject [-SyncObjectType] <Object> [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
指定されたブジェクト クラス (SyncObjectType) のすべての同期されたオブジェクトを Azure AD から読み取ります。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsAadObject -SyncObjectType 'publicFolder' -Credential $(Get-Credential)
```
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
オブジェクトの型
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Azure AD 全体管理者の資格情報
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

### OUTPUTS

このコマンドレットは、同期クライアントによって同期される "シャドウ" プロパティを返します。これは、Azure AD の各プロパティに格納されている実際の値とは異なる場合があります。 たとえば、未検証のドメイン サフィックス "user@nonverified.domain" と同期されるユーザーの UPN には、Azure AD でテナントの既定のドメイン "user@tenantname.onmicrosoft.com" に変換された UPN サフィックスが使用されます。 この場合、Get-ADSyncToolsAadObject では、Azure AD の実際の値 "user@tenantname.onmicrosoft.com" ではなく、"user@nonverified.domain" の "シャドウ" 値が返されます。

## <a name="get-adsynctoolsmsdsconsistencyguid"></a>Get-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory オブジェクトの ms-ds-ConsistencyGuid を取得します
### SYNTAX
```
Get-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
ターゲット Active Directory オブジェクトの mS-DS-ConsistencyGuid 属性の値を GUID 形式で返します。 マルチドメイン フォレスト内の Active Directory オブジェクトをサポートします。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>例 2
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>例 3
```
'User1@Contoso.com' | Get-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
取得する AD のターゲット オブジェクト
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect の実行履歴を取得します
### SYNTAX
```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect の実行履歴を XML 形式で返す関数
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>例 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
履歴を収集する過去の日数 (既定値 = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="get-adsynctoolsrunhistorylegacywmi"></a>Get-ADSyncToolsRunHistoryLegacyWmi
### SYNOPSIS
古いバージョンの Azure AD Connect (WMI) について Azure AD Connect の実行履歴を取得します
### SYNTAX
```
Get-ADSyncToolsRunHistoryLegacyWmi [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect の実行履歴を XML 形式で返す関数
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>例 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
履歴を収集する過去の日数 (既定値 = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="get-adsynctoolssqlbrowserinstances"></a>Get-ADSyncToolsSqlBrowserInstances
### SYNOPSIS
SQL Browser サービスから SQL Server インスタンスを取得します
### SYNTAX
```
Get-ADSyncToolsSqlBrowserInstances [[-Server] <String>]
```
### DESCRIPTION
SQL 診断関連の関数とユーティリティ
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsSqlBrowserInstances -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="get-adsynctoolstenantazureenvironment"></a>Get-ADSyncToolsTenantAzureEnvironment
### SYNOPSIS
ユーザーが属する Azure 環境を取得するためのヘルパー関数。
### SYNTAX
```
Get-ADSyncToolsTenantAzureEnvironment [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
この関数は、Oauth 検出エンドポイントを呼び出して CloudInstance と tenant_region_scope を取得し、Azure 環境を判定します。
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration

### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsTenantAzureEnvironment -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
ユーザーの PowerShell 資格情報オブジェクト:

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### INPUTS
ユーザーの PowerShell 資格情報オブジェクト

### OUTPUTS
Azure 環境 (文字列)
## <a name="get-adsynctoolstls12"></a>Get-ADSyncToolsTls12
### SYNOPSIS
クライアントまたはサーバーの .NET Framework 向け TLS 1.2 設定を取得します
### SYNTAX
```
Get-ADSyncToolsTls12 [<CommonParameters>]
```
### DESCRIPTION
.NET Framework 向け TLS 1.2 に関する情報をレジストリから読み取ります。

| Path                                                         | 名前                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Enabled                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Enabled                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

### EXAMPLES
#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsTls12
```
### PARAMETERS
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### RELATED LINKS
詳細情報: [Azure AD Connect に対する TLS 1.2 の強制](reference-connect-tls-enforcement.md)

## <a name="import-adsynctoolsobjects"></a>Import-ADSyncToolsObjects
### SYNOPSIS
XML ファイルから Azure AD Connect オブジェクトをインポートします
### SYNTAX
```
Import-ADSyncToolsObjects [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Export-ADSyncToolsObjects を使用してエクスポートされた XML ファイルから内部 ADSync オブジェクトをインポートします
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Import-ADSyncToolsObjects -Path .\20210224-003104_81275a23-0168-eb11-80de-00155d188c11_MV.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
インポートする XML ファイルのパス
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="import-adsynctoolsrunhistory"></a>Import-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect の実行履歴をインポートします
### SYNTAX
```
Import-ADSyncToolsRunHistory [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Export-ADSyncToolsRunHistory を使用して作成された XML から Azure AD Connect の実行ステップの結果をインポートする関数
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Export-ADSyncToolsRunHistory -Path .\RunHistory-RunStep.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
インポートする XML ファイルのパス
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="import-adsynctoolssourceanchor"></a>Import-ADSyncToolsSourceAnchor
### SYNOPSIS
Azure AD から ImmutableID をインポートします
### SYNTAX
```
Import-ADSyncToolsSourceAnchor [-Output] <String> [-IncludeSyncUsersFromRecycleBin] [<CommonParameters>]
```
### DESCRIPTION
ImmutableID 値を含むすべての Azure AD 同期済みユーザーを格納するファイルを GUID 形式で生成します。要件:MSOnline PowerShell モジュール
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Import-ADSyncToolsSourceAnchor -OutputFile '.\AllSyncUsers.csv'
```
#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-output"></a>-Output
出力 CSV ファイル
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Azure AD のごみ箱から同期ユーザーを取得します
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="invoke-adsynctoolssqlquery"></a>Invoke-ADSyncToolsSqlQuery
### SYNOPSIS
テスト目的でデータベースに対して SQL クエリを呼び出します
### SYNTAX
```
Invoke-ADSyncToolsSqlQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```
### DESCRIPTION
SQL 診断関連の関数とユーティリティ
### EXAMPLES
#### <a name="example-1"></a>例 1
```
New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823 | Invoke-ADSyncToolsSqlQuery
```
#### <a name="example-2"></a>例 2
```
$sqlConn = New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823
```
Invoke-ADSyncToolsSqlQuery -SqlConnection $sqlConn -Query 'SELECT *, database_id FROM sys.databases'
### PARAMETERS
#### <a name="-sqlconnection"></a>-SqlConnection
SQL 接続
```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-query"></a>-Query
SQL クエリ
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: SELECT name, database_id FROM sys.databases
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

## <a name="remove-adsynctoolsaadobject"></a>Remove-ADSyncToolsAadObject
### SYNOPSIS
孤立した同期済みオブジェクトを Azure AD から削除します
### SYNTAX
#### <a name="csvinput"></a>CsvInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-InputCsvFilename] <Object> [-WhatIf] [-Confirm]
 [<CommonParameters>]
```
#### <a name="objectinput"></a>ObjectInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-SourceAnchor] <Object> [-SyncObjectType] <Object>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
SourceAnchor と ObjecType に基づいて同期済みのオブジェクトを 10 個ずつ Azure AD から削除します。CSV ファイルは、Export-ADSyncToolsAadDisconnectors を使用して生成できます
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Remove-ADSyncToolsAadObject -InputCsvFilename .\DeleteObjects.csv -Credential (Get-Credential)
```
#### <a name="example-2"></a>例 2
```
Remove-ADSyncToolsAadObject -SourceAnchor '2epFRNMCPUqhysJL3SWL1A==' -SyncObjectType 'publicFolder' -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Azure AD 全体管理者の資格情報
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-inputcsvfilename"></a>-InputCsvFilename
CSV 入力ファイル名
```yaml
Type: Object
Parameter Sets: CsvInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-sourceanchor"></a>-SourceAnchor
オブジェクトの SourceAnchor
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-syncobjecttype"></a>-SyncObjectType
オブジェクトの型
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
コマンドレットの実行時に発生する内容を示します。
このコマンドレットは実行されません。
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
コマンドレットの実行前に確認を求めるメッセージが表示されます。
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### INPUTS
InputCsvFilename は、少なくとも 2 つの列 (SourceAnchor、SyncObjectType) がある CSV ファイルを指している必要があります
### OUTPUTS
ExportDeletions 操作の結果を表示します。免責事項: ごみ箱のあるユーザー オブジェクトを除き、この関数で削除された他の種類のオブジェクトは復旧できません。
## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates
### SYNOPSIS
UserCertificate 属性から有効期限が切れた証明書を削除するスクリプト
### SYNTAX
```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```
### DESCRIPTION
このスクリプトは、Active Directory ドメイン内のターゲット組織単位からすべてのオブジェクトを取得し、オブジェクト クラス (ユーザー/コンピューター) でフィルター処理し、UserCertificate 属性に存在する有効期限が切れたすべての証明書を削除します。
既定 (BackupOnly モード) では、期限切れの証明書をファイルにバックアップするだけで、AD 内は何も変更しません。
-BackupOnly $false を使用した場合、これらのオブジェクトの UserCertificate 属性に存在する有効期限が切れた証明書は、ファイルにコピーされた後で Active Directory から削除されます。
各証明書は別のファイル名でバックアップされます (ObjectClass_ObjectGUID_CertThumprint.cer)。このスクリプトでは、有効なまたは有効期限切れの証明書を持つすべてのユーザーと、行われた実際のアクション (スキップ/エクスポート/削除) が記載された CSV 形式のログ ファイルが作成されます。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user
#### <a name="example-2"></a>例 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false
### PARAMETERS
#### <a name="-targetou"></a>-TargetOU
AD オブジェクトを検索するターゲット OU
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-backuponly"></a>-BackupOnly
BackupOnly は AD から証明書を削除しません
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-objectclass"></a>-ObjectClass
オブジェクト クラスのフィルター
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState
### SYNOPSIS
Azure AD Connect の AutoUpgrade 状態を修復します
### SYNTAX
```
Repair-ADSyncToolsAutoUpgradeState
```
### DESCRIPTION
ビルド 1.1.524 (2017 年 5 月) で発生した、AutoUpgrade が有効なときに新しいバージョンのオンライン チェックが無効になる AutoUpgrade の問題を修正します。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Repair-ADSyncToolsAutoUpgradeState
```
## <a name="resolve-adsynctoolssqlhostaddress"></a>Resolve-ADSyncToolsSqlHostAddress
### SYNOPSIS
SQL サーバー名を解決します
### SYNTAX
```
Resolve-ADSyncToolsSqlHostAddress [-Server] <String> [<CommonParameters>]
```
### DESCRIPTION
SQL 診断関連の関数とユーティリティ
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Resolve-ADSyncToolsSqlHostAddress -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="search-adsynctoolsadobject"></a>Search-ADSyncToolsADobject
### SYNOPSIS
Active Directory フォレスト内の Active Directory オブジェクトを、その UserPrincipalName、sAMAccountName、または DistinguishedName で検索します
### SYNTAX
```
Search-ADSyncToolsADobject [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
マルチドメイン クエリをサポートし、mS-DS-ConsistencyGuid を含むすべての必須プロパティを返します。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Search-ADSyncToolsADobject 'CN=user1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>例 2
```
Search-ADSyncToolsADobject -Identity "user1@Contoso.com"
```
#### <a name="example-3"></a>例 3
```
Get-ADUser 'CN=user1,OU=Sync,DC=Contoso,DC=com' | Search-ADSyncToolsADobject
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
ConsistencyGuid を設定する AD のターゲット ユーザー
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="set-adsynctoolsmsdsconsistencyguid"></a>Set-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory オブジェクトの ms-ds-ConsistencyGuid を設定します
### SYNTAX
```
Set-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
ターゲット Active Directory ユーザーの mS-DS-ConsistencyGuid 属性の値を設定します。
マルチドメイン フォレスト内の Active Directory オブジェクトをサポートします。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value '88666888-0101-1111-bbbb-1234567890ab'
```
#### <a name="example-2"></a>例 2
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-3"></a>例 3
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' '8d6c6818-018c-4f11-9bb8-1b04e2caa1b6'
```
#### <a name="example-4"></a>例 4
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-5"></a>例 5
```
'88666888-0101-1111-bbbb-1234567890ab' | Set-ADSyncToolsMsDsConsistencyGuid -Identity User1
```
#### <a name="example-6"></a>例 6
```
'GGhsjYwBEU+buBsE4sqhtg==' | Set-ADSyncToolsMsDsConsistencyGuid User1
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
mS-DS-ConsistencyGuid を設定する AD 内のターゲット オブジェクト
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-value"></a>-Value
設定する値 (ImmutableId、バイト配列、GUID、GUID 文字列または Base64 文字列)
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="set-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
クライアントまたはサーバーの .NET Framework 向け TLS 1.2 設定を設定します
### SYNTAX
```
Set-ADSyncToolsTls12 [[-Enabled] <Boolean>] [<CommonParameters>]
```
### DESCRIPTION
.NET Framework 向け TLS 1.2 を有効または無効にするレジストリ エントリを設定します。

| Path                                                         | 名前                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Enabled                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Enabled                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

パラメーターを指定せずにコマンドレットを実行すると、.NET Framework 向け TLS 1.2 が有効になります

### EXAMPLES
#### <a name="example-1"></a>例 1
```
Set-ADSyncToolsTls12
```
#### <a name="example-2"></a>例 2
```
Set-ADSyncToolsTls12 -Enabled $true
```
### PARAMETERS
#### <a name="-enabled"></a>-Enabled
TLS 1.2 が有効
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: True
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
### RELATED LINKS
詳細情報: [Azure AD Connect に対する TLS 1.2 の強制](reference-connect-tls-enforcement.md)

## <a name="test-adsynctoolssqlnetworkport"></a>Test-ADSyncToolsSqlNetworkPort
### SYNOPSIS
SQL Server のネットワーク ポートをテストします
### SYNTAX
```
Test-ADSyncToolsSqlNetworkPort [[-Server] <String>] [[-Port] <String>]
```
### DESCRIPTION
SQL 診断関連の関数とユーティリティ
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01'
```
#### <a name="example-2"></a>例 2
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01' -Port 1433
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
[SQL Server ポート]
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport
### SYNOPSIS
Active Directory インポート手順からトレース ファイルを作成します
### SYNTAX
#### <a name="adconnectorxml"></a>ADConnectorXML
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADConnectorXML] <String> [<CommonParameters>]
```
#### <a name="adwatermarkinput"></a>ADwatermarkInput
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADwatermark] <String> [<CommonParameters>]
```
### DESCRIPTION
特定の Active Directory ウォーターマーク チェックポイント (パーティション Cookie とも呼ばれます) から実行される Active Directory インポートのすべての LDAP クエリをトレースします。 現在のフォルダーにトレース ファイル '\ADimportTrace_yyyyMMddHHmmss.log' を作成します。
-ADConnectorXML を使用するには、Synchronization Service Manager に移動します。AD コネクタを右クリックし、[Export Connector]\(コネクタのエクスポート\) を選択します。
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Trace Active Directory Import for user objects by providing an AD Connector XML file
```
Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Filter '(&(objectClass=user))' -ADConnectorXML .\ADConnector.xml
#### <a name="example-2"></a>例 2
```
Trace Active Directory Import for all objects by providing the Active Directory watermark (cookie) and AD Connector credential
```
$creds = Get-Credential Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Credential $creds -ADwatermark "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
### PARAMETERS
#### <a name="-dc"></a>-DC
ターゲット ドメイン コントローラー
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-rootdn"></a>-RootDN
フォレスト ルート DN
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
トレースする AD オブジェクトの種類。
すべてのオブジェクトの種類を表すには、"(&(objectClass=*))" を使用します
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
AD に対して LDAP クエリを実行するための資格情報を指定します
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-ssl"></a>-SSL
SSL 接続
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adconnectorxml"></a>-ADConnectorXML
AD コネクタのエクスポート XML ファイル - AD コネクタを右クリックし、[Export Connector]\(コネクタのエクスポート\) を選択します
```yaml
Type: String
Parameter Sets: ADConnectorXML
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adwatermark"></a>-ADwatermark
XML ファイルの代わりに、透かしを手動で入力します。例: $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
```yaml
Type: String
Parameter Sets: ADwatermarkInput
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery
### SYNOPSIS
LDAP クエリをトレースします
### SYNTAX
```
Trace-ADSyncToolsLdapQuery [-RootDN] <String> [-Credential] <PSCredential> [[-Server] <String>]
 [[-Port] <Int32>] [-Filter <String>] [<CommonParameters>]
```
### DESCRIPTION
{{説明を入力}}
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Trace-ADSyncToolsLdapQuery -RootDN "DC=Contoso,DC=com" -Credential $Credential
```
### PARAMETERS
#### <a name="-rootdn"></a>-RootDN
フォレストまたはドメインの DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
AD 資格情報
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-server"></a>-Server
ドメイン コントローラー名 (省略可能)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
ドメイン コントローラー ポート (既定値: 389)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: 389
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
LDAP フィルター (既定値: objectClass=*)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。
## <a name="update-adsynctoolssourceanchor"></a>Update-ADSyncToolsSourceAnchor
### SYNOPSIS
新しい ConsistencyGuid (ImmutableId) でユーザーを更新します
### SYNTAX
```
Update-ADSyncToolsSourceAnchor [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String> [-Action] <String>
 [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
ConsistencyGuid レポートから取得した新しい ConsistencyGuid (ImmutableId) 値でユーザーを更新します。この関数は WhatIf スイッチをサポートします。注: ConsistencyGuid レポートはタブ区切り文字と一緒にインポートする必要があります
### EXAMPLES
#### <a name="example-1"></a>例 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2 -WhatIf
```
#### <a name="example-2"></a>例 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-action"></a>-Action
アクション
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
LOG ファイルの出力ファイル名
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
コマンドレットの実行時に発生する内容を示します。
このコマンドレットは実行されません。
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
コマンドレットの実行前に確認を求めるメッセージが表示されます。
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。