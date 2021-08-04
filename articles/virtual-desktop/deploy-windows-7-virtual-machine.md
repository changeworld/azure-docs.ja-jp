---
title: Azure Virtual Desktop で Windows 7 仮想マシンをデプロイする - Azure
description: Azure Virtual Desktop で Windows 7 仮想マシンを構成してデプロイする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4e52594578202046d36e2cbd5a727d4973f26f39
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751974"
---
# <a name="deploy-a-windows-7-virtual-machine-on-azure-virtual-desktop"></a>Azure Virtual Desktop で Windows 7 仮想マシンをデプロイする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)を参照してください。

Azure Virtual Desktop で Windows 7 仮想マシン (VM) をデプロイするプロセスは、新しいバージョンの Windows を実行している VM の場合とは若干異なります。 このガイドでは、Windows 7 をデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、「[Create a host pool with PowerShell](create-host-pools-powershell.md)」 (PowerShell を使用してホスト プールを作成する) の手順に従って、ホスト プールを作成します。 ポータルを使用している場合、「[Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」の手順 1 から 9 までの指示に従ってください。 その後、 **[確認と作成]** を選択し、空のホスト プールを作成します。

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 仮想マシンを構成する

前提条件を満たしたら、Azure Virtual Desktop でデプロイするために Windows 7 VM を構成することができます。

Azure Virtual Desktop で Windows 7 VM を設定するには、次のようにします。

1. Azure portal にサインインし、Windows 7 Enterprise イメージを検索するか、独自のカスタマイズした Windows 7 Enterprise (x64) イメージをアップロードします。
2. Windows 7 Enterprise をホスト オペレーティング システムとして使用する 1 つまたは複数の仮想マシンをデプロイします。 仮想マシンでリモート デスクトップ プロトコル (RDP) (TCP/3389 ポート) が許可されていることを確認します。
3. RDP を使用して Windows 7 Enterprise ホストに接続し、デプロイの構成時に定義した資格情報で認証します。
4. RDP でのホストへの接続時に使用したアカウントを、"リモート デスクトップ ユーザー" グループに追加します。 アカウントを追加しないと、VM を Active Directory ドメインに参加させた後に接続できなくなる可能性があります。
5. VM の Windows Update に移動します。
6. 重要なカテゴリのすべての Windows Update をインストールします。
7. 省略可能なカテゴリのすべての Windows Update (言語パックを除く) をインストールします。 このプロセスにより、これらの手順を完了するために必要なリモート デスクトップ プロトコル 8.0 更新プログラム ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) がインストールされます。
8. ローカル グループ ポリシー エディターを開き、 **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[リモート セッション環境]** の順に移動します。
9. リモート デスクトップ プロトコル 8.0 ポリシーを有効にします。
10. Active Directory ドメインにこの VM を参加させます。
11. 次のコマンドを実行して、仮想マシンを再起動します。

     ```cmd
     shutdown /r /t 0
     ```

12. [こちら](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo)の指示に従って、登録トークンを取得します。

      - 代わりに Azure portal を使用する場合、VM を追加するホスト プールの [概要] ページに進み、そこでトークンを作成することもできます。

13. [Windows 7 用 Azure Virtual Desktop エージェントをダウンロードします](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [Windows 7 用 Azure Virtual Desktop エージェント マネージャーをダウンロードします](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. Azure Virtual Desktop エージェント インストーラーを開き、指示に従います。 メッセージが表示されたら、手順 12 で作成した登録キーを指定します。
16. Azure Virtual Desktop エージェント マネージャーを開き、指示に従います。
17. 必要に応じて、TCP/3389 ポートをブロックし、リモート デスクトップ プロトコルで VM に直接アクセスできないようにします。
18. 必要に応じて、.NET Framework がバージョン 4.7.2 以降であることを確認します。 フレームワークの更新は、カスタム イメージを作成する場合に特に重要です。

## <a name="next-steps"></a>次のステップ

これで、Azure Virtual Desktop のデプロイが使用できる状態になりました。 開始するには、[最新バージョンの Azure Virtual Desktop クライアントをダウンロードします](https://aka.ms/wvd/clients/windows)。

Azure Virtual Desktop での Windows 7 に関する既知の問題のリストとトラブルシューティングの手順については、[Azure Virtual Desktop での Windows 7 仮想マシンのトラブルシューティング](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)に関する記事を参照してください。
