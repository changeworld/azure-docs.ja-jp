---
title: Azure Stack のサービスとしての検証のリリース ノート | Microsoft Docs
description: Azure Stack のサービスとしての検証のリリース ノート。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: eefd39c751bdbd9ed9c8f3b9112fee1ddbffb9a0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486939"
---
# <a name="release-notes-for-validation-as-a-service"></a>サービスとしての検証のリリース ノート

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

この記事には、Azure Stack のサービスとしての検証のリリース ノートが含まれています。

## <a name="version-405"></a>バージョン 4.0.5

2019 年 1 月 17 日

- Disk Identification Test (ディスク識別テスト) がストレージ プールの不整合を解決するために更新されました。 バージョン:5.1.14.0 -> 5.1.15.0
- Azure Stack 月間更新検証が、承認済みソフトウェアおよびコンテンツの検証の不整合を解決するために更新されました。 バージョン:5.1.14.0 -> 5.1.17.0
- OEM Extension Package Verification (OEM 拡張機能パッケージの検証) が、Azure Stack の更新手順の前に必要なチェックを実行するよう更新されました。 バージョン:5.1.14.0 -> 5.1.16.0
- 内部のバグ修正

## <a name="version-402"></a>バージョン 4.0.2

2019 年 1 月 7 日

Azure Stack 月間更新検証ワークフローを実行しているとき、ご利用の OEM 更新プログラム パッケージのバージョンが 1810 以降でない場合、OEM 更新手順に進んだときにエラーが表示されます。 これはバグです。 修正プログラムを開発中です。軽減策の手順は次のとおりです。

1. OEM の更新プログラムを通常どおり実行します。
2. パッケージが正常に適用されたら Test-AzureStack を実行し、出力を保存します。
3. テストを取り消します。
4. 保存した出力を VaaSHelp@microsoft.com に送信し、実行の合格結果を受け取ります。

## <a name="version-402"></a>バージョン 4.0.2

2018 年 11 月 30 日

- 内部のバグ修正

## <a name="version-401"></a>バージョン 4.0.1

2018 年 10 月 8 日

- VaaS の前提条件

    `Install-VaaSPrerequisites` に、クラウド管理者の資格情報は不要になりました。 このコマンドレットの最新バージョンを実行する場合、前提条件をインストールするための変更後のコマンドについては、「[Download and install the agent (エージェントのダウンロードとインストール)](azure-stack-vaas-local-agent.md#download-and-install-the-agent)」をご覧ください。 コマンドは次のとおりです。

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>バージョン 4.0.0

2018 年 8 月 29 日

- VaaS の前提条件と VHD の更新

    パッケージ検証時の問題に対処するために、`Install-VaaSPrerequisites` に、クラウド管理者の資格情報が必要になりました。 「[Download and install the agent (エージェントのダウンロードとインストール)](azure-stack-vaas-local-agent.md#download-and-install-the-agent)」のドキュメントが次のように更新されました。

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > スクリプトで必要とされる `$CloudAdminCreds` は、検証対象の Azure Stack インスタンス用です。 VaaS テナントが使用する Azure Active Directory の資格情報ではありません。

- ローカル エージェントの更新

    以前のバージョンのローカル エージェントは、現在の 4.0.0 リリースのサービスと互換性がありません。 すべてのユーザーが、ローカル エージェントを更新する必要があります。 最新のエージェントをインストールする手順については、「[Download and install the agent (エージェントのダウンロードとインストール)](azure-stack-vaas-local-agent.md#download-and-install-the-agent)」をご覧ください。

- PowerShell オートメーションの更新

    `LaunchVaaSTests` PowerShell スクリプトに、最新バージョンのスクリプト パッケージを必要とする変更が加えられました。 最新バージョンのスクリプト パッケージをインストールする手順については、「[テスト パス ワークフローの起動](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow)」をご覧ください。

- サービスとしての検証ポータル

  - パッケージの署名の通知

    パッケージ検証ワークフローの一環として OEM カスタマイズ パッケージが提出されると、パッケージ形式が検証され、公開されている仕様に従っているかどうかが確認されます。 パッケージが準拠していない場合、実行は失敗します。 テナントの登録済みの Azure Active Directory 連絡先の電子メール アドレスに電子メール通知が送信されます。

  - 対話型テスト カテゴリ

    **対話型**テスト カテゴリが追加されました。 これらのテストにより、自動化されていない対話型の Azure Stack シナリオが実行されます。

  - 対話型の機能検証

    テスト成功ワークフローで、特定の機能に焦点を合わせたフィードバックを提供する機能を使用できるようになりました。 `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` テストでは、特定の更新プログラムが正しく適用されたかどうかを確認し、フィードバックを収集します。

## <a name="next-steps"></a>次の手順

- [サービスとしての検証のトラブルシューティング](azure-stack-vaas-troubleshoot.md)
