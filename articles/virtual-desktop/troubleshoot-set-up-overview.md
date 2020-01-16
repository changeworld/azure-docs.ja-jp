---
title: Windows Virtual Desktop トラブルシューティングの概要 - Azure
description: Windows Virtual Desktop テナント環境の設定時の問題の解決方法の概要
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: ce1af278d67f96873ed611db8dc05938205638dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980241"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>トラブルシューティングの概要、フィードバック、サポート

この記事では、Windows Virtual Desktop テナント環境の設定中に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="escalation-tracks"></a>エスカレーション トラック

リモート デスクトップ クライアントを使用してテナント環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 テナントが設定されたら、新しい[診断サービス](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)を使用して、一般的なシナリオに関する問題を識別できます。

>[!NOTE]
> 弊社の Tech Community フォーラムにアクセスすると、製品チームやアクティブなコミュニティ メンバーと問題について話し合うことができます。 [Windows Virtual Desktop の Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスしてください

| **問題点**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| テナントの作成                                                    | Azure が停止した場合は、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせいただくか、**Windows Virtual Desktop (Compute)** のサポート リクエストを開きます。|
| Azure portal で Marketplace テンプレートにアクセスする       | Azure が停止した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 <br> <br> Azure Marketplace Windows Virtual Desktop テンプレートを無料で入手できます。|
| GitHub から Azure Resource Manager テンプレートにアクセスする                                  | 「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop セッション ホスト VM の作成」セクションをご覧ください。 問題が解決されない場合、[GitHub サポート チーム](https://github.com/contact)にお問い合わせください。 <br> <br> GitHub でテンプレートにアクセスした後にエラーが発生した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | **Azure サポート (ネットワーク)** にお問い合わせください。 |
| Windows Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | **Azure サポート (コンピューティング)** にお問い合わせください。 <br> <br> Windows Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop テナントの作成」セクションを参照してください。 |
| Azure portal から Windows Virtual Desktop セッション ホスト環境を管理する    | **Azure サポート**にお問い合わせください。 <br> <br> リモート デスクトップ サービス/Windows Virtual Desktop PowerShell 使用時の管理問題については、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Windows Virtual Desktop 構成を管理する      | 「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 <br> <br> 問題がサンプルの GUI (グラフィカル ユーザー インターフェイス) に関連する場合、Yammer コミュニティにアクセスしてください。|
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | [リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md)に関するページを参照してください。それでも問題が解決されない場合は、**Windows Virtual Desktop (Compute) のサポート リクエストを開いてください**。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | [リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md)に関するページの「ユーザーが接続しても何も表示されない (フィードなし)」セクションを使用してトラブルシューティングしてください。 <br> <br> ユーザーがアプリ グループに割り当てられている場合は、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | [Windows Virtual Desktop サービス接続](troubleshoot-service-connection.md)に関するページを参照してください。それでも問題が解決されない場合は、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| GitHub で Windows Virtual Desktop ツール (Azure Resource Manager テンプレート、診断ツール、管理ツール) を使用するときに発生する問題 | 問題の報告方法については、[リモート デスクトップ サービス向け Azure Resource Manager テンプレート](https://github.com/Azure/RDS-Templates/blob/master/README.md)に関するページを参照してください。 |

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービス接続](troubleshoot-service-connection.md) に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、[デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
