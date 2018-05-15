---
title: Azure Media Services の Transform と Job | Microsoft Docs
description: Media Services を使用するときは、ビデオを処理するためのルールまたは仕様を説明する Transform を作成する必要があります。 この記事では、Transform とその使用方法の概要を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transform と Job

## <a name="overview"></a>概要 

Azure Media Services REST API (v3) の最新バージョンには、ビデオのエンコードと分析を行うための新しいテンプレート化されたワークフロー リソースが導入されています。これは**Transform** と呼ばれています。 **Transform** を使用して、ビデオのエンコードまたは分析を行うための一般的なタスクを構成できます。 各 **Transform** は、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します。 

**Transform** オブジェクトはレシピであり、**Job** は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Azure Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。 ビデオの場所は、HTTP(S) URL、SAS URL、ローカルまたは Azure Blob Storage に配置されているファイルのパスを使用して指定できます。 Azure Media Services アカウント内に最大 100 個の Transform を設定でき、これらの Transform に基づいて Job を送信できます。 Azure Event Grid 通知システムに直接的に統合されている通知を使用して、Job 状態変更などのイベントをサブスクライブできます。 

この API は Azure Resource Manager によって実行されるため、Resource Manager テンプレートを使用して、Transform の作成と Media Services アカウントへの配置を行うことができます。 この API でリソース レベルでのロールベースのアクセス制御も設定でき、Transform などの特定のリソースへのアクセスをロックダウンすることもできます。

## <a name="typical-workflow-and-example"></a>一般的なワークフローと例

1. Transform を作成します。 
2. その Transform に基づいて Job を送信します。 
3. Transform を一覧表示します。 
4. 今後この "レシピ" を使用する予定がなければ、Transform を削除します。 

すべてのビデオの最初のフレームをサムネイル画像として抽出するとします。実行する手順は次のようになります。 

1. 処理のルールを定義します。例: ビデオの最初のフレームをサムネイルとして使用する 
2. 次に、サービスへの入力にするビデオごとに、以下の情報をサービスに通知ます。 
    1. ビデオがある場所 
    2. 出力の書き込み先。例: サムネイル画像 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ビデオ ファイルのストリーミング](stream-files-dotnet-quickstart.md)
