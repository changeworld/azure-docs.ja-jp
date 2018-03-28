---
title: Azure Data Factory でのビジュアルの作成 | Microsoft Docs
description: Azure Data Factory でビジュアルの作成機能を使用する方法について説明します
services: data-factory
documentationcenter: ''
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
ms.openlocfilehash: 954693ee208dc7868a5a5ad0e774c5c352036627
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory でのビジュアルの作成
Azure Data Factory ユーザー インターフェイス エクスペリエンス (UX) を使用すると、コードを記述することなくデータ ファクトリ用リソースを視覚的に作成してデプロイできます。 パイプライン キャンバスにアクティビティをドラッグし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。 UX を使用してビジュアルの作成機能を実行するには 2 つの方法があります。

- Data Factory サービスにより直接作成します。
- コラボレーション、ソース管理、またはバージョン管理のための Visual Studio Team Services (VSTS) Git 統合を使用して作成します。

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory サービスを使用して直接作成
Data Factory サービスによるビジュアルの作成は、VSTS によるビジュアルの作成と次の 2 点で異なります。

- Data Factory サービスには、変更について JSON エンティティを格納するためのリポジトリが含まれていません。
- Data Factory サービスは、コラボレーションまたはバージョン管理用に最適化されていません。

![Data Factory サービスの構成 ](media/author-visually/configure-data-factory.png)

UX **作成キャンバス**を使用して Data Factory サービスで直接作成する場合、**発行**モードを使用できます。 行った変更はすべて、Data Factory サービスに直接発行されます。

![発行モード](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>VSTS Git 統合での作成
VSTS Git 統合を使ったビジュアルの作成では、データ ファクトリ パイプラインの使用にあたりソース管理とコラボレーションがサポートされています。 データ ファクトリを VSTS Git アカウント リポジトリと関連付けて、ソース管理、コラボレーション、バージョン管理などを行うことができます。 1 つの VSTS Git アカウントで複数のリポジトリを使うことができますが、1 つの VSTS Git リポジトリに関連付けられるのは 1 つのデータ ファクトリのみです。 VSTS アカウントまたはリポジトリを持っていない場合は、[この手順](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)に従ってリソースを作成します。

> [!NOTE]
> スクリプトとデータ ファイルは VSTS Git リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインでは、VSTS Git リポジトリに保存されているスクリプトまたはデータ ファイルが Azure Storage に自動的にアップロードされません。

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Azure Data Factory で VSTS Git リポジトリを構成する
データ ファクトリで VSTS Git リポジトリを構成するには 2 つの方法があります。

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>構成方法 1: [始めましょう] ページ
Azure Data Factory で **[始めましょう]** ページに移動します。 **[Configure Code Repository]\(コード リポジトリの構成\)** を選択します。

![VSTS コード リポジトリの構成](media/author-visually/configure-repo.png)

**[リポジトリ設定]** 構成ウィンドウが表示されます。

![コード リポジトリ設定の構成](media/author-visually/repo-settings.png)

ウィンドウに次の VSTS コード リポジトリの設定が表示されます。

| Setting | [説明] | 値 |
|:--- |:--- |:--- |
| **リポジトリの種類** | VSTS コード リポジトリの種類。<br/>**注**: GitHub は現在サポートされていません。 | Visual Studio Team Services Git |
| **Visual Studio Team Services アカウント** | VSTS アカウント名。 VSTS アカウント名は以下に見つかります。`https://{account name}.visualstudio.com` [VSTS アカウントにサインイン](https://www.visualstudio.com/team-services/git/)し、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。 | \<アカウント名> |
| **ProjectName** | VSTS プロジェクト名。 VSTS プロジェクト名は以下に見つかります。`https://{account name}.visualstudio.com/{project name}` | \<VSTS プロジェクト名> |
| **RepositoryName** | VSTS コード リポジトリ名。 VSTS プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使用できます。 | \<VSTS コード リポジトリ名> |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX **作成キャンバス**からの既存のデータ ファクトリ リソースを VSTS Git リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>構成方法 2: UX 作成キャンバス
Azure Data Factory UX **作成キャンバス**でデータ ファクトリを見つけます。 **[Data Factory]** ドロップダウン メニューを選択して、**[Configure Code Repository]\(コード リポジトリの構成\)** を選択します。

構成ウィンドウが表示されます。 構成設定について詳しくは、「<a href="#method1">構成方法 1</a>」の説明をご覧ください。

![UX 作成のコード リポジトリ設定の構成](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>バージョン コントロールの使用
開発者は、バージョン コントロール (_ソース管理_とも呼ばれます) システムを使うことで、コードの共同作業を行い、コード ベースに対して行われた変更を追跡することができます。 ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

データ ファクトリに関連付けられた各 VSTS Git リポジトリには、マスター ブランチが存在します。 VSTS Git リポジトリへのアクセスがある場合、**[同期]** または **[発行]** を選択してコードを変更できます。

![同期または発行してコードを変更](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>コード変更の同期
**[同期]** を選択すると、マスター ブランチからローカル ブランチに変更をプルしたり、ローカル ブランチからマスター ブランチに変更をプッシュしたりできます。

![コード変更の同期](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>コード変更の発行
**[発行]** を選択して、マスター ブランチのコード変更を Data Factory サービスに発行します。

> [!IMPORTANT]
> マスター ブランチは Data Factory サービスに展開されているものを代表しているわけではありません。 マスター ブランチを Data Factory サービスに手動で発行する "*必要があります*"。

## <a name="use-the-expression-language"></a>式言語の使用
プロパティ値の式を指定するには、Azure Data Factory でサポートされる式言語を使用します。 サポートされている式について詳しくは、「[Azure Data Factory の式と関数](control-flow-expression-language-functions.md)」をご覧ください。

UX **作成キャンバス**を使用して、プロパティ値の式を指定します。

![式言語の使用](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>パラメーターの指定
Azure Data Factory の **[パラメーター]** タブで、パイプラインやデータセットのパラメーターを指定できます。**[動的なコンテンツの追加]** を選択して、プロパティでパラメーターを簡単に使用できます。

![動的なコンテンツの追加](media/author-visually/dynamic-content.png)

既存のパラメーターを使用するか、プロパティ値の新しいパラメーターを指定できます。

![プロパティ値のパラメーターの指定](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>フィードバックの提供
機能に関するコメントを送信するには、またはツールの問題について Microsoft に通知するには、**[フィードバック]** を選択します。

![フィードバック](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>次の手順
パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
