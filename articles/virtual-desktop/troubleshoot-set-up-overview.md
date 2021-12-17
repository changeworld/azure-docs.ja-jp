---
title: Azure Virtual Desktop のトラブルシューティングの概要 - Azure
description: Azure Virtual Desktop 環境の設定時の問題のトラブルシューティングの概要。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 435f26a7fba7939ffc7ac370c648f6aae0d7a67f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234039"
---
# <a name="troubleshooting-overview-feedback-and-support-for-azure-virtual-desktop"></a>Azure Virtual Desktop のトラブルシューティングの概要、フィードバック、サポート

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)を参照してください。

この記事では、Azure Virtual Desktop 環境の設定時に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="troubleshoot-deployment-and-connection-issues"></a>デプロイと接続の問題を解決する

[Azure Monitor for Windows Virtual Desktop](azure-monitor.md) は Azure Monitor ブックを基盤に作られたダッシュボードであり、Azure Virtual Desktop 環境の問題を自動的にすばやく特定し、解決できます。 Kusto クエリの使用を望む場合、代わりに組み込みの診断機能である [Log Analytics](diagnostics-log-analytics.md) の使用をお勧めします。

## <a name="report-issues"></a>レポートに関する問題

統合されている Azure Resource Manager を使用して Azure Virtual Desktop の問題を報告したり、機能を提案したりするには、[Azure Virtual Desktop の Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) にアクセスしてください。 Tech Community を使用して、ベスト プラクティスについて話し合ったり、新機能を提案したり投票したりすることができます。

投稿でヘルプを要求したり、新しい機能を提案したりするときは、できるだけ詳細にトピックを記述してください。 詳細情報は、他のユーザーが質問に回答したり、投票を提案している機能を理解したりするのに役立ちます。

## <a name="escalation-tracks"></a>エスカレーション トラック

まず初めに、[Azure の状態のページ](https://status.azure.com/status)と [Azure Service Health](https://azure.microsoft.com/features/service-health/) を確認して、Azure サービスが正常に実行されていることを確認してください。

リモート デスクトップ クライアントを使用して環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 環境が設定されたら、新しい[診断サービス]()を使用して、一般的なシナリオに関する問題を識別できます。

| **問題点**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、適切なサービスを選択します ([ネットワーク] カテゴリの下で)。 |
| Azure Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、サービスで **[Azure Virtual Desktop]** を選択します。 <br> <br> Azure Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[ホスト プールの作成](troubleshoot-set-up-issues.md)」の「Azure Resource Manager テンプレート エラー」セクションを参照してください。 |
| Azure portal からの Azure Virtual Desktop セッション ホスト環境の管理    | [Azure サポート リクエストを開きます](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> リモート デスクトップ サービスまたは Azure Virtual Desktop PowerShell を使用する際の管理上の問題については、「[Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、または [Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスで **[Azure Virtual Desktop]** を選択し、問題の種類で **[構成と管理]** を選んでから、問題のサブタイプとして **[Issues configuring environment using PowerShell]\(PowerShell を使用して環境を構成する際の問題\)** を選択してください。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Azure Virtual Desktop 構成の管理      | 「[Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、または [Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Azure Virtual Desktop]** を選んでから、該当する問題の種類を選択してください。|
| FSLogix プロファイル コンテナーのデプロイと管理 | 「[FSLogix 製品のトラブルシューティング ガイド](/fslogix/fslogix-trouble-shooting-ht/)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Azure Virtual Desktop]** を選択し、問題の種類として **[FSLogix]** を選んでから、該当する問題のサブタイプを選択してください。 |
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | 「[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Azure Virtual Desktop]** を選んでから、問題の種類として **[リモート デスクトップ クライアント]** を選択してください。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | 「[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)」の「[ユーザーが接続しても何も表示されない (フィードなし)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)」セクションに従ってトラブルシューティングを行います。 <br> <br> ユーザーがアプリ グループに割り当てられている場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Azure Virtual Desktop]** を選んでから、問題の種類として **[リモート デスクトップ クライアント]** を選択します。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | 「[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)」を参照してください。それでも問題が解決されない場合は、[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)に関するページ」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| サードパーティの認証方法またはツールに関する問題 | お使いのサードパーティのプロバイダーが Azure Virtual Desktop のシナリオをサポートしていることを確認し、既知の問題があれば、そちらにお問い合わせください。 |
| Azure Virtual Desktop で Log Analytics を使用する場合の問題 | 診断スキーマに関する問題については、[Azure サポート リクエストをオープン](https://azure.microsoft.com/support/create-ticket/)してください。<br><br>Log Analytics のクエリ、視覚化、またはその他の問題については、Log Analytics の下で適切な問題の種類を選択してください。 |
| Microsoft 365 アプリを使用する場合の問題 | [Microsoft 365 管理センターのヘルプ オプション](/microsoft-365/admin/contact-support-for-business-products/)のいずれかを使用して、Microsoft 365 管理センターにお問い合わせください。 |

## <a name="next-steps"></a>次のステップ

- Azure Virtual Desktop 環境でホスト プールを作成しているときに発生した問題のトラブルシューティングを行うには、「[ホスト プールの作成](troubleshoot-set-up-issues.md)」を参照してください。
- Azure Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Azure Virtual Desktop エージェントまたはセッション接続に関連する問題のトラブルシューティングについては、「[Azure Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Azure Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Azure Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- Azure Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Azure Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-monitor/essentials/activity-log.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、[デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。