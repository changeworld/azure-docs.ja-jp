---
title: 'Azure AD Connect: ADSync PowerShell リファレンス | Microsoft Docs'
description: このドキュメントでは、ADSync.psm1 PowerShell モジュールの参照情報を示します。
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
ms.openlocfilehash: 19d3b2dc72e31cef290bd5253e7b173d624488af
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228741"
---
# <a name="azure-ad-connect--adsync-powershell-reference"></a>Azure AD Connect: ADSync PowerShell リファレンス
次のドキュメントでは、Azure AD Connect に含まれる ADSync.psm1 PowerShell モジュールの参照情報を示します。


## <a name="add-adsyncaddsconnectoraccount"></a>Add-ADSyncADDSConnectorAccount

 ### <a name="synopsis"></a>概要
 このコマンドレットは、Azure AD と同期エンジンの両方で、サービス アカウントのパスワードをリセットして更新します。

 ### <a name="syntax"></a>SYNTAX
  #### <a name="byidentifier"></a>byIdentifier
   ```
     Add-ADSyncADDSConnectorAccount [-Identifier] <Guid> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 #### <a name="byname"></a>byName
   ```
     Add-ADSyncADDSConnectorAccount [-Name] <String> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 ### <a name="description"></a>Description
 このコマンドレットは、Azure AD と同期エンジンの両方で、サービス アカウントのパスワードをリセットして更新します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
   ```powershell
     PS C:\> Add-ADSyncADDSConnectorAccount -Name contoso.com -EACredential $EAcredentials
   ```

 contoso.com に接続されているサービス アカウントのパスワードをリセットします。

 ### <a name="parameters"></a>PARAMETERS

   #### <a name="-eacredential"></a>-EACredential
   Active Directory のエンタープライズ管理者アカウントの資格情報。

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
   ```

  #### <a name="-identifier"></a>-Identifier
  サービス アカウントのパスワードをリセットする必要があるコネクタの識別子。

   ```yaml
     Type: Guid
     Parameter Sets: byIdentifier
     Aliases:

     Required: True
     Position: 0
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="-name"></a>-Name
  コネクタの名前。

   ```yaml
     Type: String
     Parameter Sets: byName
     Aliases:
 
     Required: True
     Position: 1
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="commonparameters"></a>共通パラメーター
  このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

  #### <a name="systemguid"></a>System.Guid

  #### <a name="systemstring"></a>System.String

 ### <a name="outputs"></a>出力

  #### <a name="systemobject"></a>System.Object



## <a name="disable-adsyncexportdeletionthreshold"></a>Disable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>概要
 エクスポート ステージでの削除しきい値の機能を無効にします。

 ### <a name="syntax"></a>SYNTAX
   
   ```
     Disable-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm]
     [<CommonParameters>]
   ```

 ### <a name="description"></a>Description
 エクスポート ステージでの削除しきい値の機能を無効にします。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
   ```powershell
     PS C:\> Disable-ADSyncExportDeletionThreshold -AADCredential $aadCreds
   ```

 指定された AAD 資格情報を使用して、エクスポート削除しきい値の機能を無効にします。

 ### <a name="parameters"></a>PARAMETERS

  #### <a name="-aadcredential"></a>-AADCredential
  AAD 資格情報。

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: 1
     Default value: None
     Accept pipeline input: True (ByPropertyName)
     Accept wildcard characters: False
  ```

  #### <a name="-confirm"></a>-Confirm
  確認を求めるダイアログを表示するためのパラメーター スイッチ。

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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

  #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>出力

  #### <a name="systemobject"></a>System.Object

## <a name="enable-adsyncexportdeletionthreshold"></a>Enable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>概要
 エクスポート削除しきい値の機能を有効にし、しきい値の値を設定します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Enable-ADSyncExportDeletionThreshold [-DeletionThreshold] <UInt32> [[-AADCredential] <PSCredential>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 エクスポート削除しきい値の機能を有効にし、しきい値の値を設定します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Enable-ADSyncExportDeletionThreshold -DeletionThreshold 777 -AADCredential $aadCreds
 ```

 エクスポート削除しきい値の機能を有効にし、削除しきい値を 777 に設定します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 資格情報。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-deletionthreshold"></a>-DeletionThreshold
 削除しきい値。

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemuint32"></a>System.UInt32
 
 #### <a name="sytemmanagementautomationpscredential"></a>Sytem.Management.Automation.PSCredential

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncautoupgrade"></a>Get-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>概要
 インストールの AutoUpgrade の状態を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncAutoUpgrade [-Detail] [<CommonParameters>]
 ``` 

 ### <a name="description"></a>Description
 インストールの AutoUpgrade の状態を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncAutoUpgrade -Detail
 ```

 インストールの AutoUpgrade の状態を返し、AutoUpgrade が一時停止されている場合は一時停止の理由を示します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-detail"></a>-Detail
 AutoUpgrade の状態が一時停止されている場合、このパラメーターを使用すると、一時停止の理由が表示されます。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobject"></a>Get-ADSyncCSObject

 ### <a name="synopsis"></a>概要
 指定されたコネクタ スペース オブジェクトを取得します。

 ### <a name="syntax"></a>SYNTAX
 
 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Get-ADSyncCSObject [-Identifier] <Guid> [<CommonParameters>]
 ```
 
 #### <a name="searchbyconnectoridentifierdistinguishedname"></a>SearchByConnectorIdentifierDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectoridentifier"></a>SearchByConnectorIdentifier
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectornamedistinguishedname"></a>SearchByConnectorNameDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectorname"></a>SearchByConnectorName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 指定されたコネクタ スペース オブジェクトを取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 contoso.com ドメインのユーザー fabrikam の CS オブジェクトを取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 コネクタの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier 
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタの名前。

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 コネクタ スペース オブジェクトの識別名。

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 コネクタ スペース オブジェクトの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-maxresultcount"></a>-MaxResultCount
 結果セットの最大数。

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-skipdnvalidation"></a>-SkipDNValidation
 DN の検証をスキップするパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-startindex"></a>-StartIndex
 返却範囲を指定する開始インデックス。

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-transient"></a>-Transient
 一時的な CS オブジェクトを取得するためのパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier, SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobjectlog"></a>Get-ADSyncCSObjectLog

 ### <a name="synopsis"></a>概要
 コネクタ スペース オブジェクトのログ エントリを取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncCSObjectLog [-Identifier] <Guid> [-Count] <UInt32> [<CommonParameters>]
 ```
 
 ### <a name="description"></a>Description
 コネクタ スペース オブジェクトのログ エントリを取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncCSObjectLog -Identifier "00000000-0000-0000-0000-000000000000" -Count 1
 ```

 指定された識別子を持つオブジェクトを 1 つ返します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-count"></a>-Count
 取得するコネクタ スペース オブジェクトのログ エントリで求められる最大数。

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 コネクタ スペース オブジェクトの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncdatabaseconfiguration"></a>Get-ADSyncDatabaseConfiguration

 ### <a name="synopsis"></a>概要
 ADSync データベースの構成を取得します。
 
 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncDatabaseConfiguration [<CommonParameters>]
 ```
 
 ### <a name="description"></a>Description
 ADSync データベースの構成を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncDatabaseConfiguration
 ```

 ADSync データベースの構成を取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncexportdeletionthreshold"></a>Get-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>概要
 AAD からエクスポート削除しきい値を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 AAD からエクスポート削除しきい値を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncExportDeletionThreshold -AADCredential $aadCreds
 ```

 指定された AAD 資格情報を使用して、AAD からエクスポート削除しきい値を取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 資格情報。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncmvobject"></a>Get-ADSyncMVObject

 ### <a name="synopsis"></a>概要
 メタバース オブジェクトを取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncMVObject -Identifier <Guid> [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 メタバース オブジェクトを取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncMVObject -Identifier "00000000-0000-0000-0000-000000000000"
 ```

 指定された識別子を持つメタバース オブジェクトを取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-identifier"></a>-Identifier
 メタバース オブジェクトの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncrunprofileresult"></a>Get-ADSyncRunProfileResult

 ### <a name="synopsis"></a>概要
 クライアントからの入力を処理し、実行プロファイルの結果を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunProfileResult [-RunHistoryId <Guid>] [-ConnectorId <Guid>] [-RunProfileId <Guid>]
 [-RunNumber <Int32>] [-NumberRequested <Int32>] [-RunStepDetails] [-StepNumber <Int32>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 クライアントからの入力を処理し、実行プロファイルの結果を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncRunProfileResult -ConnectorId "00000000-0000-0000-0000-000000000000"
 ```

 指定されたコネクタのすべての同期の実行プロファイルの結果を取得します。

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-connectorid"></a>-ConnectorId
 コネクタ識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-numberrequested"></a>-NumberRequested
 返却の最大数。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 特定の実行の識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runnumber"></a>-RunNumber
 特定の実行の実行番号。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofileid"></a>-RunProfileId
 指定された実行の実行プロファイル識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runstepdetails"></a>-RunStepDetails
 実行ステップの詳細のパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 ステップ番号でフィルター処理します。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncrunstepresult"></a>Get-ADSyncRunStepResult

 ### <a name="synopsis"></a>概要
 AD Sync の実行ステップの結果を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunStepResult [-RunHistoryId <Guid>] [-StepHistoryId <Guid>] [-First] [-StepNumber <Int32>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 AD Sync の実行ステップの結果を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncRunStepResult -RunHistoryId "00000000-0000-0000-0000-000000000000"
 ```

 指定された実行の AD Sync の実行ステップの結果を取得します。

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-first"></a>-First
 最初のオブジェクトのみを取得するためのパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 特定の実行の ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stephistoryid"></a>-StepHistoryId
 特定の実行ステップの ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 ステップ番号。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object



## <a name="get-adsyncscheduler"></a>Get-ADSyncScheduler

 ### <a name="synopsis"></a>概要
 同期スケジューラの現在の同期サイクル設定を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncScheduler [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 同期スケジューラの現在の同期サイクル設定を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Get-ADSyncScheduler
 ```

 同期スケジューラの現在の同期サイクル設定を取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncschedulerconnectoroverride"></a>Get-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>概要
 指定されたコネクタの AD Sync スケジューラ オーバーライド値を取得します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncSchedulerConnectorOverride [-ConnectorIdentifier <Guid>] [-ConnectorName <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 指定されたコネクタの AD Sync スケジューラ オーバーライド値を取得します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1 
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride -ConnectorName "contoso.com"
 ```

 'contoso.com' コネクタの AD Sync スケジューラ オーバーライド値を取得します。

 #### <a name="example-2"></a>例 2
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride
 ```

 すべての AD Sync スケジューラ オーバーライド値を取得します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 コネクタ識別子。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタ名。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsynccsobjectpasswordhashsync"></a>Invoke-ADSyncCSObjectPasswordHashSync

 ### <a name="synopsis"></a>概要
 指定された AD コネクタ スペース オブジェクトのパスワード ハッシュを同期します。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="searchbydistinguishedname"></a>SearchByDistinguishedName
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-ConnectorName] <String> [-DistinguishedName] <String>
 [<CommonParameters>]
 ```

 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-Identifier] <Guid> [<CommonParameters>]
 ```

 #### <a name="csobject"></a>CSObject
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-CsObject] <CsObject> [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 指定された AD コネクタ スペース オブジェクトのパスワード ハッシュを同期します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Invoke-ADSyncCSObjectPasswordHashSync -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DN=contoso,DN=com"
 ```

 指定されたオブジェクトのパスワード ハッシュを同期します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタの名前。

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-csobject"></a>-CsObject
 コネクタ スペース オブジェクト。

 ```yaml
 Type: CsObject
 Parameter Sets: CSObject
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 コネクタ スペース オブジェクトの識別名。

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 コネクタ スペース オブジェクトの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile

 ### <a name="synopsis"></a>概要
 特定の実行プロファイルを呼び出します。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname"></a>ConnectorName
 ```
 Invoke-ADSyncRunProfile -ConnectorName <String> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Invoke-ADSyncRunProfile -ConnectorIdentifier <Guid> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 特定の実行プロファイルを呼び出します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Invoke-ADSyncRunProfile -ConnectorName "contoso.com" -RunProfileName Export
 ```

 'contoso.com' コネクタでエクスポートを呼び出します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 コネクタの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタの名前。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-resume"></a>-Resume
 以前に停止した、または途中で終了した RunProfile の再開を試みるパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofilename"></a>-RunProfileName
 選択したコネクタで呼び出す実行プロファイルの名前。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="remove-adsyncaadserviceaccount"></a>Remove-ADSyncAADServiceAccount

 ### <a name="synopsis"></a>概要
 AAD テナント内の既存の AAD サービス アカウント (指定された資格情報に関連付けられている) を削除します (複数可)。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="serviceaccount"></a>ServiceAccount
 ```
 Remove-ADSyncAADServiceAccount [-AADCredential] <PSCredential> [-Name] <String> [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 #### <a name="serviceprincipal"></a>ServicePrincipal
 ```
 Remove-ADSyncAADServiceAccount [-ServicePrincipal] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 AAD テナント内の既存の AAD サービス アカウント (指定された資格情報に関連付けられている) を削除します (複数可)。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Remove-ADSyncAADServiceAccount -AADCredential $aadcreds -Name contoso.com
 ```

 contoso.com 内の既存の AAD サービス アカウントをすべて削除します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 資格情報。

 ```yaml
 Type: PSCredential
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-name"></a>-Name
 アカウントの名前。

 ```yaml
 Type: String
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-serviceprincipal"></a>-ServicePrincipal
 アカウントのサービス プリンシパル。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: ServicePrincipal
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 #### <a name="systemstring"></a>System.String

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncautoupgrade"></a>Set-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>概要
 インストールの AutoUpgrade の状態を有効と無効のどちらかに変更します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncAutoUpgrade [-AutoUpgradeState] <AutoUpgradeConfigurationState> [[-SuspensionReason] <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 インストールの AutoUpgrade の状態を設定します。 このコマンドレットは、AutoUpgrade の状態を有効と無効のどちらかに変更する場合にのみ使用する必要があります。 システムのみが状態を一時停止に設定する必要があります。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Set-ADSyncAutoUpgrade -AutoUpgradeState Enabled
 ```

 AutoUpgrade の状態を有効に設定します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-autoupgradestate"></a>-AutoUpgradeState
 AutoUpgrade の状態。 許容値: Suspended、Enabled、Disabled。

 ```yaml
 Type: AutoUpgradeConfigurationState
 Parameter Sets: (All)
 Aliases:
 Accepted values: Suspended, Enabled, Disabled

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-suspensionreason"></a>-SuspensionReason
 一時停止の理由。 システムのみが AutoUpgrade の状態を一時停止に設定する必要があります。

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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncscheduler"></a>Set-ADSyncScheduler

 ### <a name="synopsis"></a>概要
 同期スケジューラの現在の同期サイクル設定を設定します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncScheduler [[-CustomizedSyncCycleInterval] <TimeSpan>] [[-SyncCycleEnabled] <Boolean>]
 [[-NextSyncCyclePolicyType] <SynchronizationPolicyType>] [[-PurgeRunHistoryInterval] <TimeSpan>]
 [[-MaintenanceEnabled] <Boolean>] [[-SchedulerSuspended] <Boolean>] [-Force] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 同期スケジューラの現在の同期サイクル設定を設定します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Set-ADSyncScheduler -SyncCycleEnabled $true
 ```

 SyncCycleEnabled の現在の同期サイクル設定を True に設定します。
 
 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-customizedsynccycleinterval"></a>-CustomizedSyncCycleInterval
 設定するカスタム同期間隔のタイムスパン値を指定してください。
最小限の許容値で動作させたい場合は、このパラメーターを null に設定してください。

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-force"></a>-Force
 強制的に値を設定するためのパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 6
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-maintenanceenabled"></a>-MaintenanceEnabled
 MaintenanceEnabled を設定するためのパラメーター。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 4
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-nextsynccyclepolicytype"></a>-NextSyncCyclePolicyType
 NextSyncCyclePolicyType を設定するためのパラメーター。 許容値: Unspecified、Delta、Initial。

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 PurgeRunHistoryInterval を設定するためのパラメーター。

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-schedulersuspended"></a>-SchedulerSuspended
 SchedulerSuspended を設定するためのパラメーター。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 5
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-synccycleenabled"></a>-SyncCycleEnabled
 SyncCycleEnabled を設定するためのパラメーター。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemnullable1systemtimespan-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.TimeSpan, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncschedulerconnectoroverride"></a>Set-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>概要
 同期スケジューラの現在の同期サイクル設定を設定します。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 #### <a name="connectorname"></a>ConnectorName
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorName <String> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 同期スケジューラの現在の同期サイクル設定を設定します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Set-ADSyncSchedulerConnectorOverride -Connectorname "contoso.com" -FullImportRequired $true
 -FullSyncRequired $false
 ```

 'contoso.com' コネクタの同期サイクル設定を、フル インポートを要求し、完全同期を要求しないように設定します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 コネクタ識別子。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタ名。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullimportrequired"></a>-FullImportRequired
 次のサイクルでフル インポートを要求するには、true に設定します。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullsyncrequired"></a>-FullSyncRequired
 次のサイクルで完全同期を要求するには、true に設定します。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemguid"></a>System.Guid

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="start-adsyncpurgerunhistory"></a>Start-ADSyncPurgeRunHistory

 ### <a name="synopsis"></a>概要
 指定された期間より古い実行履歴を消去するコマンドレット。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="online"></a>オンライン
 ```
 Start-ADSyncPurgeRunHistory [[-PurgeRunHistoryInterval]  <TimeSpan>] [<CommonParameters>]
 ```

 #### <a name="offline"></a>offline
 ```
 Start-ADSyncPurgeRunHistory [-Offline] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 指定された期間より古い実行履歴を消去するコマンドレット。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Start-ADSyncPurgeRunHistory -PurgeRunHistoryInterval (New-Timespan -Hours 5)
 ```

 5 時間以上経過したすべての実行履歴を消去します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-offline"></a>-Offline
 サービスがオフラインのときに、データベースからすべての実行履歴を削除します。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: offline
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 履歴を保存するサイクル間隔。

 ```yaml
 Type: TimeSpan
 Parameter Sets: online
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemtimespan"></a>System.TimeSpan

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="start-adsyncsynccycle"></a>Start-ADSyncSyncCycle

 ### <a name="synopsis"></a>概要
 同期サイクルをトリガーします。

 ### <a name="syntax"></a>SYNTAX

 ```
 Start-ADSyncSyncCycle [[-PolicyType] <SynchronizationPolicyType>] [[-InteractiveMode] <Boolean>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 同期サイクルをトリガーします。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Start-ADSyncSyncCycle -PolicyType Initial
 ```

 初期のポリシーの種類で同期サイクルをトリガーします。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-interactivemode"></a>-InteractiveMode
 対話型 (コマンド ライン) モードとスクリプト/コード モード (他のコードからの呼び出し) を区別します。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-policytype"></a>-PolicyType
 実行するポリシーの種類。 許容値: Unspecified、Delta、Initial。

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemboolean"></a>System.Boolean

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncrunprofile"></a>Stop-ADSyncRunProfile

 ### <a name="synopsis"></a>概要
 すべてまたは指定されたビジー状態のコネクタを検索して停止します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncRunProfile [[-ConnectorName] <String>] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 すべてまたは指定されたビジー状態のコネクタを検索して停止します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Stop-ADSyncRunProfile -ConnectorName "contoso.com"
 ```

 'contoso.com' で実行中の同期を停止します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタの名前。
指定されていない場合、すべてのビジー状態のコネクタが停止されます。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncsynccycle"></a>Stop-ADSyncSyncCycle

 ### <a name="synopsis"></a>概要
 現在実行中の同期サイクルを停止するようにサーバーに通知します。

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncSyncCycle [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 現在実行中の同期サイクルを停止するようにサーバーに通知します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Stop-ADSyncSyncCycle
 ```

 現在実行中の同期サイクルを停止するようにサーバーに通知します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="sync-adsynccsobject"></a>Sync-ADSyncCSObject

 ### <a name="synopsis"></a>概要
 コネクタ スペース オブジェクトで同期プレビューを実行します。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname_objectdn"></a>ConnectorName_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorName <String> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier_objectdn"></a>ConnectorIdentifier_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorIdentifier <Guid> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="objectidentifier"></a>ObjectId
 ```
 Sync-ADSyncCSObject -Identifier <Guid> [-Commit] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 コネクタ スペース オブジェクトで同期プレビューを実行します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Sync-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 指定されたオブジェクトの同期プレビューを返します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-commit"></a>-Commit
 コミットするためのパラメーター スイッチ。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 コネクタの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 コネクタの名前。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 コネクタ スペース オブジェクトの識別名。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN, ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 コネクタ スペース オブジェクトの識別子。

 ```yaml
 Type: Guid
 Parameter Sets: ObjectIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="none"></a>なし

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncazureserviceconnectivity"></a>Test-AdSyncAzureServiceConnectivity

 ### <a name="synopsis"></a>概要
 Azure AD への接続の問題を調査および特定します。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="byenvironment"></a>ByEnvironment
 ```
 Test-AdSyncAzureServiceConnectivity [-AzureEnvironment] <Identifier> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 #### <a name="bytenantname"></a>ByTenantName
 ```
 Test-AdSyncAzureServiceConnectivity [-Domain] <String> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 Azure AD への接続の問題を調査および特定します。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Test-AdSyncAzureServiceConnectivity -AzureEnvironment Worldwide -Service SecurityTokenService -CurrentUser
 ```

 接続の問題がない場合は "True" を返します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-azureenvironment"></a>-AzureEnvironment
 テストする Azure 環境。 許容値: Worldwide、China、UsGov、Germany、AzureUSGovernmentCloud、AzureUSGovernmentCloud2、AzureUSGovernmentCloud3、PreProduction、OneBox、Default。

 ```yaml
 Type: Identifier
 Parameter Sets: ByEnvironment
 Aliases:
 Accepted values: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-currentuser"></a>-CurrentUser
 コマンドレットを実行しているユーザー。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-domain"></a>-Domain
 接続をテストしているドメイン。

 ```yaml
 Type: String
 Parameter Sets: ByTenantName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-service"></a>-Service
 接続をテストしているサービス。

 ```yaml
 Type: AzureService
 Parameter Sets: (All)
 Aliases:
 Accepted values: SecurityTokenService, AdminWebService

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="microsoftonlinedeploymentclientframeworkmicrosoftonlineinstanceidentifier"></a>Microsoft.Online.Deployment.Client.Framework.MicrosoftOnlineInstance+Identifier

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1microsoftonlinedeploymentclientframeworkazureservice-microsoftonlinedeploymentclientframework-version1600-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.Online.Deployment.Client.Framework.AzureService, Microsoft.Online.Deployment.Client.Framework, Version=1.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncuserhaspermissions"></a>Test-AdSyncUserHasPermissions

 ### <a name="synopsis"></a>概要
 ADMA ユーザーに必要な権限があるかどうかを確認するコマンドレット。

 ### <a name="syntax"></a>SYNTAX

 ```
 Test-AdSyncUserHasPermissions [-ForestFqdn] <String> [-AdConnectorId] <Guid>
 [-AdConnectorCredential] <PSCredential> [-BaseDn] <String> [-PropertyType] <String> [-PropertyValue] <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>Description
 ADMA ユーザーに必要な権限があるかどうかを確認するコマンドレット。

 ### <a name="examples"></a>例

 #### <a name="example-1"></a>例 1
 ```powershell
 PS C:\> Test-AdSyncUserHasPermissions -ForestFqdn "contoso.com" -AdConnectorId "00000000-0000-0000-000000000000"
 -AdConnectorCredential $connectorAcctCreds -BaseDn "CN=fabrikam,CN=Users,DC=contoso,DC=com" -PropertyType "Allowed-Attributes" -PropertyValue "name"
 ```

 ADMA ユーザーがユーザー 'fabrikam' の 'name' プロパティにアクセスする権限を持っているかどうかを確認します。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-adconnectorcredential"></a>-AdConnectorCredential
 AD コネクタ アカウントの資格情報。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-adconnectorid"></a>-AdConnectorId
 AD コネクタ ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-basedn"></a>-BaseDn
 チェックするオブジェクトのベース DN。

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

 #### <a name="-forestfqdn"></a>-ForestFqdn
 フォレストの名前。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-propertytype"></a>-PropertyType
 確認したいアクセス許可の種類。 許容値: Allowed-Attributes、Allowed-Attributes-Effective、Allowed-Child-Classes、Allowed-Child-Classes-Effective。

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

 #### <a name="-propertyvalue"></a>-PropertyValue
 確認したい PropertyType 属性の値。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 5
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

 #### <a name="commonparameters"></a>共通パラメーター
 このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「[about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

 ### <a name="inputs"></a>入力

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>出力

 #### <a name="systemobject"></a>System.Object

## <a name="next-steps"></a>次の手順

- [ハイブリッド ID とは](./whatis-hybrid-identity.md)
- [Azure AD Connect と Connect Health とは](whatis-azure-ad-connect.md)