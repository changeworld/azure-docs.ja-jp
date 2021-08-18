---
title: コマンドレット New-AzRecoveryServicesAsrVMNicConfig の IPConfig パラメーターの非推奨 | Microsoft Docs
description: コマンド New-AzRecoveryServicesAsrVMNicConfig の IPConfig パラメーターの非推奨に関する詳細と、新しいコマンドレット New-AzRecoveryServicesAsrVMNicIPConfig の使用に関する情報
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2021
ms.author: rishjai
ms.openlocfilehash: 138dd9d576638cda52ca62e45cdb353920e9b00b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968543"
---
# <a name="deprecation-of-ip-config-parameters-for-the-cmdlet-new-azrecoveryservicesasrvmnicconfig"></a>コマンドレット New-AzRecoveryServicesAsrVMNicConfig の IP Config パラメーターの非推奨

この記事では、非推奨、付随する影響、および次のシナリオに対してお客様が利用できる代替オプションについて説明します。

フェールオーバーまたはテスト フェールオーバーのプライマリ IP 構成設定を構成します。 

このコマンドレットは、バージョン "_Az PowerShell 5.9.0 以上_" でコマンドレット New-AzRecoveryServicesAsrVMNicConfig を使用している、Azure から Azure への DR シナリオのすべてのお客様に影響します。

> [!IMPORTANT]
> お客様は、環境の中断を回避するために、できるだけ早く修復手順を実行することをお勧めします。 

## <a name="what-changes-should-you-expect"></a>予期される変更

New-AzRecoveryServicesAsrVMNicConfig では、次のパラメーターを使用して、FO/TFO の IP 構成値を構成します。
- RecoveryVMSubnetName
- RecoveryNicStaticIPAddress
- RecoveryPublicIPAddressId
- RecoveryLBBackendAddressPoolId
- TfoVMSubnetName
- TfoNicStaticIPAddress
- TfoPublicIPAddressId
- TfoLBBackendAddressPoolId

これらのパラメーターは、コマンドレットで指定できなくなります。

- 2021 年 5 月 4 日以降、コマンドレット New-AzRecoveryServicesAsrVMNicConfig の IP Config パラメーターの非推奨に伴い、Azure portal 通知とメールでの連絡が届きます。

- これを使用している既存のスクリプトがある場合、今後サポートされなくなります。
 
## <a name="alternatives"></a>代替 

代わりとして、IP 構成の FO/TFO 設定を構成するための新しいコマンドレット [New-AzRecoveryServicesAsrVMNicIPConfig](/powershell/module/az.recoveryservices/new-azrecoveryservicesasrvmnicipconfig) が導入されています。 


## <a name="remediation-steps"></a>修復ステップ

これらのパラメーターを削除するには、スクリプトを変更する必要があります。 代わりに、IP 構成オブジェクトを作成する際は新しいコマンドレットの **AzRecoveryServicesAsrVMNicIPConfig** を使用してください。 以下に図を示します。

**既存のスクリプト** が、次のように記述されているとします。
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -RecoveryVMSubnetName "default" -TfoVMSubnetName "default" -RecoveryNicStaticIPAddress "10.1.40.223" -TfoNicStaticIPAddress "10.33.0.223"

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

スクリプトを **以下のように** アップグレードします。
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# Create the config object for Primary IP Config
$ipConfig = New-AzRecoveryServicesAsrVMNicIPConfig  -IpConfigName <ipConfigName> -RecoverySubnetName "default" -TfoSubnetName "default" -RecoveryStaticIPAddress "10.1.40.223" -TfoStaticIPAddress "10.33.0.223"

$ipConfigs = @($ipConfig)

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -IPConfig $ipConfigs

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

## <a name="next-steps"></a>次のステップ
前述のように、スクリプトを変更します。 これに関してご質問がある場合は、Microsoft サポートまでお問い合わせください