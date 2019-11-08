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
ms.openlocfilehash: b014f6015b3e13a603cf3893062bd0463eb110ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488198"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>クイック スタート:サーバー向け Azure Arc を使用してマシンを Azure に接続する - ポータル

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[サーバー向け Azure Arc の概要](overview.md)に関するページで、サポートされているクライアントと必要なネットワーク構成を確認します。

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Azure portal を使用してエージェント インストール スクリプトを生成する

1. [https://aka.ms/hybridmachineportal ][aka_hybridmachineportal] を起動する。
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

![オンボード成功](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>クリーンアップ

サーバー向け Azure Arc からマシンを切断するには、次の 2 つの手順を実行する必要があります。

1. [ポータル](https://aka.ms/hybridmachineportal)でマシンを選択し、省略記号 (`...`) をクリックして、 **[削除]** を選択します。
1. マシンからエージェントをアンインストールします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [接続されているマシンにポリシーを割り当てる](../../governance/policy/assign-policy-portal.md)