---
title: Azure Data Factory Studio のトラブルシューティング
description: Azure Data Factory Studio の問題をトラブルシューティングする方法について説明します。
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663138"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>Azure Data Factory Studio の問題のトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、このサービスのユーザー インターフェイスである [Azure Data Factory Studio](https://adf.azure.com) の一般的なトラブルシューティング方法について説明します。

## <a name="azure-data-factory-studio-fails-to-load"></a>Azure Data Factory Studio の読み込みに失敗する

> [!NOTE]
> Azure Data Factory Studio では、Microsoft Edge と Google Chrome が正式にサポートされています。 他の Web ブラウザーを使用すると、予期しない、またはドキュメントに記載されていない動作が発生する可能性があります。

### <a name="third-party-cookies-blocked"></a>サードパーティの Cookie がブロックされた

Azure Data Factory Studio では、ブラウザーの Cookie を使用してユーザー セッション状態を維持し、対話型の開発と監視のエクスペリエンスを実現します。 シークレット セッションを使用しているか、広告ブロック機能が有効になっていると、ブラウザーでサードパーティの Cookie がブロックされる可能性があります。 サード パーティの Cookie がブロックされていると、ポータルの読み込み時に問題が発生する可能性があります。  空白のページ (https://adf.azure.com/accesstoken.html ) にリダイレクトされたり、サードパーティの Cookie がブロックされていることを示す警告メッセージが表示されたりすることがあります。 この問題を解決するには、次の手順に従って、ブラウザーでサードパーティの Cookie オプションを有効にします。

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>すべての Cookie を許可する

1. ブラウザーで **edge://settings/content/cookies** にアクセスします。
1. **[Cookie データの保存と読み取りをサイトに許可する]** が有効になっていて、 **[サードパーティの Cookie をブロックする]** オプションが無効になっていることを確認します。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="Edge で Cookie をすべて許可する":::
1. Azure Data Factory Studio を更新して、もう一度やり直してください。

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Azure Data Factory Studio のみに Cookie の使用を許可する

すべての Cookie を許可しない場合は、必要に応じて ADF Studio のみを許可することもできます。

1. **edge://settings/content/cookies** にアクセスします。
1. **[許可]** セクションの **[追加]** を選択して、**adf.azure.com** サイトを追加します。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="Edge の許可されたサイトに ADF UX を追加する":::
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>すべての Cookie を許可する

1. ブラウザーで **chrome://settings/cookies** にアクセスします。
1. **[Allow all cookies]\(Cookie をすべて許可する\)** オプションを選択します。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="Chrome で Cookie をすべて許可する":::
1. Azure Data Factory Studio を更新して、もう一度やり直してください。

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Azure Data Factory Studio のみに Cookie の使用を許可する

すべての Cookie を許可しない場合は、必要に応じて ADF Studio のみを許可することもできます。
1. **chrome://settings/cookies** にアクセスします。
1. **[常に Cookie を使用できるサイト]** オプションの下にある **[追加]** を選択します。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="Chrome の許可されたサイトに ADF UX を追加する":::
1. **adf.azure.com** サイトを追加し、 **[すべての Cookie]** オプションをオンにして保存します。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="ADF UX サイトからのすべての Cookie を許可する":::
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Azure Data Factory Studio での接続失敗エラー

**[Test Connection]** \(テスト接続\) や **[プレビュー]** などをクリックした後などに、次のスクリーンショットのように、Azure Data Factory Studio で "接続に失敗しました" というエラーが表示されることがあります。 これは、ローカル コンピューターが ADF サービスに接続できなかったため、操作が失敗したということを意味しています。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="接続できなかった":::

この問題を解決するには、まずお使いのブラウザーで InPrivate ブラウズ モードで同じ操作を試してみます。

それでも動作しない場合は、エラー メッセージから **サーバー名** (この例では **dpnortheurope.svc.datafactory.azure.com**) を見つけて、その **サーバー名** をブラウザーのアドレス バーに直接入力します。 

- ブラウザーに 404 と表示される場合、通常は、お使いのクライアント側には問題はなく、ADF サービス側に問題があることを意味します。 サポート チケットを、エラー メッセージの **アクティビティ ID** と共に提出してください。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="リソースが見つかりません":::

- ブラウザーに 404 と表示されない、またはブラウザーに以下のようなエラーが表示される場合、通常は、クライアント側に問題があることを意味します。 さらにトラブルシューティングを続けます。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="クライアント側のエラー":::

さらにトラブルシューティングを行うには、**コマンド プロンプト** を開き、`nslookup dpnortheurope.svc.datafactory.azure.com` と入力します。 通常、次のような応答があります。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="コマンド応答 1":::

- 通常のドメイン ネーム サービス (DNS) 応答が表示された場合は、ファイアウォール設定について自分のローカルの IT サポートに問い合わせます。  このホスト名への HTTPS 接続がブロックされていないことを確認するようにしてください。 問題が解決しない場合は、エラー メッセージの **アクティビティ ID** と共に ADF にサポート チケットを提出してください。

- 上記の通常の応答とは異なる DNS 応答は、DNS 名を解決する際の DNS サーバーに問題が存在することを意味する場合があります。 DNS サーバーを (たとえば、Google DNS 8.8.8.8 に) 変更することで、このような場合の問題を回避できます。 

- これでも問題が解決しない場合は、さらに `nslookup datafactory.azure.com` と `nslookup azure.com` を試して、自分の DNS 解決がどのレベルで失敗しているかを確認し、トラブルシューティングのためにこの全情報を、自分のローカルの IT サポートまたは自分の ISP に送信できます。 問題が Microsoft 側にあるとされた場合は、エラー メッセージの **アクティビティ ID** と共にサポート チケットを提出してください。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="コマンド応答 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>データセットに表示されるリンクされたサービスの種類に関する警告メッセージの変更

アクティビティであるファイル形式のデータセットを使用し、後で以前使用したものとは異なる種類のリンクされたサービスをアクティビティで示した場合 (ファイル形式から Azure Data Lake Storage Gen2 など)、次の警告メッセージが表示されることがあります。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="警告メッセージ":::

ファイル形式のデータセットは、ファイルベースのすべてのコネクタで使用できます。たとえば、Parquet データセットを Azure BLOB または Azure Data Lake Storage Gen2 で構成できます。 各コネクタにより、アクティビティ上で、別のアプリ モデルを使用して、データ ストアのさまざまなセットに関連する設定がサポートされることにご注意ください。 

ADF 作成 UI では、アクティビティ (Copy、Lookup、GetMetadata、Delete アクティビティなど) でファイル形式のデータセットを使用し、データセットで現在アクティビティで使用している種類とは異なる種類のリンク サービスを示す (ファイル システムから ADLS Gen2 へ切り替えるなど) と、次の警告メッセージが表示されます。 これがクリーンな切り替えであることを確認するために、このデータセットを参照するパイプラインとアクティビティは、ユーザーの合意の下で、新しい種類も使用できるように変更されます。また、新しい種類と互換性のない既存のデータ ストア設定は、適用されなくなると消去されます。

各コネクタに対してサポートされているデータ ストア設定の詳細については、対応するコネクタに関する記事から [コピー アクティビティのプロパティ] に進み、詳細なプロパティ リストを確認してください。 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure Files](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[SFTP](connector-sftp.md) を参照してください。


## <a name="could-not-load-resource-while-opening-pipeline"></a>パイプラインを開いているときに、リソースを読み込めませんでした 

ユーザーが Azure Data Factory Studio を使用してパイプラインにアクセスすると、"リソース 'xxxxxx' を読み込めませんでした" というエラー メッセージが表示されます。  JSON に誤りがないこと、および参照されているリソースが存在することを確認してください。 状態: TypeError: 未定義のプロパティ 'xxxxx' を読み取ることはできません。考えられる原因: TypeError: 未定義のプロパティ 'xxxxxxx' を読み取ることはできません。"

このエラー メッセージのソースは、パイプラインを記述している JSON ファイルです。 これは、お客様が Git 統合を使用しているが、パイプライン JSON ファイルが何らかの理由で破損している場合に発生します。 次に示すように、パイプライン名の左側にエラー (x 付きの赤いドットが付いています) が表示されます。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="パイプライン JSON エラー":::

解決策として、最初に JSON ファイルを修正し、その後で作成ツールを使用してパイプラインを再度開いてください。


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

* [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
* [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
* [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)