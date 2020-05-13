---
title: Windows Virtual Desktop サービスの接続に関するトラブルシューティング - Azure
description: Windows Virtual Desktop テナント環境でクライアント接続を設定するときの問題を解決する方法です。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612641"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop サービスの接続

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Spring 2020 更新プログラムに適用されます。 Azure Resource Manager オブジェクトを含まない Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Virtual Desktop クライアントの接続に関する問題を解決するには、この記事を使います。

## <a name="provide-feedback"></a>フィードバックの提供

フィードバックを行い、製品チームや [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) のその他のアクティブなコミュニティメンバーと Windows Virtual Desktop Service について話し合うことができます。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>ユーザーが接続しても何も表示されない (フィードなし)

ユーザーは、リモート デスクトップ クライアントを開始して認証を行うことができますが、Web 検出フィードでアイコンが何も表示されません。

次のコマンド ラインを使用し、問題を報告しているユーザーがアプリケーション グループに割り当てられていることを確認します。

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

ユーザーが正しい資格情報でサインインしていることを確認します。

Web クライアントを使っている場合は、キャッシュされた資格情報の問題がないことを確認します。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 (Enterprise マルチセッション) 仮想マシンが応答しない

仮想マシンが応答しておらず、RDP を使用してアクセスできない場合は、ホストの状態を確認し、診断機能でトラブルシューティングを行う必要があります。

ホストの状態を確認するには、次のコマンドレットを実行します：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

ホストの状態が `NoHeartBeat` の場合、VM が応答しておらず、エージェントが Windows Virtual Desktop サービスと通信できないことを意味します。

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

NoHeartBeat ビートの状態を修正するには、いくつかの操作を実行できます。

### <a name="update-fslogix"></a>FSLogix の更新

FSLogix が最新ではない場合 (特に、バージョン 2.9.7205.27375 が frxdrvvt.sys の場合)、デッドロックが発生する可能性があります。 [FSLogix を最新バージョンに更新](https://go.microsoft.com/fwlink/?linkid=2084562) しているか確認してください。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境を作成しているときや、Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題を解決するには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
