---
title: 状態構成のために構成を複合リソースに変換する - Azure Automation
description: Azure Automation で状態構成のために構成を複合リソースに変換する方法について説明します。
keywords: DSC, PowerShell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585521"
---
# <a name="convert-configurations-to-composite-resources"></a>構成を複合リソースに変換する

> 適用先:Windows PowerShell 5.1

構成の作成を開始したら、設定のグループを管理する "シナリオ" をすばやく作成できます。
次に例を示します。

- Web サーバーを作成する
- DNS サーバーを作成する
- SharePoint サーバーを作成する
- SQL クラスターを構成する
- ファイアウォール設定を管理する
- パスワード設定を管理する

この作業を他のユーザーと共有することに関心がある場合は、構成を[複合リソース](/powershell/scripting/dsc/resources/authoringresourcecomposite)としてパッケージ化することが最良の選択肢です。
複合リソースを初めて作成するのは非常に困難な場合があります。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されているソリューションについて説明します。
> サポートは、Microsoft からではなく、GitHub コラボレーションの形式でのみ利用できます。

## <a name="community-project-compositeresource"></a>コミュニティ プロジェクト:CompositeResource

この課題を解決するために、[CompositeResource](https://github.com/microsoft/compositeresource) という名前の、コミュニティが管理するソリューションが作成されました。

CompositeResource は、構成から新しいモジュールを作成するプロセスを自動化します。
ワークステーション (またはビルド サーバー) 上で、メモリに読み込まれるように構成スクリプトを[ドット ソース形式で読み込む](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)ことから始めます。
次に、構成を実行して MOF ファイルを生成するのではなく、CompositeResource モジュールによって提供される関数を使用して変換を自動化します。
このコマンドレットは、構成の内容を読み込み、パラメーターの一覧を取得し、必要なすべてのものを含む新しいモジュールを生成します。

モジュールを生成したら、バージョンをインクリメントし、変更を加えるたびにリリース ノートを追加して、独自の [PowerShellGet リポジトリ](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)に公開することができます。

構成 (または複数の構成) を含む複合リソース モジュールを作成したら、それらを Azure の [Composable Authoring Experience](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation) で使用するか、[DSC 構成スクリプト](/azure/automation/compose-configurationwithcompositeresources)に追加して MOF ファイルを生成し、[MOF ファイルを Azure Automation にアップロードする](/powershell/scripting/dsc/configurations/configurations)ことができます。
次に、[オンプレミス](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)または [Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) のいずれかからサーバーを登録して、構成をプルします。
プロジェクトの最新のアップデートでは、PowerShell ギャラリーから構成をインポートするプロセスを自動化する、Azure Automation 用の [Runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) も公開されています。

DSC の複合リソースの作成の自動化を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/compositeresource/)にアクセスし、ソリューションをダウンロードするか、[Project Site] をクリックして[ドキュメント](https://github.com/microsoft/compositeresource)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)
- [DSC リソース](/powershell/scripting/dsc/resources/resources)
- [ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaconfig)
