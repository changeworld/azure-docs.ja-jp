---
title: Microsoft Security Code Analysis のオンボード ガイド
description: この記事では、Microsoft Security Code Analysis 拡張機能のインストールについて説明します
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 108d116500454605f33de201caffc11ae263f74c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851504"
---
# <a name="onboarding-and-installing"></a>オンボードとインストール

Microsoft Security Code Analysis の使用を開始するための前提条件:

- 次のセクションで説明する、対象となる Microsoft 統合サポート プラン。
- Azure DevOps 組織。
- Azure DevOps 組織に拡張機能をインストールするためのアクセス許可。
- クラウドでホストされている Azure DevOps パイプラインに同期できるソース コード。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 拡張機能のオンボード

- 以下のいずれかのサポート プランをお持ちの場合、拡張機能にアクセスするには、テクニカル アカウント マネージャーに連絡して、時間を購入するか、既存の時間を交換します。
  - Unified Support Advanced レベル
  - Unified Support Performance レベル
  - 開発者向け Premier サポート
  - パートナー向け Premier サポート
  - エンタープライズ向け Premier サポート
- 以下のいずれかのサポート サービスをご利用の場合、または Microsoft のサポート プランをお持ちでない場合は、資格のあるサポート プランにアップグレードする必要があります。
  - パートナー向け Azure サポート
  - Azure Basic サポート
  - Azure Developer サポート
  - Azure Standard サポート
  - Azure Professional Direct
  - 統合サポート コア レベル
- 資格のあるサポート プランを購入するには、[サポート サービスのホーム ページ](https://www.microsoft.com/enterprise/services/support)にアクセスしてください。
- サポート契約を結んだ後、作業の開始と、必要なすべての詳細情報の収集を支援する、テクニカル アカウント マネージャーに連絡します。

>[!NOTE]
>サポート契約を結んでいないお客様のために、Microsoft では、統合サポート プランを購入しなくてもサード パーティのパートナーを活用して拡張機能を購入できるパートナー購入プログラムの実現に向けて取り組んでいます。 [こちらまでメールを](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Onboarding%20Request)お寄せください。 このオプションに興味がある旨をお知らせいただければ、 プログラムの提供が開始されしだい、こちらからご連絡いたします。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 拡張機能のインストール

1. 拡張機能を Azure DevOps 組織と共有した後、Azure DevOps 組織のページに移動します。 そのようなページの URL の例は、`https://dev.azure.com/contoso` です。
1. 右上隅の自分の名前の横にあるショッピング バッグ アイコンを選択し、 **[拡張機能の管理]** を選択します。
1. **[共有]** を選択します。
1. Microsoft Security Code Analysis 拡張機能を選択し、 **[インストール]** を選択します。
1. ドロップダウン リストから、拡張機能をインストールする Azure DevOps 組織を選択します。
1. **[インストール]** を選択します。 インストールが完了した後、拡張機能を使い始めることができます。

>[!NOTE]
> 拡張機能をインストールするためのアクセス権がない場合でも、インストール手順を続行します。 インストール プロセスの間に、Azure DevOps 組織の管理者にアクセスを要求できます。

拡張機能のインストールが済むと、セキュリティで保護された開発ビルド タスクが表示され、Azure Pipelines に追加できるようになります。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Azure DevOps パイプラインへの特定のビルド タスクの追加

1. Azure DevOps 組織から、チーム プロジェクトを開きます。
1. **[パイプライン]**  >  **[ビルド]** を選択します。
1. 拡張機能のビルド タスクを追加するパイプラインを選択します。
   - 新しいパイプライン: **[新規作成]** を選択し、詳細な手順に従って新しいパイプラインを作成します。
   - パイプラインの編集: 既存のパイプラインを選択し、 **[編集]** を選択して、パイプラインの編集を始めます。
1. **[+]** を選択して、 **[タスクの追加]** ウィンドウに移動します。
1. 一覧から、または検索ボックスを使用して、追加するビルド タスクを見つけます。 **[追加]** を選択します。
1. タスクに必要なパラメーターを指定します。
1. 新しいビルドがキューに追加されます。
   >[!NOTE]
   >ファイルとフォルダーのパスは、ソース リポジトリのルートを基準とした相対パスです。 出力ファイルとフォルダーをパラメーターとして指定した場合、それらはビルド エージェントで定義した共通の場所に置き換えられます。

> [!TIP]
>
> - ビルド後に分析を実行するには、ビルドのビルド成果物の発行ステップの後に、Microsoft Security Code Analysis ビルド タスクを配置します。 そうすることで、スタティック分析ツールを実行する前に、ビルドを完了し、結果をポストすることができます。
> - セキュリティで保護された開発ビルド タスクの場合は、 **[エラー時に続行]** を常に選択します。 1 つのツールが失敗した場合でも、他のツールは実行できます。 ツールの間に相互依存関係はありません。
> - Microsoft Security Code Analysis ビルド タスクは、ツールを正常に実行できなかった場合にのみ失敗します。 ただしツールによってコード内で問題が識別された場合でも成功します。 分析後ビルド タスクを使用することにより、ツールでコード内の問題が識別されたら失敗するようにビルドを構成できます。
> - 一部の Azure DevOps ビルド タスクは、リリース パイプラインを介して実行される場合、サポートされません。 具体的には、Azure DevOps では、リリース パイプライン内から成果物を発行するタスクはサポートされていません。
> - パラメーターとして指定できる、Azure DevOps チーム ビルドの定義済み変数の一覧については、[Azure DevOps のビルド変数](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

ビルド タスクの構成の詳細については、[構成ガイド](security-code-analysis-customize.md)または [YAML の構成ガイド](yaml-configuration.md)を参照してください。

この拡張機能と提供されるツールにさらに質問がある場合は、[FAQ ページ](security-code-analysis-faq.md)を参照してください。
