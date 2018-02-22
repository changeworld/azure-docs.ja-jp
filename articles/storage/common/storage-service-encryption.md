---
title: "保存データ向け Azure Storage Service Encryption | Microsoft Docs"
description: "データを保存するときにサービス側で Azure Blob Storage を暗号化し、データを取得するときに復号化するには、Azure Storage Service Encryption 機能を使用します。"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: d04752c92218d17b4e90bb03a2ed2ac5a0a11b93
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption for Data at Rest
Azure Storage Service Encryption (SSE) for Data at Rest は、データの安全性を保護して組織のセキュリティおよびコンプライアンス要件を満たすのに役立ちます。 この機能を使用すると、Azure Storage はストレージに保存する前にデータを自動的に暗号化し、取得する前に復号化します。 暗号化、復号化、キーの管理は、ユーザーにはまったく意識されずに行われます。

以下のセクションでは、Storage Service Encryption 機能の使用方法についての詳細なガイダンス、およびサポートされるシナリオとユーザー エクスペリエンスについて説明します。

## <a name="overview"></a>概要
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 アプリケーションと Azure の間で送信されるデータは、 [クライアント側暗号化](../storage-client-side-encryption.md)、HTTPS、または SMB 3.0 使用して保護できます。 Storage Service Encryption は保存時の暗号化を提供し、暗号化、復号化、キー管理を完全に透過的な方法で処理します。 現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によってすべてのデータを暗号化します。

SSE は、Azure Storage に書き込まれるデータを書き込みの時点で暗号化する機能で、Azure Blob、File、Table、および Queue Storage で使用できます。 これは、以下に利用できます。

* Standard Storage: Blob、File、Table、Queue Storage の汎用ストレージ アカウントと、lob ストレージ アカウント
* Premium Storage 
* 既定ではクラシックと ARM のストレージ アカウントに対して有効 
* すべての冗長性レベル (LRS、ZRS、GRS、RA-GRS)
* Azure Resource Manager ストレージ アカウント (クラシック以外) 
* すべてのリージョン

詳細については、よく寄せられる質問に関するセクションを参照してください。

ストレージ アカウントについてストレージ サービスの暗号化の設定を表示するには、[Azure Portal](https://portal.azure.com) にログインしてストレージ アカウントを選択します。 [設定] ブレードで [BLOB サービス] セクション (次のスクリーンショットを参照) を探し、[暗号化] をクリックします。

![暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption/image1.png)
<br/>"*図 1: Blob service の SSE を有効にする (手順 1)*"

![暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption/image3.png)
<br/>"*図 2: File Service の SSE を有効にする (手順 1)*"

[暗号化] をクリックした後、Storage Service Encryption を有効または無効にできます。

![暗号化プロパティが表示されたポータルのスクリーンショット](./media/storage-service-encryption/image2.png)
<br/>*図 3: Blob Service と File Service の SSE を有効にする (手順 2)*

## <a name="encryption-scenarios"></a>暗号化のシナリオ
Storage Service Encryption は、ストレージ アカウント レベルで有効にされます。 既定では、すべてのサービスに対して有効です。 次のカスタマー シナリオがサポートされています。

* クラシックと Resource Manager の両方のアカウントにおける Blob、File、Table、Queue Storage の暗号化。

SSE には次の制限事項があります。

* 既存のデータ - SSE は暗号化有効化後に新しく作成されたデータだけを暗号化します。 たとえば、暗号化を有効にしないで新しい Resource Manager ストレージ アカウントを作成し、BLOB またはアーカイブ済み VHD をそのストレージ アカウントにアップロードした後、SSE を有効にした場合、それらの BLOB は、再作成またはコピーしない限り、暗号化されません。
* Marketplace のサポート - [Azure Portal](https://portal.azure.com)、PowerShell、Azure CLI を使用して Marketplace で作成された VM の暗号化を有効にします。 VHD の基本イメージは暗号化されません。ただし、VM が稼働した後の書き込みはすべて暗号化されます。

## <a name="getting-started"></a>Getting Started (概要)
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>手順 1: [新しいストレージ アカウントを作成する](../storage-create-storage-account.md)
### <a name="step-2-verify-encryption"></a>手順 2: 暗号化を確認する
[Azure Portal](https://portal.azure.com) を使用して、暗号化を確認できます。

> [!NOTE]
> ストレージ アカウントの Storage Service Encryption をプログラムを使用して確認する場合は、[Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)、[.NET 用 Storage Resource Provider クライアント ライブラリ](https://msdn.microsoft.com/library/azure/mt131037.aspx)、[Azure PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure CLI](../storage-azure-cli.md) を使用できます。
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>手順 3: ストレージ アカウントにデータをコピーする
SSE がストレージ アカウントに対して有効になると、そのストレージ アカウントに書き込まれたデータはすべて暗号化されます。 このストレージ アカウントに既にあるデータは、再度書き込まれるまで暗号化されません。 コンテナーから別のコンテナーにデータをコピーし、前のデータが暗号化されるようにすることができます。 そのためには、次のツールを使用できます。 これは File、Table、および Queue Storage でも同様です。

#### <a name="using-azcopy"></a>AzCopy の使用
AzCopy は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure BLOB、File、Table の記憶域との間でデータをコピーするために設計された Windows コマンドライン ユーティリティです。 これを使用して、あるストレージ アカウントの BLOB またはファイルを SSE を有効にした別のストレージ アカウントにコピーできます。 

詳細については、「 [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」を参照してください。

#### <a name="using-smb"></a>SMB の使用
Azure Files は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 オンプレミスまたは Azure のクライアントからファイル共有をマウントできます。 マウントすると、Robocopy などのツールを使用して、ファイルを Azure ファイル共有にコピーすることができます。 詳細については、[Windows で Azure ファイル共有をマウントする方法](../files/storage-how-to-use-files-windows.md)に関するページおよび [Linux で Azure ファイル共有をマウントする方法](../files/storage-how-to-use-files-linux.md)に関するページを参照してください。


#### <a name="using-the-storage-client-libraries"></a>ストレージ クライアント ライブラリの使用
.NET、C++、Java、Android、Node.js、PHP、Python、Ruby など、豊富なストレージ クライアント ライブラリを使用して、Blob Storage 間またはストレージ アカウント間で BLOB またはファイル データをコピーできます。

詳細については、「 [.NET を使用して Azure Blob Storage を使用する](../blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。

#### <a name="using-a-storage-explorer"></a>ストレージ エクスプローラーの使用
ストレージ エクスプローラーを使用すると、ストレージ アカウントの作成、データのアップロードとダウンロード、BLOB の内容の表示、ディレクトリ内の移動を行うことができます。 これらのいずれかを使用して、暗号化を有効にした状態でストレージ アカウントに BLOB をアップロードできます。 一部のストレージ エクスプローラーでは、ストレージ アカウントの別のコンテナーまたは SSE が有効になっている新しいストレージ アカウントに既存の BLOB ストレージからデータをコピーすることもできます。

詳細については、「 [Azure ストレージ エクスプローラー](../storage-explorers.md)」を参照してください。

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>手順 4: 暗号化されたデータの状態を照会する
ストレージ クライアント ライブラリの更新バージョンがデプロイされ、オブジェクトの状態を照会して暗号化されているかどうかを判別できるようになります。 これは現在、Blob Storage でのみ使用可能です。 File Storage のサポートはロードマップに含まれています。 

それまでの間は、Azure Portal で [Get Account Properties](https://msdn.microsoft.com/library/azure/mt163553.aspx) を呼び出して、ストレージ アカウントの暗号化が有効であることを確認したり、ストレージ アカウントのプロパティを表示したりできます。

## <a name="encryption-and-decryption-workflow"></a>暗号化と復号化のワークフロー
ここでは、暗号化/復号化のワークフローを簡単に説明します。

* 暗号化は、ストレージ アカウントに対して有効です。
* ユーザーが (PUT Blob、PUT Block、PUT Page、PUT File などにより) Blob Storage または File Storage に新しいデータを書き込むときには、使用可能なブロック暗号化のなかで最も強力なものの 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使用して、すべての書き込みが暗号化されます。
* ユーザーがデータにアクセスすると (GET Blob など)、データは自動的に復号化されてユーザーに返されます。
* 暗号化が無効になっている場合は、新しい書き込みは暗号化されず、既存の暗号化されたデータはユーザーが書き込み直すまで暗号化された状態のままになります。 暗号化が有効になっている間、Blob Storage または File Storage への書き込みが暗号化されます。 ユーザーがストレージ アカウントの暗号化の有効/無効を切り替えても、データの状態は変わりません。
* すべての暗号化キーは、Microsoft によって保管、暗号化、管理されます。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Storage Service Encryption for Data at Rest に関してよく寄せられる質問
**Q: 既存のクラシック ストレージ アカウントがあります。このアカウントで SSE を有効にできますか?**

A: SSE は、既定ですべてのストレージ アカウント (クラシックと Resource Manager のストレージ アカウント) に対して有効になります。

**Q: クラシック ストレージ アカウントのデータを暗号化するにはどうすればよいですか?**

A: 既定で有効にされている暗号化を使用して、新しいデータはストレージ サービスによって自動的に暗号化されます。 

また、新しい Resource Manager ストレージ アカウントを作成し、[AzCopy](storage-use-azcopy.md) を使用して既存のクラシック ストレージ アカウントから新しく作成した Resource Manager ストレージ アカウントにすべてのデータをコピーすることもできます。 

また、クラシック ストレージ アカウントを Resource Manager ストレージ アカウントに移行するように選ぶこともできます。 この操作は瞬時に行われ、アカウントの種類が変更されますが、既存のデータに影響はありません。 書き込まれた新しいデータは、暗号化を有効にした後にのみ暗号化されます。 詳しくは、「[Platform Supported Migration of IaaS Resources from Classic to Resource Manager (プラットフォームでサポートされているクラシックから Resource Manager への IaaS リソースの移行)](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)」をご覧ください。 これは Blob Service と File Service でのみサポートされていることに注意してください。

**Q: 既存の Resource Manager ストレージ アカウントがあります。このアカウントで SSE を有効にできますか?**

A: SSE は、既定で既存のすべての Resource Manager ストレージ アカウントで有効になります。 これは、Blob、Table、File、および Queue Storage に対してサポートされます。 

**Q: 既存の Resource Manager ストレージ アカウント内に現在あるデータを暗号化できますか?**

A: 既定ですべてのストレージ アカウント (クラシックと Resource Manager のストレージ アカウント) に対して有効にされている SSE を使用します。 ただし、既にあるデータは暗号化されません。 既存のデータを暗号化するには、別の名前または別のコンテナーにコピーしてから、暗号化されていないバージョンを削除します。 

**Q: Premium Storage を使用しています。SSE を使用できますか?**

A: はい。SSE は Standard Storage と Premium Storage の両方でサポートされています。  Premium Storage は File Service ではサポートされていません。

**Q: SSE を有効にして新しいストレージ アカウントを作成し、そのストレージ アカウントを使用して新しい VM を作成した場合、その VM は暗号化されますか?**

A: はい。 新しいストレージ アカウントを使用して作成したディスクは、SSE を有効にした後で作成するのであれば暗号化されます。 VM が Azure Marketplace を使用して作成された場合、VHD の基本イメージは暗号化されません。ただし、VM が稼働した後の書き込みはすべて暗号化されます。

**Q: Azure PowerShell および Azure CLI を使用して、SSE を有効にした新しいストレージ アカウントを作成できますか?**

A: SSE は、既定でストレージ アカウント (クラシックと Resource Manager のアカウント) の作成時に有効にされます。 Azure PowerShell と Azure CLI の両方を使用して、アカウントのプロパティを確認できます。

**Q: SSE を有効にすると、Azure Storage の料金はどれくらい増えますか?**

A: 追加コストはかかりません。

**Q: だれが暗号化キーを管理するのですか?**

A: キーは Microsoft によって管理されます。

**Q: 独自の暗号化キーを使用できますか?**

A: ユーザーが独自の暗号化キーを使用できる機能は現在作業中です。

**Q: 暗号化キーへのアクセスを取り消すことはできますか?**

A: 現時点ではできません。キーは、Microsoft によって完全に管理されます。

**Q: 新しいストレージ アカウントを作成すると、SSE は既定で有効になりますか?**

A: はい、Microsoft 管理キーを使用する SSE は、既定ですべてのストレージ アカウント (Azure Resource Manager とクラシック ストレージ アカウント) に対して有効になります。 これは、すべてのサービス (Blob、Table、Queue、File Storage) に対しても有効になっています。

**Q: この機能は Azure Disk Encryption とどのように違うのですか?**

A: この機能は、Azure Blob Storage のデータの暗号化に使用されます。 Azure Disk Encryption は、IaaS VM の OS ディスクとデータ ディスクの暗号化に使用されます。 詳細については、 [Storage セキュリティ ガイド](../storage-security-guide.md)に関する記事を参照してください。

**Q: SSE が有効な場合に、ディスクで Azure Disk Encryption を有効にするとどうなりますか?**

A: これはシームレスに機能します。 データは両方の方法で暗号化されます。

**Q: ストレージ アカウントを、geo 冗長レプリケートされるように設定してあります。SSE が有効な場合、冗長コピーも暗号化されますか?**

A: はい。ストレージ アカウントのすべてのコピーが暗号化され、すべての冗長オプション (ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)) がサポートされます。

**Q: ストレージ アカウントで暗号化を無効にできません。**

A: 暗号化は既定で有効になっており、ストレージ アカウントの暗号化を無効にするプロビジョニングはありません。 

**Q: SSE を使用できるのは特定のリージョンだけですか?**

A: すべてのサービスのすべてのリージョンで SSE を使用できます。 

**Q: 問題やフィードバックの連絡先はどこですか?**

A: Storage Service Encryption に関する問題は、 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。

## <a name="next-steps"></a>次の手順
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳細については、 [Storage セキュリティ ガイド](../storage-security-guide.md)に関する記事を参照してください。
