---
title: 'Microsoft Genomics: トラブルシューティング ガイド | Microsoft Docs'
titleSuffix: Azure
description: トラブルシューティング戦略の詳細について説明します。
keywords: トラブルシューティング, エラー, デバッグ
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 9bd1690003fd37b6c2edd0f0421cf8d0e74f8cb5
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144178"
---
# <a name="troubleshooting-guide"></a>トラブルシューティング ガイド
この概要では、Microsoft Genomics サービスを使用する際の一般的な問題に対応するための戦略について説明します。 一般的な FAQ については、「[よく寄せられる質問](frequently-asked-questions-genomics.md)」を参照してください。 


## <a name="how-do-i-check-my-job-status"></a>ジョブの状況を確認する方法
次のように、コマンド ラインから `msgen status` を呼び出して、ワークフローの状態を確認できます。 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

必須の引数が 3 つあります。
* URL - API のベース URI
* KEY - Genomics アカウントのアクセス キー。 
* ID - ワークフロー ID

URL と KEY を確認するには、Azure Portal に移動して、Genomics アカウント ページを開きます。 **[管理]** 見出しの下の **[アクセス キー]** を選択します。 ここに、API URL とアクセス キーの両方が表示されます。

また、URL と KEY を直接入力する代わりに、構成ファイルのパスを含めることもできます。 これらの引数をコマンド ラインと構成ファイルに含めた場合、コマンド ラインの引数が優先される点に注意してください。 

`msgen status` を呼び出した後、ワークフローが成功したか、ジョブが失敗した理由を示すわかりやすいメッセージが表示されます。 


## <a name="get-more-information-about-my-workflow-status"></a>ワークフローの状態の詳細情報を取得する

ジョブが成功しなかった理由の詳細情報を取得するには、ワークフロー中に生成されたログ ファイルを調べます。 出力コンテナーには `[youroutputfilename].logs.zip` フォルダーがあります。  このフォルダーを展開すると、次の項目が表示されます。

* outputFileList.txt - ワークフロー中に生成された出力ファイルの一覧
* standarderror.txt - このファイルは空です。
* standardoutput.txt - ワークフローの最上位のログが含まれます。 
* GATK ログ ファイル - `logs` フォルダー内の他のすべてのファイル

`standardoutput.txt` ファイルは、ワークフローの低レベルの情報が含まれているため、ワークフローが成功しなかった理由を特定する場合にお勧めの出発点です。 

## <a name="common-issues-and-how-to-resolve-them"></a>一般的な問題とその解決方法
このセクションでは、一般的な問題とその解決方法について簡単に説明します。

### <a name="fastq-files-are-unmatched"></a>Fastq ファイルが一致しない
Fastq ファイルは、サンプル識別子の末尾の /1 または /2 のみが異なる必要があります。 一致しない FASTQ ファイルを誤って送信した場合、`msgen status` を呼び出すと次のエラー メッセージが表示されることがあります。
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

これを解決するには、ワークフローに送信された fastq ファイルが、実際に一致するセットかどうかを確認します。 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>.bam ファイルのアップロード中にエラーが発生した。 出力 BLOB が既に存在し、上書きオプションが False に設定されていた。
エラー メッセージ `Error uploading .bam file. Output blob already exists and the overwrite option was set to False` が表示される場合、出力フォルダーには既に同じ名前の出力ファイルがあります。  既存の出力ファイルを削除するか、構成ファイルの上書きオプションをオンにします。 次に、ワークフローを再送信します。

### <a name="when-to-contact-microsoft-genomics-support"></a>Microsoft Genomics サポートに問い合わせる場合
次のエラー メッセージが表示される場合、内部エラーが発生したことを示します。 

* `Error locating input files on worker machine`
* `Process management failure`

ワークフローの再送信を試してください。 ジョブのエラーが解決しない場合や、その他の質問がある場合は、Azure Portal の Microsoft Genomics サポートに問い合わせてください。 サポート要求を送信する方法の詳細については、[こちら](file-support-ticket-genomics.md)を参照してください。

## <a name="next-steps"></a>次の手順
この記事では、Microsoft Genomics サービスに関する一般的な問題のトラブルシューティングと解決方法を学びました。 詳細情報とその他の一般的な FAQ については、「[よく寄せられる質問](frequently-asked-questions-genomics.md)」を参照してください。 
