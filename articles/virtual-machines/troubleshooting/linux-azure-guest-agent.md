---
title: Azure Linux ゲスト エージェントのトラブルシューティング
description: Azure Linux ゲスト エージェントが機能しない問題のトラブルシューティングを行います
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95547912"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Azure Linux ゲスト エージェントのトラブルシューティング

[Azure Linux ゲスト エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)は、仮想マシン (VM) エージェントです。 これを使用すると、VM が IP アドレス 168.63.129.16 でファブリック コントローラー (VM がホストされている基盤の物理サーバー) と通信できるようになります。 この IP アドレスは、通信を容易にする仮想パブリック IP アドレスです。 詳しくは、「[IP アドレス 168.63.129.16 とは](../../virtual-network/what-is-ip-address-168-63-129-16.md)」を参照してください。

## <a name="checking-agent-status-and-version"></a>エージェントの状態とバージョンの確認

「https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output」を参照してください。

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>準備未完了状態の VM エージェントのトラブルシューティング

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>手順 1. Azure Linux ゲスト エージェント サービスが実行されているかどうかを確認する

ディストリビューションによっては、サービス名を walinuxagent または waagent にすることができます。

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


サービスが表示され、実行されている場合は、サービスを再起動して問題が解決されているかどうかを確認します。 サービスが停止している場合は、サービスを開始して数分待ちます。 その後、 **[エージェントの状態]** が **[準備完了]** として報告されるかどうかを確認します。 これらの問題のさらなるトラブルシューティングを行う場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。

### <a name="step-2-check-whether-auto-update-is-enabled"></a>手順 2 自動更新が有効になっているかどうかを確認する

/etc/waagent.conf でこの設定を確認します。

```
AutoUpdate.Enabled=y
```

Azure Linux エージェントを更新する方法の詳細については、 https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent を参照してください 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>手順 3 VM がファブリック コントローラーに接続できるかどうかを確認する

curl などのツールを使用して、VM がポート 80、32526、443 で 168.63.129.16 に接続できるかどうかをテストします。 VM が想定どおりに接続されない場合は、ポート 80、443、32526 を介した送信方向の通信が VM のローカル ファイアウォールで開いているかどうかを確認します。 この IP アドレスがブロックされると、VM エージェントがさまざまなシナリオで予期しない動作を示す可能性があります。

## <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

Azure Linux ゲスト エージェントのトラブルシューティングに関するイベントは、次のログ ファイルに記録されます。

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (ホスト IP) に接続できない 

/var/log/waagent.log に、次のエラー エントリがあることがわかります。

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**分析**

VM がホスト サーバー上の WireServer IP に到達できません。

**ソリューション**

1. WireServer IP に到達できないため、SSH を使用して VM に接続し、curl から URL http://168.63.129.16/?comp=versions へのアクセスを試みます。 
1. ファイアウォール、プロキシ、または IP アドレス 168.63.129.16 へのアクセスをブロックするその他のソースが原因で起きる可能性がある問題を確認します。
1. Linux IPTables ファイアウォールまたはサードパーティのファイアウォールによってポート 80、443、32526 へのアクセスがブロックされているかどうかを確認します。 このアドレスをブロックしてはいけない詳しい理由については、「[IP アドレス 168.63.129.16 とは](../../virtual-network/what-is-ip-address-168-63-129-16.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

「Windows Azure ゲスト エージェントが機能しない」問題のさらなるトラブルシューティングを行うには、[Microsoft サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
