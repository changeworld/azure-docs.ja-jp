<properties 
	pageTitle="Azure Media Services の概念 | Microsoft Azure" 
	description="このトピックでは、Azure Media Services の概念の概要を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako"/>

#Azure Media Services の概念 

このトピックでは、Media Services の最も重要な概念の概要を説明します。

##<a id="assets"></a>資産とストレージ

###資産

[資産](https://msdn.microsoft.com/library/azure/hh974277.aspx)には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。デジタル ファイルが資産にアップロードされた後は、Media Services エンコードおよびストリーミング ワークフローで使用できます。

資産は Azure ストレージ アカウント内の BLOB コンテナーにマップされ、資産内のファイルは BLOB としてそのコンテナーに格納されます。

アップロードして資産内に格納するメディア コンテンツを特定する場合は、次の考慮事項に注意してください。

- 資産には、メディア コンテンツの一意のインスタンスを 1 つだけ含めてください。たとえば、TV ドラマ、映画、または広告の単一の編集です。
- 資産には、オーディオビジュアル ファイルの複数の演奏または編集を含めないでください。不適切な使い方の一例として、複数の TV ドラマおよび広告、または単一の製作物からの複数のカメラ アングルを 1 つの資産に格納しようと試みることが挙げられます。オーディオビジュアル ファイルの複数の演奏または編集を 1 つの資産に格納すると、ワークフローの後半でエンコード ジョブの送信や、資産の配信のストリーミングおよびセキュリティ保護を行うことが困難になるおそれがあります。

###資産ファイル 
[資産ファイル](https://msdn.microsoft.com/library/azure/hh974275.aspx)は、BLOB コンテナーに保存された実際のビデオまたはオーディオ ファイルを表します。資産ファイルは常に資産に関連付けられ、資産には 1 つまたは複数のファイルが含まれます。資産ファイル オブジェクトが blob コンテナー内のデジタル ファイルに関連付けられていないと、Media Services のエンコーダー タスクは失敗します。

**AssetFile** インスタンスと実際のメディア ファイルは、別々の 2 つのオブジェクトです。AssetFile インスタンスには、メディア ファイルに関するメタデータが含まれており、メディア ファイルには実際のメディア コンテンツが含まれています。

Media Services によって生成された BLOB コンテナーの内容を変更する場合は、必ず Media Service API を使用してください。

###資産暗号化オプション

アップロードするコンテンツの種類に応じて、Media Services によって提供されるさまざまな暗号化オプションから選択できます。

**None** – 暗号化は使用されません。これが既定値です。このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。

プログレッシブ ダウンロードを使用して MP4 を配信する場合は、このオプションを使用してコンテンツをアップロードします。

**StorageEncrypted** – AES 256 ビット暗号化を使用してクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードすると、コンテンツが保存時に暗号化された状態で格納されます。ストレージの暗号化で保護された資産は、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに置かれます。その後、新しい出力資産として再度アップロードする前に必要に応じて再度暗号化されます。ストレージの暗号化の主な使用事例としては、高品質の入力メディア ファイルをディスクに保存するときに強力な暗号化を使用してセキュリティを保護する場合が挙げられます。

ストレージで暗号化された資産を配信するためには、資産の配信ポリシーを構成して、コンテンツの配信方法を Media Services に指示する必要があります。資産をストリーミングするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシー (AES、PlayReady、暗号化なしなど) を使用してコンテンツをストリーミングする必要があります。

**CommonEncryptionProtected** - このオプションを使用するのは、共通暗号化または PlayReady DRM を使用して既に暗号化および保護されているコンテンツ (たとえば、PlayReady DRM で保護されているスムーズ ストリーミング) をアップロードする場合です。

**EnvelopeEncryptedProtected** – このオプションを使用するのは、AES (Advanced Encryption Standard) で暗号化された HLS (HTTP ライブ ストリーミング) を保護または (既に保護されている HLS を) アップロードする場合です。AES で既に暗号化された HLS をアップロードする場合、HLS は Transform Manager によって暗号化されている必要があることに注意してください。

###アクセス ポリシー 

[アクセス ポリシー](https://msdn.microsoft.com/library/azure/hh974297.aspx)は、資産へのアクセス許可 (読み取り、書き込み、一覧表示など) とアクセス期間を定義します。通常は、資産に格納されたファイルへのアクセスに用いるロケーターに、AccessPolicy オブジェクトを渡します。


###BLOB コンテナー

BLOB コンテナーは、BLOB のセットをグループ化します。BLOB コンテナーは、アクセス制御のための境界点として Media Services で使用され、Shared Access Signature (SAS) ロケーターとして資産上で使用されます。Azure Storage アカウントに含めることができる BLOB コンテナー数は無制限です。また、1 つのコンテナーに保存できる BLOB の数も無制限です。

>[AZURE.NOTE]Media Services によって生成された BLOB コンテナーの内容を変更する場合は、必ず Media Service API を使用してください。

###<a id="locators"></a>ロケーター

[ロケーター](https://msdn.microsoft.com/library/azure/hh974308.aspx)は、資産に含まれているファイルにアクセスするためのエントリ ポイントになります。アクセス ポリシーは、指定された資産に対してクライアントが保持するアクセス許可およびアクセス許可の期間を定義するために使用されます。ロケーターはアクセス ポリシーに対して多対 1 の関係を持つことができるので、同じアクセス許可とアクセス期間の設定を使用しながら、複数のロケーターが複数のクライアントに対して異なる開始時間や接続の種類を提供できます。ただし、Azure Storage サービスで設定されている共有アクセス ポリシーの制限により、特定の資産に対して、5 つを超える一意のロケーターを一度に関連付けることはできません。

Media Services では、2 種類のロケーターがサポートされています。OnDemandOrigin ロケーターはメディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) またはメディアのプログレッシブ ダウンロードに使用し、SAS URL ロケーターは Azure Storage へのメディア ファイルのアップロードまたは Azure Storage からのダウンロードに使用します。

一覧表示のアクセス許可 (AccessPermissions.List) は、OrDemandOrigin ロケーターを作成するときには使用しないでください。

###ストレージ アカウント

Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。Media Service アカウントに、1 つまたは複数のストレージ アカウントを関連付けることができます。アカウントに格納できるコンテナーの数は、ストレージ アカウントあたりのコンテナーの合計サイズが 500 TB 未満である限り無制限です。Media Services が提供する SDK レベルのツールを使用すると、複数のストレージ アカウントを管理すると共に、これらのアカウントへのアップロード中にメトリックまたはランダム配布に基づいて資産の配布を負荷分散できます。詳細については、「[Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx) の操作」をご覧ください。

##ジョブとタスク

[ジョブ](https://msdn.microsoft.com/library/azure/hh974289.aspx)は、通常、1 つのオーディオ/ビデオ プレゼンテーションを処理 (インデックス作成やエンコードなど) するために使用されます。複数のビデオを処理する場合は、エンコードするビデオごとにジョブを作成します。

ジョブには、実行する処理に関するメタデータが含まれます。ジョブ内の複数の[タスク](https://msdn.microsoft.com/library/azure/hh974286.aspx)は、1 つのタスクの出力アセットを次のタスクの入力アセットとして指定した場合、連結できます。ジョブ内の複数のタスクは、1 つのタスクの出力資産を次のタスクの入力資産として指定した場合、連結できます。この方法では、1 つのジョブにメディア表現に必要なすべての処理を含めることができます。

##<a id="encoding"></a>エンコード

Azure Media Services には、クラウド内のメディア エンコーディングに使用できる複数のオプションが用意されています。

Media Services を使い始める場合、コーデックとファイル形式の違いを理解することが重要です。コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

[動的パッケージ化](media-services-dynamic-packaging-overview.md)機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[How to Scale On-Demand Streaming Reserved Unit (オンデマンド ストリーミング占有ユニットの規模変更方法)](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

Media Services は次のオンデマンド エンコーダーをサポートしてます。これらについてはこの記事で説明します。

- [メディア エンコーダー スタンダード](media-services-encode-asset.md#media-encoder-standard)
- [メディア エンコーダー Premium ワークフロー](media-services-encode-asset.md#media-encoder-premium-workflow)

サポートさえるエンコーダーについては、「[エンコーダー](media-services-encode-asset.md)」をご覧ください


##ライブ ストリーミング

Azure Media Services では、チャネルは、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。チャネルは、次の 2 つの方法のいずれかでライブ入力ストリームを受信します。

- オンプレミスのライブ エンコーダーは、マルチ ビットレート RTMP またはスムーズ ストリーミング (Fragmented MP4) をチャネルに送信します。マルチ ビットレートのスムーズ ストリーミングを出力するライブ エンコーダーとして、Elemental、Envivio、Cisco を使用できます。Adobe Flash Live、Telestream Wirecast、Tricaster トランスコーダーは、RTMP を出力するライブ エンコーダーです。取り込んだストリームは、追加の処理なしでチャネルを通過します。Media Services は、要求に応じて、ストリームを顧客に配信します。

- RTP (MPEG-TS)、RTMP、スムーズ ストリーミング (Fragmented MP4) のいずれかの形式のシングル ビットレート ストリームは、Media Services による Live Encoding が有効なチャネルに送信されます。次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードがチャネルで実行されます。Media Services は、要求に応じて、ストリームを顧客に配信します。

###チャネル

Media Services においてライブ ストリーミング コンテンツの処理を担うのが[チャネル](https://msdn.microsoft.com/library/azure/dn783458.aspx)です。チャネルは入力エンドポイントであり、その取り込み URL をライブ トランスコーダーに対して指定します。チャネルは、ライブ トランスコーダーからライブ入力ストリームを受け取り、1 つまたは複数の StreamingEndpoint を介してストリーミングできる状態にします。また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、チャネルはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します

チャネル作成時に取り込み URL とプレビュー URL を取得できます。これらの URL を取得するには、チャネルが開始済み状態である必要はありません。ライブ トランスコーダーからチャネルへのデータのプッシュを開始する準備ができたら、チャネルを開始する必要があります。ライブ トランスコーダーがデータの取り込みを開始した後、ストリームをプレビューできます。

各 Media Services アカウントには、複数のチャネル、複数のプログラム、複数 StreamingEndpoints を含めることができます。帯域幅とセキュリティのニーズに応じて、StreamingEndpoint サービスを 1 つまたは複数のチャネル専用にすることができます。StreamingEndpoint は、どのチャネルからでもプルできます。


###プログラム

ライブ ストリームでのセグメントの公開と保存は、[プログラム](https://msdn.microsoft.com/library/azure/dn783463.aspx)を使用して制御します。プログラムはチャネルによって管理されます。チャネルとプログラムの関係は、従来のメディアとよく似ています。チャネルが絶えずコンテンツのストリームを配信するのに対し、プログラムは、そのチャネル上で決まった時間に生じるイベントです。録画されたコンテンツの保持時間は、プログラムの **ArchiveWindowLength** プロパティで設定できます。この値は、最小 5 分から最大 25 時間までの範囲で設定できます。

クライアントが現在のライブ位置からさかのぼってシークできる最長時間も ArchiveWindowLength によって決まります。プログラムの放送は、指定された期間継続しますが、ArchiveWindowLength を過ぎたコンテンツは絶えず破棄されていきます。さらに、このプロパティの値によって、クライアント マニフェストが肥大した場合の最大サイズも決まります。

各プログラムは資産に関連付けられています。プログラムを公開するには、関連付けられた資産のロケーターを作成する必要があります。このロケーターを作成すると、ストリーミング URL を構築してクライアントに提供できます。

チャネルは、最大 3 つの同時実行プログラムをサポートするため、同じ受信ストリームのアーカイブを複数作成できます。これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。たとえば、ビジネス要件によって 1 つのプログラムの 6 時間分をアーカイブする一方、最後の 10 分間のみをブロードキャストする場合があります。これを実現するには、2 つの同時実行プログラムを作成する必要があります。1 つのプログラムは 6 時間分のイベントをアーカイブするように設定しますが、プログラムは発行されません。もう 1 つのプログラムは 10 分間のアーカイブを行うように設定します。このプログラムは発行されます。


詳細については、次を参照してください。

- [Azure Media Services を使用して Live Encoding の実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)
- [オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する](media-services-live-streaming-with-onprem-encoders.md)
- [クォータと制限](media-services-quotas-and-limitations.md)。

##コンテンツの保護

###動的な暗号化

Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。Media Services では、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) と、PlayReady や Widevine DRM を使用する共通暗号化 (CENC) 使用して、動的に暗号化したコンテンツを配信できます。Media Services では、承認されたクライアントに AES キーと PlayReady ライセンスを配信するためのサービスも提供しています。

現時点では、以下のストリーミング形式を暗号化できます。HLS、MPEG DASH、およびスムーズ ストリーミング。HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

Media Services で資産を暗号化する場合は、暗号化キー (CommonEncryption か EnvelopeEncryption) を資産に関連付ける必要があります。また、キーの承認ポリシーを構成する必要があります。

ストレージで暗号化された資産をストリーミングするには、資産の配信方法を指定するように資産の配信ポリシーを構成する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、エンベロープ暗号化 (AES を使用) か共通暗号化 (PlayReady または Widevine を使用) でコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。


###トークン制限

コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、IP 制限) を指定できます。トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。Media Services では、Simple Web Tokens (SWT) 形式と JSON Web Token (JWT) 形式のトークンがサポートされます。Media Services では、Secure Token Services は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。トークンが有効であり、なおかつトークンに含まれる要求とキー (またはライセンス) に対して構成されている要求とが一致した場合、Media Services キー配信サービスは、キー (またはライセンス) をクライアントに返します。

トークン制限ポリシーを構成する際は、プライマリ検証キー、発行者、対象ユーザーの各パラメーターを指定する必要があります。プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

詳細については、次の記事を参照してください。

[コンテンツの保護に関するページ](media-services-content-protection-overview.md) [AES 128 での保護に関するページ](media-services-protect-with-aes128.md) [DRM での保護に関するページ](media-services-protect-with-drm.md)

##配信

###<a id="dynamic_packaging"></a>動的パッケージ

Media Services を使用する際、中間ファイルをアダプティブ ビットレート MP4 セットにエンコードして、その後、[動的パッケージ](media-services-dynamic-packaging-overview.md)を使用して目的の形式に変換することをお勧めします。


###ストリーミング エンドポイント

StreamingEndpoint は、ストリーミング サービスを表し、コンテンツをクライアント プレーヤー アプリケーションに直接配信するか、Content Delivery Network (CDN) を介して配信できます (Azure Media Services で Azure CDN 統合が提供されるようになりました)。 StreamingEndpoint サービスからの送信ストリームには、ライブ ストリームまたは Media Services アカウントのオンデマンド ビデオ資産を使用します。さらに、スケール単位 (ストリーミング ユニットとも呼ばれます) を調整することによって、帯域幅で増え続けるニーズを処理する StreamingEndpoint サービスの容量を制御できます。実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。スケール ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、動的パッケージ化機能などの追加機能を利用できるようになります。

動的パッケージや動的暗号化の使用をお勧めします。これらの機能を使用するには、ストリーミング元となるエンドポイントに、少なくとも 1 つのストリーミング ユニットが必要です。詳細については、「[ストリーミング エンドポイントの拡張](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

既定では、Media Services アカウントに最大 2 つのストリーミング エンドポイントを追加できます。上限の拡大を要求するには、「[クォータと制限](media-services-quotas-and-limitations.md)」をご覧ください。

StreamingEndpoint が実行状態の場合のみ課金されます。

###資産の配信ポリシー

Media Services におけるコンテンツ配信ワークフローの手順の 1 つは、ストリームする[資産の配信ポリシー](https://msdn.microsoft.com/library/azure/dn799055.aspx)を構成することです。資産の配信ポリシーは、資産を配信する方法、つまりどのストリーミング プロトコルで資産を動的パッケージングするか (例 : MPEG DASH、HLS、スムーズ ストリーミング、またはすべて)、資産を動的に暗号化するかどうか、どの暗号化方法を使用するか (エンベロープ暗号化または共通暗号化) を Media Services に示します。

ストレージで暗号化した資産をストリーミングするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシーを使用してコンテンツをストリーミングする必要があります。たとえば、AES (Advanced Encryption Standard) 暗号化キーを使用して暗号化された資産を配信するには、ポリシーの種類を DynamicEnvelopeEncryption に設定します。ストレージ暗号化を解除して資産を平文でストリームするには、ポリシーの種類を NoDynamicEncryption に設定します。

###プログレッシブ ダウンロード

プログレッシブ ダウンロードでは、ファイル全体がダウンロードされる前に、メディアの再生を開始できます。プログレッシブ ダウンロードでダウンロードできるのは、MP4 ファイルのみです。

プログレッシブ ダウンロードで使用したい場合は、暗号化された資産の暗号化を解除する必要があります。

ユーザーにプログレッシブ ダウンロード URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。ロケーターを作成すると、資産への基本パスが提供されます。それに MP4 ファイルの名前を付加する必要があります。次に例を示します。

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###ストリーミング URL

コンテンツをクライアントにストリーミングします。ユーザーにストリーミング URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。ロケーターを作成すると、ストリーミングするコンテンツが含まれているアセットの基本パスが提供されます。ただし、このコンテンツをストリーミングするためには、このパスをさらに変更する必要があります。ストリーミング マニフェスト ファイルの完全な URL を構築するには、ロケーターの Path の値とマニフェスト (filename.ism) ファイルの名前を連結する必要があります。その後、/Manifest と適切な形式 (必要な場合) をロケーターのパスに付加します。

SSL 接続経由でコンテンツのストリーミングもできます。そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。

SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。ストリーミング URL の基になるストリーミング エンドポイントの作成日が 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。"origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。URL が旧形式である場合、SSL ストリーミングに対応するためには、新しいストリーミング エンドポイントを作成してください。SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいて作成された URL を使用する必要があります。

次の一覧では、さまざまなストリーミング形式について説明し、例を示します。

- スムーズ ストリーミング

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP ライブ ストリーミング (HLS) V4

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP ライブ ストリーミング (HLS) V3

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (Adobe PrimeTime/Access ライセンスのみ)

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->