---
title: クイック スタート - サーバー向け Azure Arc を使用してマシンを Azure に接続する - ポータル
description: このクイック スタートでは、サーバー向け Azure Arc を使用して、ポータルからマシンを Azure に接続する方法について説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: azure automation, DSC, powershell, 望ましい状態の構成, 更新管理, 変更追跡, インベントリ, Runbook, Python, グラフィカル, ハイブリッド, オンボード
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 26c79db956b2703bf037fc6f7790d4ee13874410
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834242"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>クイック スタート:サーバー向け Azure Arc を使用してマシンを Azure に接続する - ポータル

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[サーバー向け Azure Arc の概要](overview.md)に関するページで、サポートされているクライアントと必要なネットワーク構成を確認します。

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Azure portal を使用してエージェント インストール スクリプトを生成する

1. [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal) を起動する。
1. **[+ 追加]** をクリックする。
1. ウィザードに従って完了する。
1. 最後のページにコピー (またはダウンロード) できるスクリプトが生成される。

スクリプトは、接続するターゲット マシンで実行する必要があります。 エージェントをダウンロードしてインストールし、マシンを接続するまでが 1 つの操作として実行されます。

Azure 以外のサーバーでは、次のことを管理する必要があります。

1. サーバーへのログオン (SSH、RDP、または PowerShell リモート処理を使用)
1. シェルの開始 (Linux では bash、Windows では PowerShell で管理者として)
1. ポータルからスクリプトを貼り付けて、Azure に接続するためにサーバー上で実行すること。
1. 個々のサーバーをオンボードするための既定の認証が、Azure の "デバイス ログイン" を使用した "*対話型*" であること。 スクリプトを実行すると、次のようなメッセージが表示されます。

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   ブラウザーを開き、認証のためのコードを入力します。 オンボードしているサーバー上でブラウザーが実行されている必要はありません。別のコンピューター (ラップトップなど) でもかまいません。

1. 非対話形式で認証する場合は、[サービス プリンシパルの作成](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale)に関するページに記載の手順に従って、ポータルから生成されたスクリプトを変更します。

> [!NOTE]
> 初めてログオンするときにサーバーで Internet Explorer を使用している場合は、エラーが発生します。ブラウザーを再度開いて、もう一度実行してください。

## <a name="execute-the-script-on-target-nodes"></a>ターゲット ノードでスクリプトを実行する

各ノードにログインし、ポータルから生成したスクリプトを実行します。 スクリプトが正常に完了したら、Azure portal に移動して、サーバーが正常に接続されていることを確認してください。

![オンボードの成功](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>クリーンアップ

サーバー向け Azure Arc からマシンを切断するには、次の 2 つの手順を行う必要があります。

1. [ポータル](https://aka.ms/hybridmachineportal)でマシンを選択し、省略記号 (`...`) をクリックして、 **[削除]** を選択します。
1. マシンからエージェントをアンインストールします。

   Windows では、[アプリと機能] コントロール パネルを使用してエージェントをアンインストールすることができます。
  
  ![アプリと機能](./media/quickstart-onboard/apps-and-features.png)

   アンインストールのスクリプトを作成したい場合は、以下の例を使用できます。**PackageId** を取得し、`msiexec /X` を使用してエージェントをアンインストールするものです。

   レジストリ キー `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` から **PackageId** を見つけます。 その後、`msiexec` を使用してエージェントをアンインストールできます。

   実際にエージェントをアンインストールする例を次に示します。

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Linux では、次のコマンドを実行してエージェントをアンインストールします。

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [接続されているマシンにポリシーを割り当てる](../../governance/policy/assign-policy-portal.md)
