---
title: 'Microsoft Genomics: 一般的な質問 - FAQ | Microsoft Docs'
titleSuffix: Azure
description: Microsoft Genomics についてよく寄せられる質問に回答します。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 56256a6c10ecb0d06dfd6194668b9c32c5540c0e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683902"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: よく寄せられる質問

この記事には、Microsoft Genomics に関連するよく寄せられる質問が記載されています。 Microsoft Genomics サービスの詳細については、「[Microsoft Genomics とは](overview-what-is-genomics.md)」を参照してください。 トラブルシューティングの詳細については、「[Troubleshooting Guide](troubleshooting-guide-genomics.md)」(トラブルシューティング ガイド) を参照してください。 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Microsoft Genomics サービス GATK 4 プロモーションとは何ですか?
2018 カレンダー年の終わりまで、Microsoft Genomics サービスは、GATK4 での 20 WGS 実行を無料で提供しています。 このオファリングに参加するには、[こちら](https://aka.ms/msgatk4)でご登録ください。 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Microsoft Genomics サービス GATK4 プロモーションの実行中に発生する可能性のある一般的な問題
発生する可能性のある一般的なエラーと、推奨される解決方法の一覧を次に示します。

| **メッセージ**                                                                                                                                                                                    | **原因**                                                                                                    | **解決策**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` is not enabled for your account.\(お使いのアカウントでは `gatk4-promo` が有効になっていません。\) 詳細については、 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics を参照してください。                               | Microsoft Genomics サービスをアクティブ化しないで、GATK4 ワークフローを実行しようとしています。       | アカウントをアクティブにするには、[こちら](https://aka.ms/msgatk4)をご覧ください。 試用版の有効期限は 2018 カレンダー年の終わりに切れることにご注意ください。 この日付より後に、プロモーション実行用にアカウントをアクティブにすることはできません。 |
| Thank you for trying `gatk4-promo`.Your trial period has ended.\(`gatk4-promo` をお試しいただきありがとうございます。試用期間は終了しました。\) For more information, https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics\(詳しくは、 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics をご覧ください\)                  | GATK4 の試用版は、カレンダーの年の終わりに有効期限が切れましたが、`gatk4-promo` process_name を呼び出そうとしています。  | process_name パラメーターを `gatk4-promo` ではなく `gatk4` に切り替えます。 これは、公式の gatk4 バージョンであり、このパラメーターを使用する場合はワークフローに課金されます。                                         |
| Thank you for trying `gatk4-promo` You have used all of your allocated runs.\(gatk4-promo をお試しいただきありがとうございます。割り当て済みの実行はすべて使用されました。\) 詳細については、 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics を参照してください。 | GATK4 の 20 プロモーションの実行をすべて正常に送信しました。                               | 新しい gatk4 の実行は、process_name 引数を `gatk4-promo` ではなく `gatk4` に設定して送信してください。 このパラメーターを使用すると、ワークフローに課金されます。                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>GATK4 プロモーションにサインアップしなくても、GATK4 ワークフローを Microsoft Genomics 上で実行できますか?
はい、Microsoft Genomics サービスの config.txt ファイルで、process_name を`gatk4` に指定します。 通常の課金レートで課金され、無料の 20 実行は Microsoft Genomics アカウントに適用されないことにご注意ください。



## <a name="what-is-the-sla-for-microsoft-genomics"></a>Microsoft Genomics の SLA はどうなっていますか。
マイクロソフトは、ワークフロー API 要求を受信するために 99.9% の時間において Microsoft Genomics サービスを利用できることを保証します。 詳細については、[SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/) のページを参照してください。

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Microsoft Genomics の使用量は請求書にどのように表示されますか。
Microsoft Genomics は、ワークフローあたりの処理ギガベースに基づいて課金されます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/genomics/)に関するページをご覧ください。


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>`msgen` クライアントで利用できるすべてのコマンドと引数の一覧はどこで確認できますか。
利用可能なコマンドと引数の完全な一覧は、`msgen help` を実行すると表示されます。 それ以上の引数が提供されていない場合は、`submit`、`list`、`cancel`、`status` のそれぞれについて、利用可能なヘルプ セクションの一覧が表示されます。 特定のコマンドのヘルプを表示するには、「`msgen help command`」と入力します。たとえば、「`msgen help submit`」と入力するとすべての送信オプションの一覧が表示されます。

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>`msgen` クライアントで最もよく使用されるコマンドは何ですか。
最もよく使用されるコマンドは、次のような `msgen` クライアントの引数です。 

 |**コマンド**          |  **フィールドの説明** |
 |:--------------------|:-------------         |
 |`list`               |送信済みジョブの一覧を返します。 引数については、`msgen help list` を参照してください。  |
 |`submit`             |ワークフロー要求をサービスに送信します。 引数については、`msgen help submit` を参照してください。|
 |`status`             |`--workflow-id` で指定されたワークフローの状態を返します。 `msgen help status` も参照してください。 |
 |`cancel`             |`--workflow-id` で指定されたワークフローの処理をキャンセルする要求を送信します。 `msgen help cancel` も参照してください。 |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>`--api-url-base` の値はどこで取得できますか。
Azure Portal に移動して、Genomics アカウント ページを開きます。 **[管理]** 見出しの下の **[アクセス キー]** を選択します。 ここに、API URL とアクセス キーの両方が表示されます。

## <a name="where-do-i-get-the-value-for---access-key"></a>`--access-key` の値はどこで取得できますか。
Azure Portal に移動して、Genomics アカウント ページを開きます。 **[管理]** 見出しの下の **[アクセス キー]** を選択します。 ここに、API URL とアクセス キーの両方が表示されます。

## <a name="why-do-i-need-two-access-keys"></a>2 つのアクセス キーが必要なのはなぜですか。
サービスの使用を中断せずにアクセス キーを更新 (再生成) したい場合は、2 つのアクセス キーが必要です。 たとえば、最初のキーを更新する場合は、すべての新しいワークフローで 2 番目のキーを使用する必要があります。 次に、最初のキーを使用しているワークフローがすべて完了するまで待ってから、最初のキーを更新します。

## <a name="do-you-save-my-storage-account-keys"></a>ストレージ アカウント キーはマイクロソフトで保存されますか。
ストレージ アカウント キーは、入力ファイルの読み取りと出力ファイルの書き込みを行うための Microsoft Genomics サービスの短期的なアクセス トークンの作成に使用されます。 トークンの既定の有効期間は 48 時間です。 トークンの有効期間は、送信コマンドの `-sas/--sas-duration` オプション (値は時間単位) を使用して変更できます。

## <a name="what-genome-references-can-i-use"></a>どのようなゲノム リファレンスを使用できますか。

次のリファレンスがサポートされています。
 |リファレンス              | `-pa/--process-args` の値 |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (alt 分析なし) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>コマンドライン引数はどのような形式の構成ファイルにすればよいですか。 

msgen は、次の形式の構成ファイルを認識します。
* すべてのオプションが、値とキーをコロンで区切ったキー/値のペアで指定されている。
空白は無視されます。
* `#` で始まる行は無視されます。
* 長い形式のコマンド行引数は、先頭のダッシュを削除して単語間のダッシュをアンダースコアに置き換えることにより、キーに変換できます。 いくつかの変換の例を次に示します。

 |コマンドライン引数            | 構成ファイルの行 |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>次の手順

次のリソースを使用して、Microsoft Genomics の使用を開始します。
- Microsoft Genomics サービス経由で最初のワークフローを実行して使用を開始します。 [Microsoft Genomics サービス経由でワークフローを実行する](quickstart-run-genomics-workflow-portal.md)
- Microsoft Genomics サービスで処理する独自のデータを送信する。[ペアリングされた FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [複数の FASTQ または BAM](quickstart-input-multiple.md) 

