---
title: Windows Virtual Desktop サービスの接続に関するトラブルシューティング - Azure
description: Windows Virtual Desktop テナント環境でクライアント接続を設定するときの問題を解決する方法です。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: 81ba2b3b5834e06922f694d6eb5338465bfb0ab7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975461"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop サービスの接続

Windows Virtual Desktop クライアントの接続に関する問題を解決するには、この記事を使います。

## <a name="provide-feedback"></a>フィードバックの提供

フィードバックを行い、製品チームや [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) のその他のアクティブなコミュニティメンバーと Windows Virtual Desktop Service について話し合うことができます。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>ユーザーが接続しても何も表示されない (フィードなし)

ユーザーは、リモート デスクトップ クライアントを開始して認証を行うことができますが、Web 検出フィードでアイコンが何も表示されません。

次のコマンド ラインを使用し、問題を報告しているユーザーがアプリケーション グループに割り当てられていることを確認します。

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

ユーザーが正しい資格情報でログインしていることを確認します。

Web クライアントを使っている場合は、キャッシュされた資格情報の問題がないことを確認します。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 (Enterprise マルチセッション) 仮想マシンが応答しない

仮想マシンが応答しておらず、RDP を使用してアクセスできない場合は、ホストの状態を確認し、診断機能でトラブルシューティングを行う必要があります。

ホストの状態を確認するには、次のコマンドレットを実行します：

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

ホストの状態が `NoHeartBeat` の場合、VM が応答しておらず、エージェントが Windows Virtual Desktop サービスと通信できないことを意味します。

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

NoHeartBeat ビートの状態を修正するには、いくつかの操作を実行できます。

### <a name="update-fslogix"></a>FSLogix の更新

FSLogix が最新ではない場合 (特に、バージョン 2.9.7205.27375 が frxdrvvt.sys の場合)、デッドロックが発生する可能性があります。 [FSLogix を最新バージョンに更新](https://go.microsoft.com/fwlink/?linkid=2084562) しているか確認してください。

### <a name="disable-bgtaskregistrationmaintenancetask"></a>BgTaskRegistrationMaintenanceTask を無効にする

FSLogix の更新が機能しない場合、この問題は、毎週のメンテナンスタスクで BiSrv コンポーネントがシステムリソースを消費していることによる可能性があります。 次の2つの方法のいずれかで、BgTaskRegistrationMaintenanceTask を無効にして、メンテナンスタスクを一時的に無効にします：

- [スタート] メニューにアクセスして、**タスク スケジューラ** を検索します。 **タスク スケジューラ ライブラリ** > **Microsoft** > **Windows** > **BrokerInfrastructure**に移動します。 **BgTaskRegistrationMaintenanceTask** という名前のタスクを探します。 見つかったら、右クリックして、ドロップダウン メニューから **[無効にする]** を選択します。
- 管理者としてコマンド ライン メニューを開き、次のコマンドを実行します：
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
