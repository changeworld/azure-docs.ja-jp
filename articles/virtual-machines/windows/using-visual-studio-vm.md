---
title: "Azure Virtual Machine での Visual Studio の使用| Microsoft Docs"
description: "Azure Virtual Machine での Visual Studio の使用"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a id="top"> </a> Azure 上の Visual Studio のイメージ
事前に構成済みの Azure 仮想マシン (VM) 上で Visual Studio を使用することは、ゼロから稼働状態の開発環境を構築する最も簡単かつ迅速な方法です。  さまざまな Visual Studio 構成のシステム イメージは、[Azure Marketplace](https://portal.azure.com/) で入手できます。 VM を起動するだけで、使用を開始できます。

Azure を利用するのが初めてであれば、 [無料の Azure アカウントを作成します](https://azure.microsoft.com/free)。

## <a name="what-configurations-and-versions-are-available"></a>どんな構成とバージョンを使用できますか。
Azure Marketplace で、最新のメジャー バージョンである Visual Studio 2017 および Visual Studio 2015 のイメージを検索します。  メジャー バージョンごとに、最初にリリースされたバージョン ('RTW' と記載) と "最新" の更新バージョンが表示されます。  これらの異なるバージョンそれぞれについて、Visual Studio Enterprise と Visual Studio Community エディションを検索します。

|               リリース バージョン              |          エディション            |    製品バージョン    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017 - 最新 (バージョン 15.5) |    Enterprise、Community     |     Version 15.5.3    |
|         Visual Studio 2017 - RTW           |    Enterprise、Community     |     Version 15.0.7    |
|   Visual Studio 2015 - 最新 (Update 3)   |    Enterprise、Community     | Version 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | なし (サービスの有効期限切れ) |          ---          |

> [!NOTE]
> Microsoft サービス ポリシーに従って、最初にリリースされた Visual Studio 2015 のバージョン ('RTW' と記載) のサービスは、期限切れになりました。  そのため、Visual Studio 2015 Update 3 は、Visual Studio 2015 製品ラインに提供されているそれ以外のバージョンだけです。

詳細については、[Visual Studio サービス ポリシー](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs)に関するページをご覧ください。

## <a name="what-features-are-installed"></a>どのような機能がインストールされていますか。
各イメージには、その Visual Studio エディションで推奨される機能セットが含まれています。  一般的に、インストールには以下が含まれます。

* 該当のワークロードで推奨されているオプションのコンポーネントを含む、使用可能なすべてのワークロード
* .NET 4.6.2 および .NET 4.7 SDK、Targeting Packs、および 開発者ツール
* Visual F#
* Visual Studio 向け GitHub 拡張
* LINQ to SQL ツール

以下は、イメージを作成するときに、Visual Studio のインストールに使用するコマンド ラインです。

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

イメージに必要な Visual Studio 機能が含まれていない場合は、フィードバック ツール (ページの右上隅) からフィードバックを提供してください。

## <a name="what-size-vm-should-i-choose"></a>どの VM サイズを選択すればいいですか。
新しい仮想マシンのプロビジョニングは簡単であり、Azure では仮想マシンの全範囲のサイズを提供しています。  何らかのハードウェアを取得する場合と同様に、パフォーマンスとコストのバランスを考えます。  Visual Studio は強力なマルチスレッド アプリケーションなので、2 つ以上のプロセッサと 7 GB 以上のメモリを含む VM サイズを検討します。  最新のマシン サイズについては、「[ Azure の Windows 仮想マシンのサイズ](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)」をご覧ください。

Azure の場合、最初の選択にとらわれることはありません。VM サイズをもう一度変更して、最初の選択のバランスを再調整できます。  より適切なサイズの新しい VM をプロビジョニングするか、または既存の VM のサイズを別の基本ハードウェアへ変更することが可能です。  詳細については、「[Windows VM のサイズ変更](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm)」をご覧ください。

## <a name="after-i-get-the-vm-running-then-what"></a>VM を実行したら、次は何をすればいいですか。
Visual Studio では、Azure の "ライセンス持ち込み" モデルに従います。  そのため、専用ハードウェアでのインストールと同様に、最初の手順の 1 つとして、お使いの Visual Studio インストールのラインセンス付与を行います。  Visual Studio サブスクリプションに関連付けられた Microsoft アカウントでサインインして Visual Studio をロック解除するか、または初期購入時のプロダクト キーを使って Visual Studio をロック解除できます。  詳細については、[Visual Studio へのサインイン](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio)と [Visual Studio のロック解除方法](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio)に関するページをご覧ください。

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>dev VM を作成した後、今後のためやチームでの使用のために保存 (キャプチャ) するにはどうしたらいいですか。

開発環境のスペクトルは幅広く、より複雑な環境の構築に関連する実質的なコストがあります。  ただし、お使いの環境の構成に関係なく、Azure では今後の使用のため、自身のため、あるいはチームの他のメンバーと使用するための '基本イメージ' として、完全に構成された VM を保存/キャプチャすることで、この投資を簡便に守れるようになっています。  次に、新しい VM を起動する場合は、Marketplace イメージではなく、基本イメージからプロビジョニングします。

簡単な概要としては、sysprep を行って実行中の VM をシャットダウンしてから、Azure Portal の UI を使用して VM を 1 つのイメージとして*キャプチャ (図 1)* します。  Azure は、選択したストレージ アカウントのイメージを格納した `.vhd` ファイルを保存します。  次に、お使いのサブスクリプションのリソース一覧に、新しいイメージがイメージ リソースとして表示されます。

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(図 1) Azure Portal の UI を使用してイメージをキャプチャする*</center>

詳細については、[VM をイメージにキャプチャする](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource)方法をご確認ください。

  **注:**  VM の sysprep を忘れずに行ってください。  この手順を実行しなかった場合、Azure ではイメージから VM をプロビジョニングできません。

> [!NOTE]
> イメージのストレージに対しては、引き続き何らかのコストが発生しますが、ゼロから VM を再構築する人件費 (VM を必要とするチーム内の各メンバーに対して) に比べれば、コストの増分は少額になります。  たとえば、チームのメンバー全員で再利用可能な 127 GB のイメージを作成して 1 か月間保管するには、数ドルのコストがかかります。  しかし、これらのコストは、個別使用のために適切に構成した dev ボックスを作成して検証する各作業者に支払う給与に比べれば少額です。

さらに、多様な開発構成や複数のコンピューター構成など、お使いの開発タスクやテクノロジで、より大きなサイズが必要になる場合があります。  Azure DevTest Labs を使用して、'ゴールデン イメージ' の構築を自動化する _"レシピ"_ を作成し、チームで実行する VM のポリシーを管理できます。  DevTest Labs の詳細については、「[開発者のための Azure DevTest Labs の使用](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab)」が最適な資料です。

## <a name="next-steps"></a>次の手順
事前構成済みの Visual Studio イメージについて確認したので、次の手順では新しい VM を作成します。

* [Azure Portal を使用した VM の作成](quick-create-portal.md)
* [Windows 仮想マシンの概要](overview.md)
