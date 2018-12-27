---
title: Azure Stack Development Kit の評価 | Microsoft Docs
description: 評価目的で Azure Stack 開発キットをデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: jeffgilb
ms.custom: mvc
ms.reviewer: misainat
ms.openlocfilehash: 44fed3311234e1a64cb46c3403f39a9e269d189b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956928"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>クイック スタート: Azure Stack Development Kit の評価

[Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) は、Azure Stack の機能やサービスを評価したり、実演したりするためにデプロイできるテスト/開発環境です。 ASDK を使い始めるには、ホスト コンピューター ハードウェアを準備してから、いくつかのスクリプトを実行する必要があります (インストールには数時間かかります)。 その後、管理者ポータルまたはユーザー ポータルにサインインし、Azure Stack の使用を開始することができます。

## <a name="prerequisites"></a>前提条件

### <a name="asdk-host-computer-requirements"></a>ASDK ホスト コンピューターの要件

ASDK をインストールする前に、開発キットをホストするコンピューターを準備する必要があります。 開発キットのホスト コンピューターは、**[ASDK 開発計画に関する考慮事項の確認](./asdk/asdk-deploy-considerations.md)** に関するページに記載のハードウェア、ソフトウェア、ネットワークの要件を満たす必要があります。

> [!TIP]
> 開発キット ホスト コンピューターにオペレーティング システムをインストールした後、ハードウェアがすべての要件を満たしていることを確認するには、[Azure Stack デプロイ要件チェック ツール](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)を使用できます。

### <a name="account-requirements"></a>アカウントの要件

デプロイの ID ソリューションとして、Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) のどちらを使用するかも選択する必要があります。 **[デプロイ計画の要件](./asdk/asdk-deploy-considerations.md#account-requirements)** でアカウントの要件を確認してください。

## <a name="download-and-extract-the-deployment-package"></a>デプロイ パッケージをダウンロードし、展開する

開発キットのホスト コンピューターの準備ができたら、ASDK デプロイ パッケージをダウンロードして展開します。 デプロイ パッケージには、Cloudbuilder.vhdx ファイルが含まれています。このファイルは、起動可能なオペレーティング システムが搭載された仮想ハード ドライブ (VHD) と、Azure Stack のインストール ファイルです。

開発キット ホストや別のコンピューターに、デプロイ パッケージをダウンロードできます。 展開されたデプロイ ファイルは 60 GB の空きディスク容量を占めます。そのため、別のコンピューターを利用することで、開発キット ホストのストレージ要件を緩和できます。

**[Azure Stack Development Kit (ASDK) をダウンロードして展開する](./asdk/asdk-download.md)**

## <a name="prepare-the-host-computer"></a>ホスト コンピューターを準備する

ASDK をインストールする前に、ホスト環境を準備し、開発キットの VHD から起動するようにシステムを構成する必要があります。 ホストを再起動すると、CloudBuilder.vhdx から起動し、ASDK のデプロイを開始できます。

**[ASDK ホスト コンピューターを準備する](./asdk/asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>ホスト コンピューターに ASDK をインストールする

ホスト コンピューターが VHD から起動したら、開発キットを Cloudbuilder 仮想環境にデプロイできます。 ASDK は、asdk-installer.ps1 PowerShell スクリプトを実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使用するか、[PowerShell のコマンド ライン](./asdk/asdk-deploy-powershell.md)からデプロイできます。

> [!NOTE]
> ホスト コンピューターが CloudBuilder.vhdx イメージから起動したら、ASDK をインストールする "*前*" に、[Azure Stack のテレメトリ設定](./asdk/asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)を構成できます。

**[Azure Stack Development Kit (ASDK) をインストールする](./asdk/asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>デプロイ後の構成を実行する

ASDK をインストールした後、推奨されるインストール後の確認と構成変更がいくつかあります。

**ツール**

Azure Stack の PowerShell ツールか GitHub ツールをインストールし、test-AzureStack コマンドレットを使用してインストールが正常に行われたことを確認できます。

**ID ソリューション**

Azure AD を使用するデプロイでは、Azure Stack の管理者ポータルとテナント ポータルの両方をアクティブにする必要があります。 アクティブにすることによって、ディレクトリの全ユーザーに関して適切なアクセス許可 (同意のページに一覧が表示されます) を Azure Stack ポータルと Azure Resource Manager に付与することに同意したことになります。

**パスワードの有効期限**

評価期間が終了する前に開発キット ホストのパスワードが期限切れにならないように、パスワードの有効期限のポリシーをリセットする必要があります。

> [!NOTE]
> ASDK のインストール "*後*" に [Azure Stack のテレメトリ設定](./asdk/asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)を構成することもできます。

**[ASDK デプロイ後のタスク](./asdk/asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure に登録する

Azure Stack に [Azure マーケットプレース項目をダウンロード](./asdk/asdk-marketplace-item.md)できるように、Azure Stack を Azure に登録する必要があります。

**[Azure Stack を Azure に登録する](./asdk/asdk-register.md)**

## <a name="next-steps"></a>次の手順

お疲れさまでした。 このクイックスタートの手順を完了することで、[管理者](https://adminportal.local.azurestack.external)ポータルと[ユーザー ポータル](https://portal.local.azurestack.external)が存在する ASDK 環境が作成されます。
