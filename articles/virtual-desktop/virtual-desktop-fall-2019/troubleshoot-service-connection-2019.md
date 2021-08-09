---
title: Azure Virtual Desktop (クラシック) サービスの接続に関するトラブルシューティング - Azure
description: Azure Virtual Desktop (クラシック) テナント環境でクライアント接続を設定するときの問題の解決方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 856d6e65273b15c843bffe40c818b07c60f00fb0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753937"
---
# <a name="azure-virtual-desktop-classic-service-connections"></a>Azure Virtual Desktop (クラシック) サービスの接続

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../troubleshoot-service-connection.md)を参照してください。

Azure Virtual Desktop クライアントの接続に関する問題を解決するには、この記事を使います。

## <a name="provide-feedback"></a>フィードバックの提供

フィードバックを行い、製品チームや [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) のその他のアクティブなコミュニティメンバーと Azure Virtual Desktop Service について話し合うことができます。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>ユーザーが接続しても何も表示されない (フィードなし)

ユーザーは、リモート デスクトップ クライアントを開始して認証を行うことができますが、Web 検出フィードでアイコンが何も表示されません。

次のコマンド ラインを使用し、問題を報告しているユーザーがアプリケーション グループに割り当てられていることを確認します。

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

ユーザーが正しい資格情報でログインしていることを確認します。

Web クライアントを使っている場合は、キャッシュされた資格情報の問題がないことを確認します。

## <a name="next-steps"></a>次のステップ

- Azure Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、[「トラブルシューティングの概要、フィードバック、サポート」](troubleshoot-set-up-overview-2019.md)を参照してください。
- Azure Virtual Desktop 環境でテナントとホスト プールを作成しているときに発生した問題のトラブルシューティングを行う場合は、[「テナントとホスト プールの作成」](troubleshoot-set-up-issues-2019.md)を参照してください。
- Azure Virtual Desktop で仮想マシン (VM) の構成中に発生した問題のトラブルシューティングを行う場合は、[「セッション ホスト仮想マシンの構成」](troubleshoot-vm-configuration-2019.md)を参照してください。
- Azure Virtual Desktop で PowerShell を使用しているときに発生した問題のトラブルシューティングを行う場合は、[「Azure Virtual Desktop PowerShell」](troubleshoot-powershell-2019.md)を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
