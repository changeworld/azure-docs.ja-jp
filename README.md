# Azure 技術ドキュメント作成協力者ガイド

この GitHub リポジトリには、Azure ドキュメント センター ([http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation)) で発行されている技術ドキュメントのソースが格納されています。

また、技術ドキュメントの作成にご協力いただく際に役立つガイダンスも納められています。作成協力者向けガイドに掲載されている記事の一覧は、[インデックス](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)のページでご覧いただけます。

## Azure ドキュメントの作成への協力

Azure ドキュメントにご興味をお持ちくださり、ありがとうございます。

* [作成協力の方法](#ways-to-contribute)
* [Azure コンテンツの作成協力について](#about-your-contributions-to-azure-content)
* [リポジトリの構造](#repository-organization)
* [GitHub、Git、およびこのリポジトリの使い方](#use-github-git-and-this-repository)
* [マークダウンを使用してトピックの書式を設定する方法](#how-to-use-markdown-to-format-your-topic)
* [その他のリソース](#その他のリソース)
* [作成協力者ガイドに掲載されている全記事のインデックス](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md) (新しいページが開きます)

## 作成協力の方法

GitHub ユーザー インターフェイスからも技術記事の作成に簡単にご協力いただけます。目的の記事をこのリポジトリで見つけるか、[http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) で記事にアクセスして、GitHub 内のソースにアクセスするためのリンクをクリックしてください。

##Azure コンテンツの作成協力について

###軽微な修正

このリポジトリに含まれているドキュメントとコード例に関して提出された軽微な修正や明確化は、[Microsoft Azure Web サイト使用条件](http://azure.microsoft.com/support/legal/website-terms-of-use/)の対象になります。


###より大規模な提出

ドキュメントとコード例に関する新しい記事または大幅な変更についてプル リクエストを送信された場合、次のグループのいずれかに該当する作成協力者の方には、オンラインの Contribution License Agreement (CLA) の提出をお願いするコメントを GitHub に送信いたします。

* Microsoft Open Technologies グループのメンバー
* Microsoft の従業員でない作成協力者

プル リクエストが正しく受理されるように、オンライン フォームへのご記入をお願いいたします。

詳細については、[http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla) をご覧ください。

## リポジトリの構造

azure-content リポジトリ内のコンテンツは、[Azure.Microsoft.com](http://azure.microsoft.com) のドキュメント構造に準拠しています。このリポジトリには、2 つのルート フォルダーがあります。

### \articles

*\articles* フォルダーには、ドキュメント記事がマークダウン ファイルとして書式設定され、*.md* の拡張子を付加して格納されています。

このルート ディレクトリ内の記事は、Azure.Microsoft.com に *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/* のパスで発行されています。

* **記事のファイル名:** [ファイルの名前付けに関するこちらのガイダンス](https://github.com/Azure/azure-content/blob/master/contributor-guide/file-names-and-locations.md)をご覧ください。

サービス別のフォルダーに格納されている記事は、Azure.Microsoft.com に 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/* のパスで発行されています。

* **メディア用サブフォルダー:** *\articles* フォルダーには、ルート ディレクトリ記事のメディア ファイル用に *\media* フォルダーが含まれており、内部には各記事の画像が格納されたサブフォルダーが含まれています。サービス別フォルダーにも、それぞれのサービス フォルダー内の記事用のメディア フォルダーが含まれています。記事の画像フォルダーの名前は、記事ファイルの名前から *.md* ファイル拡張子を除いたものが設定されています。

### \includes

繰り返し使用できるコンテンツ セクションを作成して、1 つ以上の記事に含めることができます。詳細については、[技術コンテンツで使用されるカスタム拡張](https://github.com/Azure/azure-content/blob/master/contributor-guide/custom-markdown-extensions.md)に関するトピックをご覧ください。

### \markdown templates

このフォルダーには、記事に必要な基本のマークダウン書式が設定された標準のマークダウン テンプレートが格納されています。

### \contributor-guide

このフォルダーには、作成協力者ガイドを構成している記事が格納されています。

## GitHub、Git、およびこのリポジトリの使い方

作成に協力する方法、GitHub の UI を使用して軽微な変更を投稿する方法、より大規模な投稿のためにリポジトリのフォークとクローンを行う方法については、[GitHub の記事作成用ツールをインストールしてセットアップする方法](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md)に関するトピックをご覧ください。

Git Bash をインストールしてローカルで作業する場合は、ローカルの作業ブランチを新たに作成し、変更を加え、その変更をメイン ブランチに送信する手順が、[記事の新規作成または既存の記事の更新のための Git コマンド](https://github.com/Azure/azure-content/blob/master/contributor-guide/git-commands-for-master.md))に関するトピックに記載されておりますので、ご覧ください。

### Branches

ローカルの作業ブランチを作成する際は、変更のスコープを明確に設定することをお勧めします。作業の流れを効率の良いものにし、マージが競合する可能性を減らすために、各ブランチではコンセプト/記事を 1 つに制限してください。新しいブランチのスコープに適しているのは、次のような作業です。

* 記事 (および関連する画像) の新規作成
* 記事の誤字脱字や文法の編集
* 多数の記事に対する統一された書式変更の適用 (例: 新しい著作権用フッター)

## マークダウンを使用してトピックの書式を設定する方法

このリポジトリ内のすべての記事には、GitHub 用のマークダウンが使用されています。以下のリソースが役立ちます。

- [マークダウンの基本](https://help.github.com/articles/markdown-basics/)

- [印刷可能なマークダウンの一覧](https://github.com/Azure/azure-content/blob/master/contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- マークダウン エディターの一覧については、[ツールとセットアップに関するトピック](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md#install-a-markdown-editor)をご覧ください。

## 記事のメタデータ

記事のメタデータを使用すると、作者の属性、作成協力者の属性、階層リンク、記事の説明、SEO 最適化など、azure.microsoft.com Web サイトが備える機能や、Microsoft がコンテンツのパフォーマンスを評価するために使用しているレポート機能を利用できます。このように、メタデータは重要性の高い要素です。 [こちらのリンクから、メタデータを正しく活用するためのガイダンスにアクセスできます](https://github.com/Azure/azure-content/blob/master/contributor-guide/article-metadata.md)。

## その他のリソース

すべてのガイダンス トピックについては、[こちらの作成協力者ガイドのインデックス](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)をご覧ください。

<!----HONumber=AcomDC_0307_2016-->