---
title: Azure App Configuration Sync で GitHub Actions を使用する
description: 定義されたアクションが GitHub リポジトリで実行されたときに、GitHub Actions を使用して App Configuration インスタンスに対する更新をトリガーします
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1b7586e6321318a3fe4566e8c744c17a1d7ee179
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310616"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>GitHub Actions を使用して App Configuration インスタンスを同期する
Azure App Configuration は、GitHub リポジトリで実行されたアクションによってトリガーされたときに、GitHub Actions を使用して App Configuration インスタンスを更新します。 GitHub ワークフローを利用してアプリ構成を更新し、アプリ コードの更新に使用されているのと同じワークフローにアプリ構成の更新を統合することができます。

GitHub Actions の[ワークフロー](https://help.github.com/articles/about-github-actions#workflow)とは、GitHub リポジトリに定義されている自動化されたプロセスです。 このプロセスは、GitHub プロジェクトを構築してデプロイする方法を GitHub に指示します。 Azure App Configuration には、ソース リポジトリに変更が加えられたときに App Configuration インスタンスの更新を有効にするための *Azure App Configuration Sync* アクションが用意されています。 

ワークフローは、リポジトリの `/.github/workflows/` パスにある YAML (.yml) ファイルによって定義されます。 この定義には、ワークフローを定義するさまざまな手順とパラメーターが含まれています。

GitHub イベント (リポジトリへのプッシュなど) によって、GitHub アクション ワークフローをトリガーできます。  Azure には、指定された GitHub アクションが発生したときに App Configuration インスタンスの更新をトリガーできるようにする *Azure App Configuration Sync* アクションが用意されています。 これにより、チームは、アプリ コードと同様に、アプリ構成ファイルをプッシュ、レビュー、または分岐するときに GitHub のコア機能を利用できます。

GitHub の[ドキュメント](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)では、GitHub ワークフローとアクションについて詳細に説明されています。 

## <a name="enable-github-actions-in-your-repository"></a>リポジトリで GitHub Actions を有効にする
この GitHub アクションの使用を開始するには、リポジトリにアクセスし、 **[Actions]\(アクション\)** タブを選択します。"Azure App Configuration Sync" を検索して、マーケットプレースでこの GitHub アクションを見つけて選択します。 

> [!div class="mx-imgBorder"]
> ![[Actions]\(アクション\) タブを選択する](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![App Configuration Sync アクションを選択する](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>プッシュ後に構成ファイルを同期する
このアクションは、変更が `appsettings.json` にプッシュされたときに Azure App Configuration ファイルを同期します。 開発者が `appsettings.json` に変更を加えてプッシュすると、Azure App Configuration Sync アクションにより、App Configuration インスタンスが新しい値で更新されます。

このワークフローの最初のセクションは、`appsettings.json` を含む "*プッシュ*" が "*マスター*" ブランチに対して "*行われたとき*" にアクションがトリガーされることを指定しています。 2 番目のセクションは、アクションがトリガーされると実行されるジョブを一覧で示しています。 このアクションは、リポジトリにシークレットとして格納されている接続文字列を使用して、関連するファイルをチェックアウトし、App Configuration インスタンスを更新します。  Github でのシークレットの使用の詳細については、暗号化されたシークレットの作成と使用に関する[こちらの記事](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)を参照してください。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>同期時に動的ラベルを使用する
前のアクションは、`appsettings.json` が更新されるたびに、単に App Configuration インスタンスを更新しました。 このアクションは、同期のたびに動的ラベルを挿入して、各同期を一意に識別できるようにします。  これにより、コード変更をすばやく構成変更にマップすることができます。

このワークフローの最初のセクションは、`appsettings.json` を含む "*プッシュ*" が "*マスター*" ブランチに対して "*行われたとき*" にアクションがトリガーされることを指定しています。 2番目のセクションは、コミット ハッシュに基づいて構成更新用の一意のラベルを作成するジョブを実行します。 その後ジョブは、新しい値とこの更新用の一意のラベルを使用して、App Configuration インスタンスを更新します。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>厳格な同期を使用する
厳格モードが有効になっている場合、同期により、App Configuration インスタンスは、指定されたプレフィックスとラベルについて構成ファイルを正確に照合します。 構成ファイルに含まれていない、同じプレフィックスとラベルを持つキーと値のペアは削除されます。 
 
厳格モードが有効になっていない場合は、同期では、構成ファイルのキーと値の設定のみが行われます。 キーと値のペアは削除されません。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>最大深度を使用して GitHub アクションを制限する
入れ子になった JSON 属性の既定の動作では、オブジェクト全体がフラット化されます。  次の JSON は、このキーと値のペアを定義しています。

| Key | 値 |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
入れ子になったオブジェクトが Configuration インスタンスにプッシュされる値となるよう意図されている場合は、*depth* 値を使用して、適切な深さでフラット化を停止できます。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

depth を 2 に指定した場合、上記の例では次のキーと値のペアが返されます。

| Key | 値 |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>アクションの入力について
入力パラメーターは、実行時にアクションによって使用されるデータを指定します。  App Configuration Sync で受け入れられる入力パラメーターと、それぞれの予期される値を次の表に示します。  GitHub Actions でのアクション入力の詳細については、GitHub の[ドキュメント](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)を参照してください。

> [!Note]
> 入力 ID では大文字と小文字は区別されません。


| 入力名 | 必須 | 値 |
|----|----|----|
| configurationFile | はい | リポジトリ内の構成ファイルへのパス。リポジトリのルートを基準とした相対パスです。  glob パターンがサポートされており、複数のファイルを含めることができます。 |
| format | はい | 構成ファイルのファイル形式。  有効な形式:JSON、YAML、properties。 |
| connectionString | はい | App Configuration インスタンスの接続文字列。 接続文字列は、GitHub リポジトリにシークレットとして格納する必要があります。また、ワークフローではシークレット名のみを使用する必要があります。 |
| separator | はい | 構成ファイルをキーと値のペアにフラット化するときに使用される区切り記号。  有効な値: , ; : - _ __ / |
| prefix | いいえ | キーの先頭に追加されるプレフィックス。 |
| ラベル●らべる○ | いいえ | キーと値のペアを設定するときに使用されるラベル。 指定されない場合、null ラベルが使用されます。 |
| strict | いいえ | 厳格モードを有効にするかどうかを決めるブール値。 既定値は false です。 |
| depth | いいえ | 構成ファイルをフラット化するための最大深度。  depth は正の数値である必要があります。  既定では、最大深度はありません。 |
| tags | いいえ | キーと値のペアに設定されているタグを指定します。  予期される形式は、次の形状の JSON オブジェクトの文字列フィールド形式です:  { [propertyName: string]: string; } 各プロパティ名と値はタグになります。 |

## <a name="next-steps"></a>次のステップ

この記事では、GitHub の App Configuration Sync アクションと、それを使用して App Configuration インスタンスの更新を自動化する方法について説明しました。 Azure App Configuration がキーと値のペアの変更に対してどのように反応するかについては、続けて次の[記事](./concept-app-configuration-event.md)をお読みください。
