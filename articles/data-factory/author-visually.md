---
title: "Azure データ ファクトリを視覚的に作成する | Microsoft Docs"
description: "Azure データ ファクトリを視覚的に作成する方法について説明します"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>データ ファクトリの視覚的な作成
Azure Data Factory UX エクスペリエンスでは、ユーザーは、1 行のコードも記述することなく、データ ファクトリ内のリソースを視覚的に作成および展開できます。 このコード不要のインターフェイスを使うと、パイプライン キャンバス上にアクティビティをドラッグ アンド ドロップし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。 ADF UX ツールは 2 つの方法で使うことができます。

1. Data Factory サービスを直接操作します
2. コラボレーション、ソース管理、またはバージョン管理のための VSTS Git 統合を構成します

## <a name="authoring-with-data-factory"></a>Data Factory での作成
最初のオプションは、Data Factory モードで直接作成する方法です。 このアプローチは、VSTS コード リポジトリを使った作成とは異なり、変更した JSON エンティティが格納されるリポジトリはなく、コラボレーションまたはバージョン コントロール用に最適化されてはいません。

![Configure Data Factory](media/author-visually/configure-data-factory.png)

Data Factory モードでは、"発行" モードだけがあります。 行った変更はすべて、Data Factory サービスに直接発行されます。

![Data Factory への発行](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>VSTS Git 統合での作成
VSTS Git 統合を使った作成では、ソース管理とコラボレーションを利用しながらデータ ファクトリ パイプラインを作成できます。 ユーザーは、VSTS Git アカウント リポジトリとデータ ファクトリを関連付けて、ソース管理、コラボレーション、バージョン管理などを行うことができます。1 つの VSTS Git アカウントで、複数のリポジトリを使うことができます。 ただし、1 つの VSTS Git リポジトリを関連付けることができるデータ ファクトリは 1 つだけです。 VSTS アカウントとリポジトリをまだ持っていない場合は、[こちら](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)で作成してください。

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Azure Data Factory で VSTS Git リポジトリを構成する
データ ファクトリで VSTS Git リポジトリを構成するには 2 つの方法があります。

#### <a name="method-1-lets-get-started-page"></a>方法 1: [始めましょう] ページ

[始めましょう] ページに移動して、[Configure Code Repository]\(コード リポジトリの構成\) をクリックします。

![コード リポジトリを構成する](media/author-visually/configure-repo.png)

リポジトリの設定を構成するためのサイド パネルが表示されます。

![リポジトリの設定を構成する](media/author-visually/repo-settings.png)
* **[リポジトリの種類]**: Visual Studio Team Services Git (現時点では、GitHub はサポートされていません)。
* **[Visual Studio Team Services Account]\(Visual Studio Team Services アカウント\)**: アカウント名は、https://<アカウント名>.visualstudio.com からわかります。[こちら](https://www.visualstudio.com/team-services/git/)で VSTS アカウントにサインインし、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。
* **[プロジェクト名]**: プロジェクト名は、https://<アカウント名>.visualstudio.com/<プロジェクト名> からわかります。
* **[Git repository name]\(Git リポジトリ名\)**: リポジトリ名です。 VSTS プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使います。
* **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)**: このボックスをオンにすると、UX キャンバスで作成した現在データ ファクトリ リソースを、関連付けられている VSTS Git リポジトリに JSON 形式でインポートできます。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。    このチェック ボックスをオフにした場合は、既存のリソースは Git リポジトリにインポートされません。

#### <a name="method-2-from-authoring-canvas"></a>方法 2: 作成キャンバスから

"作成キャンバス" で、お使いのデータ ファクトリ名の下の [Data Factory] ドロップダウン メニューをクリックします。 次に、[Configure Code Repository]\(コード リポジトリの構成\) をクリックします。 **方法 1** と同じように、リポジトリの設定を構成するためのサイド パネルが表示されます。 設定について詳しくは、前のセクションをご覧ください。

![コード リポジトリを構成する 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>バージョン コントロール
開発者は、バージョン コントロール (ソース管理とも呼ばれます) システムを使うことで、コードの共同作業を行い、コード ベースに対して行われた変更を追跡することができます。 ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

データ ファクトリに関連付けられた各 VSTS Git リポジトリには、マスター ブランチが存在します。 VSTS Git リポジトリへのアクセス権を持つすべてのユーザーには、マスター ブランチから変更を行うとき、同期と発行の 2 つのオプションがあります。

![同期と発行](media/author-visually/sync-publish.png)

#### <a name="sync"></a>同期

[Sync] をクリックすると、マスター ブランチからローカル ブランチに変更をプルしたり、ローカル ブランチからマスター ブランチに変更をプッシュしたりできます。

![変更の同期](media/author-visually/sync-change.png)

#### <a name="publish"></a>[発行]
 マスター ブランチでの変更を Data Factory サービスに発行します。

> [!NOTE]
> **マスター ブランチは Data Factory サービスに展開されているものを代表しているわけではありません**。 マスター ブランチを Data Factory サービスに手動で発行する "*必要があります*"。




## <a name="expression-language"></a>式言語

プロパティの値を定義するときは、Azure Data Factory でサポートされる式言語を使って式を指定できます。 サポートされている式について詳しくは、「[Azure Data Factory の式と関数](control-flow-expression-language-functions.md)」をご覧ください。

ユーザー エクスペリエンスでは次のようにプロパティ値の式を指定します。

![式言語](media/author-visually/expression-language.png)

## <a name="parameters"></a>parameters
ユーザーは、[パラメーター] タブで、パイプラインやデータセットのパラメーターを指定できます。さらに、[動的なコンテンツの追加] をクリックすることにより、簡単にプロパティでパラメーターを利用できます。

![動的コンテンツ](media/author-visually/dynamic-content.png)

プロパティ値では、既存のパラメーターを使うことも、新しいパラメーターを指定することもできます。

![parameters](media/author-visually/parameters.png)

## <a name="feedback"></a>フィードバック
[フィードバック] アイコンをクリックして、さまざまな機能や、発生している問題について Microsoft にフィードバックをお寄せください。

![フィードバック](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>次の手順

パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
