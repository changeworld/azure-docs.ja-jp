<properties
   pageTitle="Azure Storage のセキュリティの概要 | Microsoft Azure"
   description="Azure Storage は、持続性、可用性、スケーラビリティで顧客のニーズに応える最新のアプリケーションのためのクラウド ストレージ ソリューションです。この記事では、Azure Storage で使用できる Azure のコア セキュリティ機能の概要について説明します。"
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Azure Storage のセキュリティの概要

Azure Storage は、持続性、可用性、スケーラビリティで顧客のニーズに応える最新のアプリケーションのためのクラウド ストレージ ソリューションです。Azure Storage には、包括的な一連のセキュリティ機能が用意されています。

- ストレージ アカウントを、ロールベースのアクセス制御と Azure Active Directory を使用して保護できます。
- アプリケーションと Azure の間で送信されるデータを、クライアント側暗号化、HTTPS、または SMB 3.0 使用して保護できます。
- Storage Service Encryption を使用して Azure Storage に書き込むときに、データが自動的に暗号化されるように設定することができます。
- 仮想マシンに使用する OS とデータ ディスクを、Azure Disk Encryption を使用して暗号化されるように設定できます。
- Shared Access Signature を使用して、Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権を付与できます。

この記事では、Azure Storage で使用できる各コア セキュリティ機能の概要について説明します。各機能の詳細について説明する記事へのリンクが用意されているため、さらに詳しく学習できます。

Azure Storage でのセキュリティの詳細については、「[Azure Storage セキュリティ ガイド](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md)」を参照してください。

この記事では、以下の主要機能について扱います。

- ロールベースのアクセス制御
- ストレージ アカウント キーの管理
- ストレージ オブジェクトへの委任されたアクセス
- 転送中の暗号化
- 保存時の暗号化/Storage Service Encryption
- Storage Analytics

## ロール ベースのアクセス制御 (RBAC)

ロールベースのアクセス制御 (RBAC) を使用して、ストレージ アカウントをセキュリティで保護できます。各 Azure サブスクリプションは、1 つの Azure Active Directory (AD) テナントと関連付けられます。そのディレクトリに登録されたユーザー、グループ、およびアプリケーションのみが、Azure サブスクリプションでリソースを管理できます。これらのアクセス権は、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てることによって付与します。

RBAC を使用して、Azure Storage アカウントの管理操作へのアクセスを許可することができます。RBAC を使用して、以下の操作をどのユーザーに許可するかを制御します。

- ストレージ アカウントの管理
- ストレージ アカウント キーの読み取り、およびこれらのキーを使用した BLOB、キュー、テーブル、ファイルへのアクセス
- ストレージ キーの再生成

詳細情報:

- [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

## ストレージ アカウント キーの管理

ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。一方のアクセス キーでストレージ アカウントに接続したまま、もう一方のアクセス キーを再生成できます。

ストレージ アカウント キーは、ストレージ アカウント名と共に、ストレージ アカウントに保存されているデータ オブジェクト (BLOB、テーブル内のエンティティ、キュー メッセージ、Azure ファイル共有上のファイルなど) へのアクセスに使用できます。RBAC を使用して、データ オブジェクト自体へのアクセスを制御するストレージ アカウント キーへのアクセスを制御します。

詳細情報:

- [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)

## ストレージ オブジェクトへの委任されたアクセス

Shared Access Signature (SAS) は、セキュリティ トークンを含む文字列です。セキュリティ トークンを URI にアタッチすることで、ストレージ オブジェクトへのアクセスを委任し、アクセス許可やアクセスの日時の範囲などの制限を指定することができます。

ストレージ アカウント キーの付与は、ストレージ王国の鍵を共有するようなものです。完全なアクセス権が付与されます。SAS を使用すると、一定時間、必要なアクセス許可のみをクライアントに付与できます。そのための方法は次のとおりです。

- BLOB、コンテナー、キュー メッセージ、ファイル、テーブルに対してアクセス権を付与できます。テーブルの場合、実際にテーブル内のエンティティの範囲に対してアクセス許可を付与するには、ユーザーにアクセス権を持たせるパーティションと行キーの範囲を指定します。
- SAS を使用して行われる要求を、Azure の外部の特定 IP アドレスまたは IP アドレスの範囲に制限できます。
- 要求に特定のプロトコル (HTTPS または HTTP/HTTPS) を使用することを必須にできます。つまり、HTTPS トラフィックのみを許可する場合、必須のプロトコルを HTTPS のみに設定することで、HTTP トラフィックをブロックすることができます。

詳細情報:

- [SAS モデルについて](../storage/storage-dotnet-shared-access-signature-part-1.md)

## 転送中の暗号化
転送中の暗号化は、ネットワーク間でデータを転送するときにデータを保護するメカニズムです。Azure Storage では、以下を使用してデータをセキュリティ保護できます。

- トランスポートレベルの暗号化 (Azure Storage の内外にデータを転送する場合の HTTPS など)
- ワイヤ暗号化 (Azure ファイル共有の SMB 3.0 暗号化など)
- クライアント側の暗号化 (Storage にデータを転送する前にデータを暗号化し、Storage からデータを転送した後にデータを復号化します)

### トランスポートレベルの暗号化

Azure Storage データのセキュリティを確保するために、クライアントと Azure Storage 間でデータを暗号化することができます。REST API を呼び出すときや、ストレージ内のオブジェクトにアクセスするときに、[HTTPS を有効に](../app-service-web/web-sites-configure-ssl-certificate.md)できます。また、[共有アクセス署名](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS) を使用して、Azure Storage オブジェクトへのアクセスを委任することもできます。SAS にはオプションのパラメーター "Protocol" があり、このパラメーターで、SAS の利用時に HTTPS プロトコルのみを使用できることを指定できます。これにより、SAS トークンを使用してリンクを送信するユーザーはすべて、指定のプロトコルを使用することになります。

### ファイル共有へのアクセスのワイヤ暗号化

SMB 3.0 は暗号化をサポートしており、Windows Server 2012 R2、Windows 8、Windows 8.1、および Windows 10 での使用が可能なので、リージョンにまたがるアクセスや、デスクトップへのアクセスも許可できます。

Azure ファイル共有は、Linux 仮想マシンで使用できます。Linux SMB クライアントはまだ暗号化をサポートしていないため、アクセスは Azure リージョン内でのみ許可されます。Linux の暗号化サポートは検討中です。暗号化が追加されると、Linux で Azure ファイル共有にアクセスした場合に、Windows と同じネットワーク レベルの暗号化を利用できるようになります。

詳細情報:

- [Linux で Azure File Storage を使用する方法](../storage/storage-how-to-use-files-linux.md)
- [Windows で Azure File Storage を使用する](../storage/storage-dotnet-how-to-use-files.md)
- [Inside Azure File Storage (Azure File Storage の内部)](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### クライアント側の暗号化

クライアント側の暗号化により、クライアント アプリケーションと Azure Storage 間でデータが転送されるときに、データをセキュリティで保護できます。データは暗号化されてから、Azure Storage に転送されます。Azure Storage からデータを取得するときは、クライアント側で受け取った後にデータが暗号化されます。データが転送時に暗号化される場合でも、HTTPS を使用することで、データの整合性に影響を及ぼすネットワーク エラーを軽減することができます。

詳細情報:

- [Client-Side Encryption for Microsoft Azure Storage (Microsoft Azure Storage のクライアント側の暗号化)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Cloud security controls series: Encrypting Data in Transit (クラウドのセキュリティ管理シリーズ: 転送中のデータの暗号化)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## 保存時の暗号化

Azure には、“保存時の“ データの暗号化を提供する機能が 3 つあります。

- Storage Service Encryption
- クライアント側の暗号化
- Azure Disk Encryption

### Storage Service Encryption

Storage Service Encryption を使用すると、ストレージ サービスが Azure Storage にデータを書き込むときに自動的に暗号化するように要求できます。Azure Storage からデータを読み取ると、Storage サービスによって復号化されてから、返されます。そのため、データをセキュリティで保護するためにコードを変更したり、アプリケーションにコードを追加したりする必要はありません。

[Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) は、[Azure BLOB Storage](https://azure.microsoft.com/services/storage/blobs/)で使用できます。他の Azure Storage の種類について詳しくは、[File Storage](https://azure.microsoft.com/services/storage/files/)、[ディスク (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/)、[Table Storage](https://azure.microsoft.com/services/storage/tables/)、および[Queue Storage](https://azure.microsoft.com/services/storage/queues/) に関するページをそれぞれ参照してください。

詳細情報:

- [Azure Storage Service Encryption for Data at Rest](../storage/storage-service-encryption.md)

### クライアント側の暗号化

転送中の暗号化について説明した際に、クライアント側の暗号化について触れました。この機能を使用すると、クライアント アプリケーションのデータをプログラムで暗号化してからネットワーク経由で送信し、Azure Storage に書き込むことができます。また、Azure Storage から取得した後にプログラムでデータを復号化することができます。

クライアント側の暗号化には、転送中の暗号化機能だけでなく、保存時の暗号化機能もあります。転送中にデータは暗号化されますが、さらに HTTPS を使用すると組み込みのデータ整合性チェックを利用できます。これによって、データの整合性に影響を及ぼすネットワーク エラーを軽減できます。

詳細情報:

- [Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../storage/storage-client-side-encryption.md)

### Azure Disk Encryption

仮想マシン (VM) 向けの Azure Disk Encryption は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で管理するキーとポリシーを使用して VM ディスク (ブート ディスクとデータ ディスクを含む) を暗号化するソリューションです。組織のセキュリティとコンプライアンスの要件に対処する際に大きな効果を発揮します。

Linux および Windows オペレーティング システムに対して機能します。Key Vault により、ディスク暗号化キーの保護、管理、および監査を行うこともできます。VM ディスク内のすべてのデータは、Azure Storage アカウントでの保存中に、業界標準の暗号化技術を使用して暗号化されます。Windows 向けの Disk Encryption ソリューションのベースは [Microsoft BitLocker ドライブ暗号化](https://technet.microsoft.com/library/cc732774.aspx)であり、Linux 向けソリューションは [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) がベースになっています。

詳細情報:

- [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Windows と Linux IaaS Virtual Machines の Azure Disk Encryption)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Storage Analytics

各ストレージ アカウントで、Azure Storage Analytics を有効にして、ログを実行し、メトリック データを保存することができます。これは、パフォーマンス メトリックの確認や、ストレージ アカウントのパフォーマンスに関するトラブルシューティングに最適なツールです。

Storage Analytics ログで確認できるもう 1 つのデータは、誰かがストレージにアクセスするときに使用される認証方法です。たとえば、Blob Storage では、使用されたのが Shared Access Signature かストレージ アカウント キーか、またはアクセスされた BLOB がパブリックかどうかが確認できます。

詳細情報:

- [Storage Analytics](../storage/storage-analytics.md)
- [Storage Analytics のログの形式](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Azure ポータルでのストレージ アカウントの監視](../storage/storage-monitor-storage-account.md)
- [Azure Storage のメトリックおよびログ、AzCopy、Message Analyzer を使用したエンド ツー エンド トラブルシューティング](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->