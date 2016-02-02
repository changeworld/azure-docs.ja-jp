<properties
	pageTitle="Azure ポータルでストレージ アカウントを作成、管理、削除する方法 | Microsoft Azure"
	description="Azure ポータルで、新しいストレージ アカウントの作成、アカウント アクセス キーの管理、ストレージ アカウントの削除を実行します。Standard および Premium ストレージ アカウントについて説明します。"
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="robinsh"/>


# Azure ストレージ アカウントについて

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## 概要

Azure ストレージ アカウントを使用すると、Azure Storage の Azure BLOB サービス、Queue サービス、Table サービス、ファイル サービスにアクセスできます。ストレージ アカウントは、Azure Storage のデータ オブジェクトに対して一意の名前空間を提供します。既定では、アカウントのデータはアカウント所有者だけが使用できます。

ストレージ アカウントには、次の 2 種類があります。

- Standard ストレージ アカウント。Blob、Table、Queue、File Storage が含まれています。
- Premium ストレージ アカウント。現在、Azure 仮想マシン ディスクでのみサポートされています。Premium Storage の詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)」をご覧ください。

## ストレージ アカウントの課金

Azure Storage は、ストレージ アカウントに基づいた使用量によって課金されます。ストレージのコストは、ストレージ容量、レプリケーション スキーム、ストレージ トランザクション、送信データ転送という 4 つの要因に基づいています。

- ストレージ容量とは、データの保存に使用するためにストレージ アカウントに割り当てられた容量を指します。単純にデータを保存する場合の料金は、保存するデータ量とレプリケーション方法によって決まります。
- レプリケーションによって、1 回に保持するデータのコピー数や、データの場所が決定されます。
- トランザクションには、Azure Storage に対するすべての読み取り操作と書き込み操作が含まれます。
- 送信データ転送とは、Azure リージョンから転送されたデータを意味します。他のリージョンで動作しているアプリケーションがストレージ アカウント内のデータにアクセスした場合、そのアプリケーションがクラウド サービスか、その他の種類のアプリケーションかに関係なく、送信データ転送として課金されます(Azure サービスでは、同じデータ センター内のデータとサービスをグループ化して、送信データ転送処理とそれに伴う料金を削減または不要にする手順が用意されています)。  

ストレージ容量、レプリケーション、トランザクションの料金の詳細については、「[Azure の価格](https://azure.microsoft.com/pricing/details/#storage)」ページをご覧ください。送信データ転送の料金の詳細については、[データ転送の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページをご覧ください。

ストレージ アカウントの容量とパフォーマンスのターゲットの詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/azure/dn249410.aspx)」をご覧ください。

> [AZURE.NOTE] Azure の仮想マシンを作成する場合、デプロイメント場所にまだストレージ アカウントがない状況では、その場所に自動的にストレージ アカウントが作成されます。このため、使用する仮想マシンのディスク用にストレージ アカウントを作成する場合、必ずしも以下の手順に従う必要はありません。ストレージ アカウント名は仮想マシン名を基にして付けられます。詳細については、[Azure Virtual Machines のドキュメント](https://azure.microsoft.com/documentation/services/virtual-machines/)を参照してください。

## ストレージ アカウント エンドポイント

Azure Storage に格納するすべてのオブジェクトには一意の URL アドレスが設定されています。ストレージ アカウント名は、そのアドレスのサブドメインになります。サブドメインとドメイン名の組み合わせは、各サービスに固有であり、ストレージ アカウントの*エンドポイント*になります。

たとえば、ストレージ アカウントの名前が "mystorageaccount" の場合、ストレージ アカウントの既定のエンドポイントは次のようになります。

- BLOB サービス: http://*mystorageaccount*.blob.core.windows.net

- Table サービス: http://*mystorageaccount*.table.core.windows.net

- Queue サービス: http://*mystorageaccount*.queue.core.windows.net

- File サービス: http://*mystorageaccount*.file.core.windows.net

ストレージ アカウント内のオブジェクトにアクセスするための URL は、ストレージ アカウント内のオブジェクトの場所をエンドポイントに追加して作成します。たとえば、BLOB アドレスは次の形式になります。http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

また、カスタム ドメイン名もストレージ アカウントを使用するように構成することができます。詳細については、「[Azure ストレージ アカウントの BLOB データのカスタム ドメイン名の構成](storage-custom-domain-name.md)」をご覧ください。

## ストレージ アカウントの作成

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. ハブ メニューで、**[新規]**、**[データ + ストレージ]**、**[ストレージ アカウント]** の順にクリックします。

3. デプロイメント モデル (**[リソース マネージャー]** または **[クラシック]**) を選択します。**[リソース マネージャー]** が、推奨されるデプロイメント モデルです。詳細については、「[リソース マネージャー デプロイメントとクラシック デプロイメントを理解する](../resource-manager-deployment-model.md)」を参照してください。

4. ストレージ アカウントの名前を入力します。

	> [AZURE.NOTE] ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。
	>  
	> ストレージ アカウント名は Azure 内で一意である必要があります。選択したストレージ アカウント名が既に使用されているかどうかが、Azure ポータルによって示されます。

	Azure Storage で、オブジェクトのアドレスの処理にストレージ アカウント名がどのように使用されるかについては、後述の「[ストレージ アカウント エンドポイント](#storage-account-endpoints)」をご覧ください。

5. 作成するストレージ アカウントの種類を指定します。ストレージ アカウントの種類により、ストレージ アカウントがレプリケーションされる方法と、Standard Storage アカウントと Premium Storage アカウントのどちらであるかが決まります。

	既定のストレージ アカウントの種類は **Standard-RAGRS** で、Read-Access Geo Redundant レプリケーションを持つ Standard Storage アカウントです。この種類のストレージ アカウントは、プライマリ リージョンから数百マイル離れたセカンダリ リージョンにレプリケートされ、セカンダリ リージョンへの読み取りアクセスを提供します。

	Azure Storage のレプリケーション オプションの詳細については、[Azure Storage のレプリケーション](storage-redundancy.md)に関するページをご覧ください。Standard Storage アカウントと Premium Storage アカウントの詳細については、[Storage の概要](storage-introduction.md)に関するページと「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)」を参照してください。

6. ストレージ アカウントの診断を有効にするかどうかを指定します。診断には、Storage Analytics のログとメトリックが含まれます。

7. 複数の Azure サブスクリプションを所有している場合は、**[サブスクリプション]** フィールドが表示されます。新しいストレージ アカウントを作成するサブスクリプションを選択します。

8. 新しいリソース グループを指定するか、既定のリソース グループを選択します。リソース グループの詳細については、「[Azure ポータルを使用した Azure リソースの管理](../azure-portal/resource-group-portal.md)」を参照してください。

9. ストレージ アカウントの地理的な場所を選択します。

10. **[作成]** をクリックしてストレージ アカウントを作成します。

## ストレージ アクセス キーの管理

ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。

> [AZURE.NOTE] ストレージ アクセス キーは、他の人と共有しないことをお勧めします。アクセス キーを入力しないでストレージ リソースにアクセスする場合は、*Shared Access Signature* が利用できます。Shared Access Signature を使用すると、指定した期間、指定した権限で、アカウント内のリソースにアクセスできるようになります。詳細については、「[Shared Access Signature、第 1 部: SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

### ストレージ アクセス キーの表示とコピー

[Azure ポータル](https://portal.azure.com)で、お使いのストレージ アカウントに移動し、**[キー]** アイコンをクリックして、アカウントのアクセス キーを表示、コピー、再生成します。また、**[アクセス キー]** ブレードには、コピーしてアプリケーションで使用できるプライマリ キーとセカンダリ キーを使用してあらかじめ構成された接続文字列も含まれています。

### ストレージ アクセス キーの再生成

ストレージ接続のセキュリティを維持するために、ストレージ アカウントのアクセス キーを定期的に変更することをお勧めします。アクセス キーは 2 つ割り当てられるため、一方のアクセス キーでストレージ アカウントに接続したまま、もう一方のアクセス キーを再生成できます。

> [AZURE.WARNING] アクセス キーを再生成すると、そのストレージ アカウントに依存する仮想マシン、メディア サービス、およびアプリケーションが影響を受けます。アクセス キーを使用してストレージ アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。

**仮想マシン** - ストレージ アカウントに実行中の仮想マシンが含まれる場合は、アクセス キーの再生成後、すべての仮想マシンを再デプロイする必要があります。再デプロイメントを回避するには、アクセス キーを生成する前に仮想マシンをシャットダウンします。

**メディア サービス** - ストレージ アカウントに依存するメディア サービスがある場合は、アクセス キーを再生成した後で、そのキーをメディア サービスと再同期する必要があります。

**アプリケーション** - ストレージ アカウントを使用する Web アプリケーションまたはクラウド サービスがある場合は、キーを切り替えない限り、キーを再生成すると接続が失われます。

次に、ストレージ アクセス キーの交換プロセスを示します。

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

2. ストレージ アカウントのプライマリ アクセス キーを再生成します。**[アクセス キー]** ブレードで、**[Key1 の再生成]** をクリックし、確認画面で **[はい]** をクリックして新しいキーを生成します。

3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

4. 同様に、セカンダリ アクセス キーを再生成します。

## ストレージ アカウントの削除

使わなくなったストレージ アカウントを削除するには、[Azure ポータル](https://portal.azure.com)でストレージ アカウントに移動し、**[削除]** をクリックします。ストレージ アカウントを削除すると、アカウント内のすべてのデータを含む、アカウント全体が削除されます。

> [AZURE.WARNING] 削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。これはアカウントのどのリソースにも当てはまります。BLOB、テーブル、キュー、またはファイルを削除すると、完全に削除されます。

Azure 仮想マシンに関連付けられているストレージ アカウントを削除するには、最初にすべての仮想マシン ディスクが削除されていることを確認する必要があります。最初に仮想マシン ディスクを削除しないと、ストレージ アカウントを削除しようとしたときに、次のようなエラー メッセージが表示されます。

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

仮想マシン ディスクを削除するには、Azure クラシック ポータルで次の手順を実行します。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. [Virtual Machines] タブに移動します。
3. [ディスク] タブをクリックします。
4. データ ディスクを選択し、[ディスクの削除] をクリックします。
5. ディスク イメージを削除するには、[イメージ] タブに移動し、アカウントに保存されているイメージを削除します。

詳細については、[Azure Virtual Machine のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)のページを参照してください。

## 次のステップ

- [Azure Storage のレプリケーション](storage-redundancy.md)
- [Azure Storage への接続文字列の構成](storage-configure-connection-string.md)
- [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
- [Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/)

<!---HONumber=AcomDC_0128_2016-->