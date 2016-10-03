<properties
	pageTitle="保存データ向け Azure Storage Service Encryption | Microsoft Azure"
	description="データを保存するときにサービス側で Azure Blob Storage を暗号化し、データを取得するときに復号化するには、Azure Storage Service Encryption 機能を使用します。"
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="robinsh"/>

# Azure Storage Service Encryption for Data at Rest

Azure Storage Service Encryption (SSE) for Data at Rest は、データの安全性を保護して組織のセキュリティおよびコンプライアンス要件を満たすのに役立ちます。この機能を使用すると、Azure Storage はストレージに保存する前にデータを自動的に暗号化し、取得する前に復号化します。暗号化、復号化、キーの管理は、ユーザーにはまったく意識されずに行われます。

以下のセクションでは、Storage Service Encryption 機能の使用方法についての詳細なガイダンス、およびサポートされるシナリオとユーザー エクスペリエンスについて説明します。

## 概要


Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。アプリケーションと Azure の間で送信されるデータは、[クライアント側暗号化](storage-client-side-encryption.md)、HTTPS、または SMB 3.0 使用して保護できます。Storage Service Encryption は保存時の暗号化を提供し、暗号化、復号化、キー管理を完全に透過的な方法で処理します。現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によってすべてのデータを暗号化します。

SSE は、Azure Storage に書き込まれるときにデータを暗号化することで機能し、ブロック BLOB、ページ BLOB、追加 BLOB に使用できます。これは、以下に利用できます。

-   汎用ストレージ アカウントと Blob Storage アカウント
-   Standard Storage と Premium Storage
-   すべての冗長性レベル (LRS、ZRS、GRS、RA-GRS)
-   Azure Resource Manager ストレージ アカウント (クラシック以外)
-   すべてのリージョン

次のスクリーンショットは、[Azure ポータル](https://azure.portal.com)での Storage Service Encryption の設定画面です。この画面では、[暗号化] をクリックして続行します。

![暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption/image1.png)

[暗号化] をクリックした後、Storage Service Encryption を有効または無効にできます。

![暗号化プロパティが表示されたポータルのスクリーンショット](./media/storage-service-encryption/image2.png)

##暗号化のシナリオ

Storage Service Encryption は、ストレージ アカウント レベルで有効にできます。次のカスタマー シナリオがサポートされています。

-   ブロック BLOB、追加 BLOB、ページ BLOB の暗号化。

-   オンプレミスから Azure に移動したアーカイブ済み VHD とテンプレートの暗号化。

-   VHD を使用して作成された IaaS VM の基になっている OS とデータ ディスクの暗号化。

SSE には次の制限事項があります。

-   クラシック ストレージ アカウントの暗号化はサポートされていません。

-   Resource Manager ストレージ アカウントに移行されたクラシック ストレージ アカウントの暗号化はサポートされていません。

-   既存のデータ - SSE は暗号化有効化後に新しく作成されたデータだけを暗号化します。たとえば、暗号化を有効にしないで新しい Resource Manager ストレージ アカウントを作成し、BLOB またはアーカイブ済み VHD をそのストレージ アカウントにアップロードした後、SSE を有効にした場合、それらの BLOB は、再作成またはコピーしない限り、暗号化されません。

-   Marketplace のサポート - [Azure ポータル](https://portal.azure.com)、PowerShell、および Azure CLI を使用して Marketplace で作成された VM の暗号化を有効にします。VHD の基本イメージは暗号化されません。ただし、VM が稼働した後の書き込みはすべて暗号化されます。

-   テーブル、キュー、ファイルのデータは暗号化されません。

##Getting Started (概要)

###手順 1: [新しいストレージ アカウントを作成する](storage-create-storage-account.md)

###手順 2: 暗号化を有効にする

[Azure ポータル](https://portal.azure.com)を使用して、暗号化を有効にできます。

> [AZURE.NOTE] ストレージ アカウントの Storage Service Encryption をプログラムを使用して有効または無効にする場合は、[Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)、[.NET 用 Storage Resource Provider クライアント ライブラリ](https://msdn.microsoft.com/library/azure/mt131037.aspx)、[Azure PowerShell](../powershell-install-configure.md)、または [Azure CLI](storage-azure-cli.md) を使用します。

###手順 3: ストレージ アカウントにデータをコピーする

ストレージ アカウントで SSE を有効にして、そのストレージ アカウントに BLOB を書き込むと、BLOB が暗号化されます。このストレージ アカウントに既にある BLOB は、再度書き込まれるまで暗号化されません。あるストレージ アカウントからのデータを SSE 暗号化が有効になっている別のストレージ アカウントにコピーしたり、あるコンテナーから別のコンテナーに BLOB をコピーしたりして、以前のデータを確実に暗号化することができます。そのためには、次のツールを使用できます。

#### AzCopy の使用

AzCopy は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure BLOB、File、Table の記憶域との間でデータをコピーするために設計された Windows コマンドライン ユーティリティです。これを使用して、あるストレージ アカウントの BLOB を SSE を有効にした別のストレージ アカウントにコピーできます。

詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」を参照してください。

#### ストレージ クライアント ライブラリの使用

.NET、C++、Java、Android、Node.js、PHP、Python、Ruby など、豊富なストレージ クライアント ライブラリを使用して、Blob Storage 間またはストレージ アカウント間で BLOB データをコピーできます。

詳細については、「[.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)」を参照してください。

#### ストレージ エクスプローラーの使用

ストレージ エクスプローラーを使用すると、ストレージ アカウントの作成、データのアップロードとダウンロード、BLOB の内容の表示、ディレクトリ内の移動を行うことができます。これらのいずれかを使用して、暗号化を有効にした状態でストレージ アカウントに BLOB をアップロードできます。一部のストレージ エクスプローラーでは、ストレージ アカウントの別のコンテナーまたは SSE が有効になっている新しいストレージ アカウントに既存の BLOB ストレージからデータをコピーすることもできます。

詳細については、「[Azure ストレージ エクスプローラー](storage-explorers.md)」を参照してください。

###手順 4: 暗号化されたデータの状態を照会する

ストレージ クライアント ライブラリの更新バージョンがデプロイされ、オブジェクトの状態を照会して暗号化されているかどうかを判別できるようになります。例については、今後このドキュメントに追加される予定です。

それまでの間は、Azure ポータルで [Get Account Properties](https://msdn.microsoft.com/library/azure/mt163553.aspx) を呼び出して、ストレージ アカウントの暗号化が有効であることを確認したり、ストレージ アカウントのプロパティを表示したりできます。

##暗号化と復号化のワークフロー

ここでは、暗号化/復号化のワークフローを簡単に説明します。

-   ユーザーがストレージ アカウントで暗号化を有効にします。

-   ユーザーが Blob Storage に新しいデータを書き込むと (PUT Blob、PUT Block、PUT Page など)、使用可能なものの中で最も強力なブロック暗号化の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使用して、すべての書き込みが暗号化されます。

-   ユーザーがデータにアクセスすると (GET Blob など)、データは自動的に復号化されてユーザーに返されます。

-   暗号化が無効になっている場合は、新しい書き込みは暗号化されず、既存の暗号化されたデータはユーザーが書き込み直すまで暗号化された状態のままになります。暗号化が有効になっている間、Blob Storage への書き込みは暗号化されます。ユーザーがストレージ アカウントの暗号化の有効/無効を切り替えても、データの状態は変わりません。

-   すべての暗号化キーは、Microsoft によって保管、暗号化、管理されます。

##Storage Service Encryption for Data at Rest に関してよく寄せられる質問

**Q: 既存のクラシック ストレージ アカウントがあります。このアカウントで SSE を有効にできますか?**

A: いいえ。SSE は Resource Manager ストレージ アカウントでのみサポートされます。

**Q: クラシック ストレージ アカウントのデータを暗号化するにはどうすればよいですか?**

A: 新しい Resource Manager ストレージ アカウントを作成し、[AzCopy](storage-use-azcopy.md) を使用して既存のクラシック ストレージ アカウントから新しく作成した Resource Manager ストレージ アカウントにデータをコピーします。

**Q: 既存の Resource Manager ストレージ アカウントがあります。このアカウントで SSE を有効にできますか?**

A: はい。ただし、新しく書き込まれた BLOB のみが暗号化されます。前に戻って、既に存在するデータを暗号化することはありません。

**Q: 既存の Resource Manager ストレージ アカウント内に現在あるデータを暗号化できますか?**

A: Resource Manager ストレージ アカウントではいつであっても SSE を有効にすることができます。ただし、既にある BLOB は暗号化されません。これらの BLOB を暗号化するには、別の名前または別のコンテナーにコピーしてから、暗号化されていないバージョンを削除します。

**Q: Premium Storage を使用しています。SSE を使用できますか?**

A: はい。SSE は Standard Storage と Premium Storage の両方でサポートされています。

**Q: 新しいストレージ アカウントを作成して SSE を有効にし、そのストレージ アカウントを使用して新しい VM を作成した場合、その VM は暗号化されますか?**

A: はい。新しいストレージ アカウントを使用して作成したディスクは、SSE を有効にした後で作成するのであれば暗号化されます。VM が Azure Marketplace を使用して作成された場合、VHD の基本イメージは暗号化されません。ただし、VM が稼働した後の書き込みはすべて暗号化されます。

**Q: Azure PowerShell および Azure CLI を使用して、SSE を有効にした新しいストレージ アカウントを作成できますか?**

A: はい。

**Q: SSE を有効にすると、Azure Storage の料金はどれくらい増えますか?**

A: 追加コストはかかりません。

**Q: だれが暗号化キーを管理するのですか?**

A: キーは Microsoft によって管理されます。

**Q: 独自の暗号化キーを使用できますか?**

A: ユーザーが独自の暗号化キーを使用できる機能は現在作業中です。

**Q: 暗号化キーへのアクセスを取り消すことはできますか?**

A: 現時点ではできません。キーは、Microsoft によって完全に管理されます。

**Q: 新しいストレージ アカウントを作成すると、SSE は既定で有効になりますか?**

A: SSE は既定では有効になりません。Azure ポータルを使用して有効にできます。Storage Resource Provider REST API を使用してプログラムでこの機能を有効にすることもできます。

**Q: この機能は Azure ドライブ暗号化とどのように違うのですか?**

A: この機能は、Azure Blob Storage のデータの暗号化に使用されます。Azure Disk Encryption は、IaaS VM の OS ディスクとデータ ディスクの暗号化に使用されます。詳細については、[Storage セキュリティ ガイド](storage-security-guide.md)に関する記事を参照してください。

**Q: SSE を有効にした後、ディスクで Azure Disk Encryption を有効にするとどうなりますか?**

A: これはシームレスに機能します。データは両方の方法で暗号化されます。

**Q: ストレージ アカウントを、geo 冗長レプリケートされるように設定してあります。SSE を有効にした場合、冗長コピーも暗号化されますか?**

A: はい。ストレージ アカウントのすべてのコピーが暗号化され、すべての冗長オプション (ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)) がサポートされます。

**Q: ストレージ アカウントで暗号化を有効にできません。**

A: それは Resource Manager ストレージ アカウントですか。 クラシック ストレージ アカウントはサポートされていません。

**Q: SSE を使用できるのは特定のリージョンだけですか?**

A: SSE はすべてのリージョンでご利用いただけます。

**Q: 問題やフィードバックの連絡先はどこですか?**

A: Storage Service Encryption に関する問題は、[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。

##次のステップ

Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。詳細については、[Storage セキュリティ ガイド](storage-security-guide.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0921_2016-->