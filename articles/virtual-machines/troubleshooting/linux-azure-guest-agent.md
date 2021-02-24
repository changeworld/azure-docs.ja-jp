---
title: Azure Linux エージェントのトラブルシューティング
description: Azure Linux エージェントに関する問題を解決します。
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
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878699"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Azure Linux エージェントのトラブルシューティング

[Azure Linux エージェント](../extensions/agent-linux.md)を使用すると、仮想マシン (VM) が IP アドレス 168.63.129.16 でファブリック コントローラー (VM がホストされている基盤の物理サーバー) と通信できるようになります。

>[!NOTE]
>この IP アドレスは、通信を容易にする仮想パブリック IP アドレスです。ブロックしないでください。 詳しくは、「[IP アドレス 168.63.129.16 とは](../../virtual-network/what-is-ip-address-168-63-129-16.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

エージェントの状態とバージョンを見て、現在もサポートされていることを確認します。 「[Azure での仮想マシン エージェントの最小バージョンのサポート](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)」を参照のうえ、バージョン サポートをご確認ください。あるいは、状態とバージョンを見つける手順については、「[WALinuxAgent FAQ](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output)」を参照してください。

## <a name="troubleshoot-a-not-ready-status"></a>準備中状態のトラブルシューティング

1. Azure Linux エージェントが実行されていることを確認するには、そのサービス状態を見てください。 サービス名は **walinuxagent** や **waagent** のようになります。

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

   サービスが実行されている場合、再起動して問題を解決してください。 サービスが停止している場合、少し待ち、その後状態をもう一度ご確認ください。

1. 自動更新は必ず有効にしてください。 自動更新設定は **/etc/waagent.conf** で確認します。

   ```
   AutoUpdate.Enabled=y
   ```

   Azure Linux エージェントを更新する方法の詳細については、「[VM で Azure Linux エージェントを更新する方法](../extensions/update-linux-agent.md)」を参照してください。

1. VM がファブリック コントローラーに接続できることを確認します。 curl などのツールを使用して、VM がポート 80、443、32526 で 168.63.129.16 に接続できるかどうかをテストします。 VM が想定どおりに接続されない場合は、ポート 80、443、32526 を介した送信方向の通信が VM のローカル ファイアウォールで開いているかどうかを確認します。 この IP アドレスがブロックされると、VM エージェントが予期しない動作を示す可能性があります。

## <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

Azure Linux エージェントのトラブルシューティングに関するイベントは、 **/var/log/waagent.log** ファイルに記録されます。

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (ホスト IP) に接続できない

VM がホスト サーバーで WireServer IP に到達できないとき、 **/var/log/waagent.log** ファイルに次のエラーが表示されます。

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

この問題を解決するには、次の手順を実行します。

* SSH を使用して VM に接続し、curl から URL http://168.63.129.16/?comp=versions へのアクセスを試みます。
* ファイアウォール、プロキシ、または IP アドレス 168.63.129.16 へのアクセスをブロックするその他のソースが原因で起きるおそれがある問題を確認します。
* Linux IPTables ファイアウォールまたはサードパーティのファイアウォールによってポート 80、443、32526 へのアクセスがブロックされているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

Azure Linux エージェントに関する問題の解決を続ける場合、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。