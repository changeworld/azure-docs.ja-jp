---
title: Azure Stack Development Kit のデプロイ | Microsoft Docs
description: このチュートリアルでは、インストーラー スクリプトを使用して ASDK をインストールします。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: de31ed75b79ddb3832e32fad141ea7aef806d4d3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-using-the-installer"></a>チュートリアル: インストーラーを使用して ASDK をデプロイする
このチュートリアルでは、非運用環境で Azure Stack Development Kit (ASDK) をデプロイします。 

ASDK は、Azure Stack の機能やサービスを評価したり実演したりするためにデプロイできるテスト/開発環境です。 ASDK を使い始めるには、ホスト コンピューター ハードウェアを準備してから、いくつかのスクリプトを実行する必要があります (インストールには数時間かかります)。 その後、管理者ポータルとユーザー ポータルにサインインし、Azure Stack の使用を開始することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * デプロイ パッケージをダウンロードし、展開する
> * 開発キットのホスト コンピューターを準備する 
> * ホスト コンピューターに ASDK をインストールする
> * デプロイ後の構成を実行する
> * Azure に登録する

## <a name="prerequisites"></a>前提条件 
ASDK をインストールする前に、開発キットをホストするコンピューター (開発キット ホスト) を準備する必要があります。 開発キット ホスト コンピューターは、ハードウェア、ソフトウェア、およびネットワークの最小要件を満たしている必要があります。 

デプロイの ID ソリューションとして、Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) のどちらを使用するかも選択する必要があります。 

開発キット ホストが最小限のハードウェア要件を満たしていることを確認し、インストール プロセスをスムーズに実行できるように、デプロイを開始する前に ID ソリューションを決定してください。 

**[ASDK デプロイ計画に関する考慮事項を確認する](asdk-deploy-considerations.md)**

> [!TIP]
> 開発キット ホスト コンピューターにオペレーティング システムをインストールした後、ハードウェアがすべての要件を満たしていることを確認するには、[Azure Stack デプロイ要件チェック ツール](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)を使用できます。

## <a name="download-and-extract-the-deployment-package"></a>デプロイ パッケージをダウンロードし、展開する
開発キット ホスト コンピューターが ASDK の基本的なインストール要件を満たしていることを確認したら、次の手順は ASDK デプロイ パッケージをダウンロードして展開することです。 デプロイ パッケージには、Cloudbuilder.vhdx ファイルが含まれています。このファイルは、起動可能なオペレーティング システムと Azure Stack インストール ファイルを含む仮想ハード ドライブです。

開発キット ホストや別のコンピューターに、デプロイ パッケージをダウンロードできます。 展開されたデプロイ ファイルは 60 GB の空きディスク容量を占めます。そのため、別のコンピューターを利用することで、開発キット ホストのハードウェア要件を緩和できます。

**[Azure Stack Development Kit (ASDK) をダウンロードして展開する](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>開発キットのホスト コンピューターを準備する
ASDK をホスト コンピューターにインストールする前に、環境を準備し、VHD から起動するようにシステムを構成する必要があります。 開発キット ホスト コンピューターは、準備ができると、ASDK のデプロイを開始できるように、CloudBuilder.vhdx 仮想マシン ハード ドライブから起動します。

**[ASDK ホスト コンピューターを準備する](asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>ホスト コンピューターに ASDK をインストールする
開発キット ホスト コンピューターの準備ができたら、ASDK を CloudBuilder.vhdx イメージにデプロイできます。 ASDK は、asdk-installer.ps1 PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使用するか、完全に[コマンド ライン](asdk-deploy-powershell.md)から、デプロイすることができます。 

> [!NOTE]
> 必要に応じて、ホスト コンピューターが CloudBuilder.vhdx で起動された後、ASDK をインストールする "*前*" に、[Azure Stack のテレメトリ設定](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)を構成することができます。


**[Azure Stack Development Kit (ASDK) をインストールする](asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>デプロイ後の構成を実行する
ASDK をインストールした後、推奨されるインストール後の確認と構成変更がいくつかあります。 test-AzureStack コマンドレットを使用してインストールが正常に行われたことを検証し、Azure Stack PowerShell および GitHub ツールをインストールすることができます。 

Azure AD を使用するデプロイの後は、Azure Stack の管理者とテナント ポータルの両方をアクティブにする必要があります。 アクティブにすることによって、ディレクトリの全ユーザーに関して適切なアクセス許可 (同意のページに一覧が表示されます) を Azure Stack ポータルと Azure Resource Manager に付与することに同意したことになります。

評価期間が終了する前に開発キット ホストのパスワードが期限切れにならないように、パスワードの有効期限のポリシーをリセットする必要もあります。

> [!NOTE]
> 必要に応じて、ASDK のインストール "*後*" に [Azure Stack のテレメトリ設定](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)を構成することもできます。

**[ASDK デプロイ後のタスク](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure に登録する
Azure Stack に [Azure マーケットプレース項目をダウンロード](asdk-marketplace-item.md)できるように、Azure Stack を Azure に登録する必要があります。

**[Azure Stack を Azure に登録する](asdk-register.md)**

## <a name="next-steps"></a>次の手順
お疲れさまでした。 これらの手順を完了すると、[管理者](https://adminportal.local.azurestack.external)ポータルと[ユーザー](https://portal.local.azurestack.external) ポータルの両方を開発キット環境で利用できるようになります。 

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * デプロイ パッケージをダウンロードし、展開する
> * 開発キットのホスト コンピューターを準備する 
> * ホスト コンピューターに ASDK をインストールする
> * デプロイ後の構成を実行する
> * Azure に登録する

次のチュートリアルに進み、Azure Stack マーケットプレース項目を追加する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack マーケットプレース項目を追加する](asdk-marketplace-item.md)




