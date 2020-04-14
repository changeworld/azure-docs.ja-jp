---
title: Azure ファイル共有への移行
description: Azure ファイル共有への移行について説明し、移行ガイドを紹介します。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247318"
---
# <a name="migrate-to-azure-file-shares"></a>Azure ファイル共有への移行

この記事では、Azure ファイル共有への移行の基本的な側面を取り上げます。

移行に関する基本事項のほか、個人に合わせた既存の移行ガイドも紹介します。 これらの移行ガイドは、ファイルを Azure ファイル共有に移動するのに役立ちます。また、現在データが配置されている場所、および移行先として計画しているデプロイ モデル (クラウドのみまたはハイブリッド) ごとに整理されています。

## <a name="migration-basics"></a>移行の基本

Azure では、複数の異なる種類のクラウド ストレージを使用できます。 お使いのデータに適した Azure ストレージ オプションを判断することが、Azure へのファイル移行の基本です。

Azure ファイル共有は、汎用ファイル データに適しています。 あらゆるものに、オンプレミス SMB または NFS 共有を使用します。 [Azure File Sync](storage-sync-files-planning.md) を使用すると、必要に応じて、複数の Azure ファイル共有のコンテンツを、複数のオンプレミス Windows サーバーにキャッシュできます。

オンプレミス サーバーで現在実行されているアプリケーションがあるときは、アプリケーションに応じて、Azure ファイル共有にファイルを格納することが適している場合があります。 アプリケーションをリフトして Azure で実行し、Azure ファイル共有を共有ストレージとして使用できます。 このシナリオでは、[Azure Disk](../../virtual-machines/windows/managed-disks-overview.md) を考慮することもできます。 データへの SMB またはマシン ローカル アクセスや共有アクセスに依存しない、クラウド生まれのアプリケーションについては、多くの場合、[Azure BLOB](../blobs/storage-blobs-overview.md) などのオブジェクト ストレージを選択することをお勧めします。

移行で重要なのは、ファイルを現在の保存場所から Azure に移行するときに、適用できるファイルの忠実性をすべてキャプチャすることです。 適切な Azure ストレージを選択するためのサポートは、Azure ストレージ オプションで忠実度がどの程度サポートされ、シナリオでどのくらい必要とされているか、という側面でもあります。 従来、汎用ファイル データが依存しているのは、ファイルのメタデータです。 おそらく、アプリケーション データではありません。 ファイルの基本的なコンポーネントは 2 つあります。

- **データ ストリーム**: ファイルのデータ ストリームにはファイル コンテンツが格納されます。
- **ファイル メタデータ**: ファイル メタデータには、サブコンポーネントが複数あります。
   * ファイル属性: 読み取り専用など。
   * ファイルのアクセス許可: "*NTFS アクセス許可*" または "*ファイルとフォルダーの ACL*" と呼ばれます。
   * タイムスタンプ: 特に "*作成*" タイムスタンプ、"*最終更新日時*" タイムスタンプ。
   * 代替データ ストリーム: 標準以外のプロパティをより多く格納するための領域。

したがって、移行中のファイルの忠実性は、移行元にある適用可能なすべてのファイル情報を保存する機能、移行ツールを使用してそれを転送する機能、および移行先ストレージにそれを保存する機能として定義できます。

移行をできるだけ円滑に進めるために、[ニーズに合った最適なコピー ツール](#migration-toolbox)を特定し、ストレージ ターゲットをソースと一致させます。

前述の情報を考慮すると、Azure の汎用ファイルのターゲット ストレージは [Azureファイル共有](storage-files-introduction.md)であることが明確になります。 Azure BLOB のオブジェクト ストレージと比べ、Azure ファイル共有では、ファイルにファイル メタデータをネイティブに保存できます。

また、Azure ファイル共有では、ファイルおよびフォルダー階層も保持されます。 追加として:
* NTFS アクセス許可は、オンプレミスと同じように、ファイルおよびフォルダーに保存できます。
* AD ユーザー (または Azure AD DS ユーザー) は、Azure ファイル共有にネイティブにアクセスできます。 
    これらのユーザーは、現在の ID を使用して、共有のアクセス許可およびファイルとフォルダーの ACL に基づいてアクセスを取得します。 ユーザーがオンプレミスのファイル共有に接続するときの動作と、それほど違いはありません。
*  現時点で Azure ファイル共有のファイルに保存できないファイルの忠実性の主な側面が、代替データ ストリームです。
   これは、Azure File Sync が関与しているときはオンプレミスで保持されます。

* [Azure ファイル共有の AD 認証の詳細情報](storage-files-identity-auth-active-directory-enable.md)
* [Azure ファイル共有の Azure Active Directory Domain Services (AAD DS) 認証](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>移行ガイド

次の表に詳細な移行ガイドを示します。

操作方法は次のとおりです。
1. ファイルが現在保存されているソースシステムの行を探します。
2. Azure File Sync を使用してオンプレミスで 1 つ以上の Azure ファイル共有のコンテンツをキャッシュするハイブリッド デプロイを対象とするか、クラウドで Azure ファイル共有を直接使用するかを決定します。 ご自身の決定が示されているターゲット列を選択します。
3. ソースとターゲットが交差するテーブル セルに、使用可能な移行シナリオが示されています。 そのいずれかを選択してリンクをクリックすると、詳細な移行ガイドに直接移動します。

シナリオにリンクが設定されていない場合は、公開されている移行ガイドがまだありません。 この表を定期的にチェックして、更新の有無をご確認ください。 新しいガイドは、入手可能になった時点で公開されます。

| source | ターゲット: </br>ハイブリッド デプロイ | ターゲット: </br>クラウドのみのデプロイ |
|:---|:--|:--|
| | ツールの組み合わせ:| ツールの組み合わせ: |
| Windows Server 2012 R2 以降 | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync と DataBox](storage-sync-offline-data-transfer.md)</li><li>記憶域移行サービスと Azure File Sync</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync と DataBox</li><li>記憶域移行サービスと Azure File Sync</li><li>RoboCopy</li></ul> |
| Windows Server 2012 以前 | <ul><li>Azure File Sync と DataBox</li><li>記憶域移行サービスと Azure File Sync</li></ul> | <ul><li>記憶域移行サービスと Azure File Sync</li><li>RoboCopy</li></ul> |
| ネットワーク接続ストレージ (NAS) | <ul><li>[Azure File Sync と RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux/Samba | <ul><li>[RoboCopy と Azure File Sync](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| StorSimple 8100/8600 | <ul><li>[Azure File Sync と 8020 仮想アプライアンス](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>移行ツールボックス

### <a name="file-copy-tools"></a>ファイル コピー ツール

Microsoft および Microsoft 以外のファイル コピー ツールがいくつか用意されています。 移行シナリオで適切なファイル コピー ツールを選択するには、次の 3 つの基本的な質問について検討する必要があります。

* コピー ツールによって、指定されたファイル コピーのソースとターゲットの場所がサポートされていますか。 
    * ソース保存場所およびターゲット保存場所との間の、ネットワーク パスまたは使用可能なプロトコル (REST/SMB/NFS など) はサポートされていますか。
* コピー ツールによって、ソース/ターゲットの場所でサポートされている、必要なファイルの忠実性は保持されますか。 場合によっては、ターゲット ストレージで、ソースと同じ忠実性がサポートされていないことがありますが、 ターゲット ストレージがニーズを満たしているという判断は既に下しているため、あとはコピー ツールが、ターゲット ファイルの忠実性機能を対応させるだけです。
* コピー ツールを移行戦略に適合させるための機能、そのコピー ツールにありますか。 
    * たとえば、ダウンタイムを最小化できるオプションが用意されているかどうかを検討します。 良い質問の例としては、ユーザーが頻繁にアクセスする同じ場所で、このコピーを複数回実行できるかどうか、などがあります。 これにより、ダウンタイムを大幅に削減できます。 これを、ソースの変更が停止したときにのみコピーを開始できる状況と比較し、完全なコピーが保証されるようにしてください。

次の表は、Microsoft ツールと、Azure ファイル共有との現時点での適合性を分類したものです。

| 推奨 | ツール | Azure ファイル共有のサポート | ファイルの忠実性の保持 |
| :-: | :-- | :---- | :---- |
|![はい、推奨されます。](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | サポートされています。 Azure ファイル共有は、ネットワーク ドライブとしてマウントできます。 | 完全な忠実性* |
|![はい、推奨されます。](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Azure ファイル共有にネイティブに統合されます。 | 完全な忠実性* |
|![はい、推奨されます。](media/storage-files-migration-overview/circle-green-checkmark.png)| 記憶域移行サービス (SMS) | 間接的にサポートされています。 Azure ファイル共有は、SMS ターゲット サーバーでネットワーク ドライブとしてマウントできます。 | 完全な忠実性* |
|![完全にはお勧めできません。](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure DataBox | サポートされています。 | メタデータがコピーされません。 [Azure File Sync と組み合わせて使用できます](storage-sync-offline-data-transfer.md)。 |
|![推奨されません。](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | サポートされています。 | メタデータがコピーされません。 |
|![推奨されません。](media/storage-files-migration-overview/circle-red-x.png)| Azure ストレージ エクスプローラー | サポートされています。 | メタデータがコピーされません。 |
|![推奨されません。](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | サポートされています。 | メタデータがコピーされません。 |
|||||

" *\* 完全な忠実性: Azure ファイル共有の機能を満たすか上回る。* "

### <a name="migration-helper-tools"></a>移行ヘルパー ツール

このセクションでは、移行の計画と実行に役立つツールを紹介します。

* **RoboCopy、Microsoft Corporation 製**

    ファイルの移行に最適なコピー ツールの 1 つ。Microsoft Windows に付属しています。 このツールにはオプションが多数含まれているため、主要な [RoboCopy ドキュメント](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) が役に立ちます。

* **TreeSize、JAM Software GmbH 製**

    Azure File Sync は、主に、項目数 (ファイルとフォルダー) に基づいてスケーリングされるため、TiB の合計とともに少なくなります。 このツールを使用すると、Windows Server ボリューム上のファイルとフォルダーの数が決まります。 さらに、これは、[Azure File Sync デプロイ](storage-sync-files-deployment-guide.md)の前、パースペクティブを作成するときに使用できますが、デプロイの後も、クラウドの階層化が行われている環境で、項目数だけでなく、サーバーキャッシュが最も使用されているディレクトリを確認したいときに使用できます。
    このツール (テスト バージョン 4.4.1) は、クラウドで階層化されたファイルと互換性があります。 通常の操作中、階層化されたファイルが、このツールによって再度呼び出されることはありません。


## <a name="next-steps"></a>次のステップ

1. 目指している Azure ファイル共有 (クラウドのみまたはハイブリッド) のデプロイに適したプランを作成します。
2. 使用可能な移行ガイドの一覧を確認して、Azure ファイル共有のご自身のソースとデプロイに合った詳細なガイドを見つけます。

この記事で説明されている Azure Files テクノロジの詳細については、以下を参照してください。

* [Azure ファイル共有の概要](storage-files-introduction.md)
* [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
* [Azure File Sync: クラウドを使った階層化](storage-sync-cloud-tiering.md)
