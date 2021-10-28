---
title: Azure IoT Edge for Linux on Windows 用の仮想スイッチを作成する | Microsoft Docs
description: Azure IoT Edge for Linux on Windows 用の仮想スイッチを作成するためのインストール
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e9b47d3cfdb648a63156164f2341b05b4eeec4a1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234776"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Azure IoT Edge for Linux on Windows の仮想スイッチの作成
Azure IoT Edge for Linux on Windows では、ホスト コンピューター上の仮想スイッチを使用して仮想マシンと通信します。 Windows のデスクトップ バージョンには、使用できる既定のスイッチが付属していますが、Windows Server にはありません。 IoT Edge for Linux on Windows を Windows Server デバイスにデプロイするには、まず仮想スイッチを作成する必要があります。 また、必要に応じて、このガイドを利用してカスタム仮想スイッチを作成することもできます。 

この記事では、次の手順に従って、Windows デバイスに仮想スイッチを作成し、IoT Edge for Linux on Windows をインストールする方法について説明します。
- 仮想スイッチを作成する
- NAT テーブルを作成する
- DHCP サーバーをインストールして設定する

## <a name="prerequisites"></a>前提条件
- Windows デバイス。 サポートされている Windows バージョンについては、「[オペレーティング システム](support.md#operating-systems)」を参照してください。
- Windows デバイスにインストールされている Hyper-V ロール。 Hyper-V を有効にする方法の詳細については、「[Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする](./how-to-provision-single-device-linux-on-windows-symmetric.md?tabs=powershell#prerequisites)」を参照してください。

## <a name="create-virtual-switch"></a>仮想スイッチを作成する 
このセクションの次の手順は、仮想スイッチの作成に関する一般的なガイドです。 仮想スイッチの構成がお使いのネットワーク環境に合っていることを確認してください。

1. 管理者特権セッションで PowerShell を開きます。

2. Windows ホストの仮想スイッチを確認し、使用できる仮想スイッチがないことを確認します。 詳細については [Get-VMSwitch (Hyper-V)](/powershell/module/hyper-v/get-vmswitch) に関する記事を参照してください。 

   ```powershell
   Get-VMSwitch
   ```

   **Default Switch** という名前の仮想スイッチが既に作成されていて、カスタム仮想スイッチが不要な場合は、このガイドの残りの手順は行わずに IoT Edge for Linux on Windows をインストールすることができます。

3. 名前と、種類として **内部** または **プライベート** を指定して、新しい VM スイッチを作成します。 **外部** 仮想スイッチを作成するには、**NetAdapterInterfaceDescription** と **NetAdapterName** のいずれかのパラメーターを指定します。それにより、仮想スイッチの種類が **外部** に暗黙的に設定されます。 詳細と追加の手順については、[New-VMSwitch (Hyper-V)](/powershell/module/hyper-v/new-vmswitch) に関するページと「[Hyper-V 仮想マシン用の仮想スイッチを作成する](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)」を参照してください。
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. 作成されたスイッチのインターフェイス インデックスを取得します。 詳細については、[Get-NetAdapter (NetAdapter)](/powershell/module/netadapter/get-netadapter) に関するページを参照してください。 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. 前の手順のインターフェイス インデックスを使用して、作成されたスイッチ ネットワーク アダプターの IP アドレス オクテットを取得します。 詳細については、[Get-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/get-netipaddress) に関するページを参照してください。 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. 前の手順の IP アドレス ファミリとインターフェイス インデックスを使用して、新しいゲートウェイ IP アドレスを作成して設定します。  たとえば、仮想ネットワーク スイッチ アダプターの IPv4 アドレスが xxx.xxx.xxx.yyy の場合、gatewayIp を xxx.xxx.xxx.1 のように設定できます。 詳細については、[New-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/new-netipaddress) に関するページを参照してください。
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. 内部ネットワークアドレスを外部ネットワークに変換するネットワーク アドレス変換 (NAT) オブジェクトを作成します。 前の手順と同じ IPv4 ファミリのアドレスを使用します。 たとえば、仮想ネットワーク スイッチ アダプターの IPv4 アドレスが xxx.xxx.xxx.yyy の場合、natIp を xxx.xxx.xxx.0 のように設定できます。 詳細については、[New-NetNat (NetNat)](/powershell/module/netnat/new-netnat) に関するページを確認してください。 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>DHCP サーバーを作成する 

>[!WARNING]
>DHCP サーバーを企業ネットワーク環境にデプロイするには、承認が必要になる場合があります。 仮想スイッチの構成が企業ネットワークのポリシーに準拠しているかどうかを確認してください。 詳細については、「[Windows PowerShell を使用して DHCP をデプロイする](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)」というガイドを参照してください。 

1. DHCP サーバー機能がデバイスにインストールされているかどうかを確認します。 **Install State** 列を探します。
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. インストールされていない場合は、次のコマンドを使用してインストールします。
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. DHCP サーバーを既定のローカル セキュリティ グループに追加し、サーバーを再起動します。
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. DHCP サーバーのスコープを構成します。 詳細については、[Add-DhcpServerv4Scope (DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope) に関するページを参照してください。  DHCP サーバーの IP の範囲は、**startIp** と **endIp** によって決まります。 たとえば、100 のアドレスを使用できるようにする場合は、手順 5 の仮想ネットワーク スイッチ アダプターの xxx.xxx.xxx.yyy IPv4 アドレスに従って、startIp = xxx.xxx.xxx.100、endIp = xxx.xxx.xxx.200、subnetMask = 255.255.255.0 となります。
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. 最後に、NAT オブジェクトと gatewayIp を DHCP サーバーに割り当て、サーバーを再起動して構成を読み込みます。
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>次のステップ
「[Azure IoT Edge for Linux on a Window デバイスのインストールとプロビジョニング](how-to-provision-single-device-linux-on-windows-symmetric.md)」の手順に従って、IoT Edge for Linux on Windows でデバイスを設定します。
