---
title: "クラシックから Azure Resource Manager への移行中に発生する一般的なエラー | Microsoft Docs"
description: "この記事では、Azure サービス管理から Azure Resource Manager スタックへの IaaS リソースの移行中に発生する一般的なエラーと対応策を一覧で示しています。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5bc03a1b-eb1c-438c-83d9-f0e9d61f1b6a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 345db9b2e45937ea45329acf780601e4e0fbd504


---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行中に発生する一般的なエラー
この記事では、Azure クラシック デプロイメント モデルから Azure Resource Manager スタックへの IaaS リソースの移行中に発生する一般的なエラーと軽減策を一覧で示しています。

## <a name="list-of-errors"></a>エラー一覧
| エラー文字列 | 対応策 |
| --- | --- |
| 内部サーバー エラー |場合によっては、これは再試行によって解消される一時的なエラーです。 エラーが解消されない場合は、プラットフォームのログを調べる必要があるため、[Azure サポートにご連絡](../azure-supportability/how-to-create-azure-support-request.md)ください。 <br><br> **注:** サポート チームによるインシデントの追跡が開始されたら、ご自分で対応しようとしないでください。環境で予期しない結果が発生する可能性があります。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} は PaaS 配置 (Web/Worker) であるため、移行がサポートされません。 |これは、デプロイに Web/worker ロールが含まれている場合に発生します。 Virtual Machines の移行のみがサポートされているため、デプロイから Web/worker ロールを削除し、移行をやり直してください。 |
| テンプレート {template-name} をデプロイできませんでした。 関連付け ID = {guid} |移行サービスのバックエンドでは、Azure Resource Manager テンプレートを使用して Azure Resource Manager スタック内にリソースを作成します。 テンプレートはべき等であるため、通常は移行操作を繰り返すことでこのエラーを解決しても問題はありません。 エラーが解消されない場合は、[Azure サポートに連絡](../azure-supportability/how-to-create-azure-support-request.md)して関連付け ID をお知らせください。 <br><br> **注:** サポート チームによるインシデントの追跡が開始されたら、ご自分で対応しようとしないでください。環境で予期しない結果が発生する可能性があります。 |
| 仮想ネットワーク {virtual-network-name} は存在していません。 |これは、新しい Azure Portal で Virtual Network を作成した場合に発生する可能性があります。 実際の Virtual Network 名は、"グループ * <VNET name>" のパターンに従います。 |
| HostedService {hosted-service-name} 内の VM {vm-name} に、Azure Resource Manager でサポートされない拡張機能 {extension-name} が含まれています。 移行を続行する前に、この拡張機能を VM からアンインストールすることをお勧めします。 |Azure Resource Manager では、BGInfo 1.* などの XML 拡張機能はサポートされていません。 そのため、これらの拡張機能を移行することはできません。 これらの拡張機能が仮想マシンにインストールされている場合は、移行を完了する前に自動的にアンインストールされます。 |
| HostedService {hosted-service-name} の VM {vm-name} には、拡張機能 VMSnapshot/VMSnapshotLinux が含まれています。この拡張機能は、現時点では移行がサポートされていません。 この機能を一度 VM からアンインストールし、移行が完了したら Azure Resource Manager を使って再インストールしてください。 |これは、仮想マシンが Azure Backup 用に構成されるシナリオです。 これは、現時点ではサポートされていないシナリオであるため、https://aka.ms/vmbackupmigration の回避策に従ってください。 |
| HostedService {hosted-service-name} の VM {vm-name} には、拡張機能 {extension-name} が含まれています。この拡張機能のステータスについて VM から報告がないため、 この VM を移行できません。 この拡張機能のステータスが報告されるようにするか、VM からこの拡張機能をアンインストールしたうえで、移行をやり直してください。 <br><br> HostedService {hosted-service-name} の VM {vm-name} には、拡張機能 {extension-name} が含まれています。この拡張機能のハンドラー ステータスとして {handler-status} が報告されているため、 この VM を移行できません。 この拡張機能のハンドラー ステータスとして {handler-status} が報告されるようにするか、VM からこの拡張機能をアンインストールしたうえで、移行をやり直してください。 <br><br> HostedService {hosted-service-name} 内の VM {vm-name} 用の VM エージェントがエージェントの全般ステータスとして [準備未完了] を報告しています。 このため、VM に移行拡張機能があっても、VM を移行できません。 VM エージェントがエージェントの全般ステータスとして [準備完了] を報告していることを確認してください。 また、https://aka.ms/classiciaasmigrationfaqs をご覧ください。 |Azure ゲスト エージェントと VM 拡張機能では、各自の状態を設定するために VM ストレージ アカウントへの発信インターネット アクセスが必要です。 状態エラーの一般的な原因には、以下が考えられます。 <li> ネットワーク セキュリティ グループがインターネットへの発信アクセスがブロックしている <li> VNET にオンプレミスの DNS サーバーがあるときに DNS 接続が失われている <br><br> サポートされていない状態が解決しない場合は、拡張機能をアンインストールしてこのチェックをスキップし、移行を進めることができます。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} には複数の可用性セットがあるため、移行がサポートされません。 |現時点では、1 つ以下の可用性セットを持つホストされるサービスのみを移行できます。 この問題を回避するには、余分な可用性セットと可用性セット内の Virtual Machines を別のホストされるサービスに移動してください。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} に含まれている VM が可用性セットの一部ではないため、HostedService に可用性セットが含まれているとしても、この配置の移行はサポートされません。 |このシナリオの回避策は、すべての仮想マシンを 1 つの可用性セット内に移動するか、ホストされるサービスの可用性セットからすべての仮想マシンを削除することです。 |
| ストレージ アカウント/HostedService/Virtual Network {virtual-network-name} は移行中のため、変更できません。 |このエラーは、リソースの "準備" 移行処理が完了し、リソースに変更を加える可能性がある操作がトリガーされた場合に発生します。 "準備" 操作の後、管理プレーンはロックされるため、リソースの変更はすべてブロックされます。 管理プレーンのロックを解除するには、"コミット" 移行操作を実行して移行を完了するか、 "中止" 移行操作で "準備" 操作をロールバックします。 |
| HostedService {hosted-service-name} は状態が RoleStateUnknown の VM {vm-name} が存在するため、移行が許可されていません。 VM が [実行中]、[停止済み]、[停止済み (割り当て解除)] のいずれかの状態の場合にのみ、移行が許可されます。 |VM が状態遷移中である可能性があります。通常、この状態は、再起動や拡張機能のインストールなどの HostedService の更新操作を実行しているときに発生します。移行を試行する前に、HostedService の更新操作が完了するまで待つことをお勧めします。 |

## <a name="next-steps"></a>次のステップ
移行プロセスを説明する記事の一覧を次に示します。

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-linux-cli-migration-classic-resource-manager.md)




<!--HONumber=Nov16_HO3-->


