---
title: 顧客の音声リソースをバックアップおよび復旧する方法
titleSuffix: Azure Cognitive Services
description: Custom Speech と Custom Voice を使用してサービスの停止に備える方法について説明します。
services: cognitive-services
author: masaki-itagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.custom: ignite-fall-2021
ms.openlocfilehash: fbcea71a200ec65e2c179f98230e7a87775ebd80
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706537"
---
# <a name="back-up-and-recover-speech-customer-resources"></a>顧客の音声リソースをバックアップおよび復旧する

Speech サービスは[さまざまなリージョンで使用できます](./regions.md)。 サービス サブスクリプション キーは、1 つのリージョンに関連付けられています。 キーを取得するときは、データ、モデル、およびデプロイが存在する特定のリージョンを選択します。

カスタマイズされた音声モデル、カスタム音声フォント、話者認識の音声プロファイルなど、顧客が作成したデータ資産のデータセットも、**サービスによってデプロイされたリージョン内でのみ使用できます**。 このような資産は次のとおりです。

**カスタム音声**
-   トレーニング オーディオ データまたはトレーニング テキスト データ
-   テスト オーディオ データまたはテスト テキスト データ
-   カスタマイズされた音声モデル
-   ログ データ

**Custom Voice**
-   トレーニング オーディオ データまたはトレーニング テキスト データ
-   テスト オーディオ データまたはテスト テキスト データ
-   カスタム音声フォント

**Speaker Recognition**
- 話者登録オーディオ
- 話者の声紋

お客様によっては、既定のエンドポイントを使用して音声合成用にオーディオまたは標準の音声を文字起こしする場合と、カスタマイズ用に資産を作成する場合があります。

これらの資産は、リポジトリ自体によって定期的かつ自動的にバックアップされるので、リージョンが使用できなくなった場合に **データの損失は発生しません**。 ただし、リージョンの停止が発生した場合にサービスの継続性を確保するための手順を実行する必要があります。

## <a name="how-to-monitor-service-availability"></a>サービスの可用性を監視する方法

既定のエンドポイントを使用する場合は、エラーを監視するようにクライアント コードを構成し、エラーが解決しない場合は、サービス サブスクリプションがある別のリージョンに再接続できるように準備する必要があります。

エラーを監視するようにクライアントを構成するには、次の手順に従います。

1.  [ドキュメントにある、リージョンで使用可能なエンドポイントのリスト](./rest-speech-to-text.md)を参照してください。
2.  プライマリ リージョンと 1 つ以上のセカンダリ リージョンまたはバックアップ リージョンをリストから選択します。
3. Azure portal で、リージョンごとに Speech Service リソースを作成します。
    -  特定のクォータを設定した場合は、バックアップ リージョンにも同じクォータを設定することを検討してください。 「[Speech サービスのクォータと制限](./speech-services-quotas-and-limits.md)」の詳細を参照してください。

4.  各リージョンには独自の STS トークン サービスがあることに注意してください。 プライマリ リージョンとバックアップ リージョンについては、クライアント構成ファイルによって次のことが認識されている必要があります。
    -  リージョンの Speech サービス エンドポイント
    -  [リージョンのサブスクリプション キーとリージョン コード](./rest-speech-to-text.md)

5.  接続エラー (通常は接続タイムアウトとサービス利用不能エラー) を監視するようにコードを構成します。 [GitHub: リージョンを切り替える候補を表示するためのサンプルの追加](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965)に関する C# のサンプル コードを参照してください。

    1.  ネットワークには一時的なエラーが発生するため、単一の接続の問題が発生した場合は、再試行することをお勧めします。
    2.  永続化のために、トラフィックを新しい STS トークン サービスおよび Speech サービス エンドポイントにリダイレクトします (テキスト読み上げについては、[GitHub: TTS パブリック音声切り替えリージョン](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880)に関するサンプル コードを参照してください)。

この種類の使用法では、リージョンの障害からの復旧は非常に低コストで瞬時に行われる可能性があります。 必要なのは、クライアント側でこの機能を開発することだけです。 オーディオ ストリームのバックアップがないと想定した場合に発生するデータ損失は、最小限に抑えられます。

## <a name="custom-endpoint-recovery"></a>カスタム エンドポイントの回復

1 つのリージョン内のデータ資産、モデル、またはデプロイを、他のリージョンで表示したりアクセスしたりすることはできません。

既定のエンドポイントで使用されるものと同じ手順に従って、メインおよびとセカンダリ リージョンの両方に Speech サービス リソースを作成する必要があります。

### <a name="custom-speech"></a>Custom Speech

Custom Speech サービスでは、自動フェールオーバーはサポートされていません。 クライアント コードに手動または自動フェールオーバーを実装するには、次の手順に従うことをお勧めします。 これらの手順では、セカンダリ リージョンでカスタム モデルをレプリケートします。 この準備により、プライマリ リージョンで障害が発生したときにクライアント コードをセカンダリ リージョンに切り替えることができます。

1.  1 つのメイン リージョン (プライマリ) にカスタム モデルを作成します。
2.  [モデル コピー API](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) を実行して、準備されたすべてのリージョン (セカンダリ) にカスタム モデルをレプリケートします。
3.  Speech Studio にアクセスして、コピーしたモデルを読み込み、セカンダリ リージョンに新しいエンドポイントを作成します。 「[Custom Speech モデルのトレーニングとデプロイ](./how-to-custom-speech-train-model.md)」で、新しいモデルをデプロイする方法を参照してください。
    -  特定のクォータを設定した場合は、バックアップ リージョンにも同じクォータを設定することを検討してください。 「[Speech サービスのクォータと制限](./speech-services-quotas-and-limits.md)」の詳細を参照してください。
4.  既定のエンドポイントの使用法と同じように、永続的なエラーでフェールオーバーするようにクライアントを構成します。

クライアント コードでは、プライマリ リージョンでのデプロイ済みのモデルの可用性を監視し、プライマリに障害が発生したときに、そのオーディオ トラフィックをセカンダリ リージョンにリダイレクトすることができます。 リアルタイム フェールオーバーを必要としない場合でも、次の手順に従って手動フェールオーバーを準備できます。

#### <a name="offline-failover"></a>オフライン フェールオーバー

リアルタイム フェールオーバーを必要としない場合は、データをインポートし、後でセカンダリ リージョンにモデルを作成してデプロイすることができます。ただし、これらのタスクの完了には時間がかかることを理解しておく必要があります。

#### <a name="failover-time-requirements"></a>フェールオーバー時間の要件

このセクションでは、タイミングに関する一般的なガイダンスを示します。 この時間は、[代表的なテスト データ セット](https://github.com/microsoft/Cognitive-Custom-Speech-Service)を使用してオフライン フェールオーバーを推定するために記録されています。

-   新しいリージョンへのデータのアップロード: **15 分**
-   音響または言語モデルの作成: **6 時間 (データ ボリュームによって異なります)**
-   モデル評価: **30 分**
-   エンドポイントのデプロイ: **10 分**
-   モデル コピー API の呼び出し: **10 分**
-   クライアント コードの再構成とデプロイ: **クライアント システムによって異なります**

運用モデルでは、プライマリ リージョンとセカンダリ リージョンのキーを作成することをお勧めします。

### <a name="custom-voice"></a>Custom Voice

Custom Voice では自動フェールオーバーはサポートされていません。 次の 2 つのオプションを使用して、リアルタイムの合成エラーを処理します。

**オプション 1: 同じリージョン内のパブリック音声にフェールオーバーします。**

カスタム音声のリアルタイム合成に失敗した場合は、パブリック音声にフェールオーバーします (クライアントのサンプル コード: [GitHub: パブリック音声へのカスタム音声のオーバー](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899))。

[使用可能なパブリック音声](./language-support.md#neural-voices)を確認します。 別の音声または別のリージョンにフェールオーバーする場合は、上記のサンプル コードを変更することもできます。

**オプション 2: 別のリージョンのカスタム音声にフェールオーバーします。**

1.  カスタム音声を作成し、1 つのメイン リージョン (プライマリ) にデプロイします。
2.  [Speech Studio](https://speech.microsoft.com) で、カスタム音声モデルを別のリージョン (セカンダリ リージョン) にコピーします。
3.  Speech Studio に移動し、セカンダリ リージョンの Speech リソースに切り替えます。 コピーしたモデルを読み込み、新しいエンドポイントを作成します。
    -   通常、音声モデルのデプロイは **3 分で** 終了します。
    -   注: 追加のエンドポイントには追加料金がかかります。 [モデル ホスティングの価格については、こちらをご覧ください](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

4.  セカンダリ リージョンにフェールオーバーするようにクライアントを構成します。 [GitHub: セカンダリ リージョンへのカスタム音声フェールオーバー](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920)に関する C# のサンプル コードを参照してください。

### <a name="speaker-recognition"></a>Speaker Recognition

Speaker Recognition では、[Azure のペアになっているリージョン](../../best-practices-availability-paired-regions.md)を使用して、自動的にフェールオーバー操作を行います。 話者の登録と声紋は定期的にバックアップされ、データの損失を防ぎ、障害が発生した場合に使用することができます。

障害が発生すると、Speaker Recognition サービスはペアになっているリージョンに自動的にフェールオーバーし、メイン リージョンがオンラインに戻るまで、バックアップされたデータを使用して要求の処理を続行します。