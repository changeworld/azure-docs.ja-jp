---
title: 開発者ポータルのウィジェットを投稿する方法
titleSuffix: Azure API Management
description: API Management 開発者ポータル リポジトリにウィジェットを投稿するときに従うべき推奨ガイドラインについて説明します。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ae491880dc7dc69a3930e6c967fb7a660db75f38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631478"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>API Management 開発者ポータルにウィジェットを投稿する方法

API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)にウィジェットを投稿する場合は、この 3 つの手順に従ってください。

1. リポジトリをフォークします。

1. ウィジェットを実装します。

1. 公式リポジトリにご自身のウィジェットを含めるために pull request を開きます。

ご自身のウィジェットはリポジトリのライセンスを継承します。 これは、セルフホステッド バージョンのポータルでの[オプトイン インストール](developer-portal-use-community-widgets.md)で使用できます。 開発者ポータル チームは、マネージド バージョンのポータルにもこれを含めることを決定する場合があります。

独自のウィジェットを開発する方法の例については、[ウィジェットの実装](developer-portal-implement-widgets.md)に関するチュートリアルを参照してください。

## <a name="contribution-guidelines"></a>投稿に関するガイドライン

このガイダンスは、お客様およびポータルの訪問者の安全とプライバシーを確保することを目的としています。 投稿が受け入れられるようにするには、これらのガイドラインに従ってください。

1. ご自身のウィジェットを `community/widgets/<your-widget-name>` フォルダーに配置します。

1. ウィジェットの名前は小文字と英数字で、単語の間にダッシュを入れる必要があります。 たとえば、「 `my-new-widget` 」のように入力します。

1. このフォルダーには、発行されたポータルのウィジェットのスクリーンショットが含まれている必要があります。

1. このフォルダーには、`/scaffolds/widget/readme.md` ファイルのテンプレートに従った `readme.md` ファイルが含まれている必要があります。

1. このフォルダーには、ウィジェットの依存関係をインストールまたは管理するための npm コマンドを含む `npm_dependencies` ファイルを含めることができます。

    すべての依存関係のバージョンを明示的に指定します。 次に例を示します。  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    ご自身のウィジェットには最小限の依存関係が必要です。 すべての依存関係は、レビュー担当者によって慎重に調査されます。 特に、ウィジェットの中核となるロジックは、ウィジェットのフォルダー内でオープンソース化する必要があります。 npm パッケージにはラップしないでください。

1. ウィジェットのフォルダー以外のファイルに対する変更は、ウィジェットの投稿の一部として許可されていません。 これには `/package.json` ファイルが含まれますが、これに限定されるわけではありません。

1. 追跡スクリプトを挿入したり、顧客が作成したデータをカスタム サービスに送信したりすることはできません。

    > [!NOTE]
    > 顧客が作成したデータは、`Logger` インターフェイスを介してのみ収集できます。

## <a name="next-steps"></a>次のステップ

- 投稿の詳細については、API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal/)を参照してください。

- 独自のウィジェットを開発する詳しい方法については、[ウィジェットの実装](developer-portal-implement-widgets.md)に関する記事を参照して、ステップごとに学習してください。

- コミュニティによって投稿されたウィジェットの使用方法については、[コミュニティ ウィジェットの使用](developer-portal-use-community-widgets.md)に関する記事を参照してください。