<properties
	pageTitle="Azure ストレージ アカウント、コンテナー、VHD を削除できない問題を解決する | Microsoft Azure"
	description="Azure ストレージ アカウント、コンテナー、VHD を削除できない問題を解決する"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Azure ストレージ アカウント、コンテナー、VHD を削除できない問題を解決する

## 概要
[Azure ポータル](https://portal.azure.com/)または [Azure クラシック ポータル](https://manage.windowsazure.com/)で Azure ストレージ アカウント、コンテナー、VHD を削除しようとしてエラーを受け取ることがあります。この問題の原因は次のようなものになります。

-	VM を削除するとき、ディスクと VHD は自動的に削除されません。それがストレージ アカウントを削除できない理由になっていることがあります。ディスクを別の VM にマウントできるように、マイクロソフトはディスクを削除しません。

-	ディスクかディスクで関連付けられている BLOB がリースになっています。

この記事で Azure の問題に対処できない場合は、[MSDN と Stack Overflow の Azure フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。問題をこれらのフォーラムまたは Twitter の @AzureSupport に投稿できます。また、[Azure サポート](https://azure.microsoft.com/support/options/) サイトの **[サポートの要求]** を選択して、Azure サポート要求を提出することもできます。

## 解決策
最も一般的な問題を解決するには、次の方法をお試しください。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に切り替えます。
2. **[仮想マシン]**、**[ディスク]** の順に選択します。

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 削除するストレージ アカウント、コンテナー、VHD に関連付けられているディスクを見つけます。そのディスクがある場所では、ストレージ アカウント、コンテナー、VHD が関連付けられています。

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. ディスクの **[接続先]** フィールドに VM がないことを確認し、ディスクを削除します。

 	> [AZURE.NOTE] ディスクが VM に接続されている場合、削除できません。削除された VM からディスクが非同期で切り離されます。VM が削除されてからこのフィールドが消去されるまで数分かかることがあります。

5. **[仮想マシン]**、**[イメージ]** の順に選択し、ストレージ アカウント、コンテナー、VHD に関連付けられているイメージを削除します。

その後、もう一度ストレージ アカウント、コンテナー、VHD の削除を試します。

> [AZURE.WARNING] アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。これはアカウントのどのリソースにも当てはまります。VHD、BLOB、テーブル、キュー、またはファイルを削除すると、完全に削除されます。リソースが使用されていないことを確認してください。

## 症状

次のセクションでは、Azure ストレージ アカウント、コンテナー、VHD を削除しようとして表示される一般的なエラーについて説明します。

### シナリオ 1: ストレージ アカウントを削除できない

[Azure ポータル](https://portal.azure.com/)または [Azure クラシック ポータル](https://manage.windowsazure.com/)でストレージ アカウントに移動し、[削除] を選択し、不要になったストレージ アカウントを削除しようとしても、次のエラー メッセージが表示されることがあります。

**Azure ポータルで**:

*ストレージ アカウント <vm-storage-account-name> を削除できません。ストレージ アカウント <vm-storage-account-name> を削除できません。ストレージ カウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

**Azure クラシック ポータルで**:

*ストレージ カウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります (例: xxxxxxxxx- xxxxxxxxx-O-209490240936090599)。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

次のエラーが表示されることもあります。

**Azure ポータルで**:

*ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください*。

**Azure クラシック ポータルで**:

*送信失敗 ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください。ストレージ アカウントを削除しようとした際に、それに関連付けられているアクティブなディスクがまだ存在している場、削除する必要があるアクティブなディスクがあることを示すメッセージが表示されます*。

### シナリオ 2: コンテナーを削除できない

ストレージ コンテナーを削除しようとして、次のエラーが表示されることがあります。

*ストレージ コンテナー <container name> を削除できませんでした。エラー: 現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした*。

### シナリオ 3: VHD を削除できない

VM を削除した後、関連付けられている VHD の BLOB を削除しようとして次のメッセージが表示されます。

*BLOB 'パス/XXXXXX-XXXXXX-os-1447379084699.vhd' を削除できませんでした。エラー: 現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。*

## 詳細

維持されている V1 VM は [Azure ポータル](https://portal.azure.com/) または [Azure クラシック ポータル](https://manage.windowsazure.com/)に [停止 (割り当て解除)] 状態で表示されます。

**Azure クラシック ポータル**:

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Azure ポータル**:

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

[停止 (割り当て解除)] 状態のとき、CPU、メモリ、ネットワークなど、コンピューターのリソースが解放されますが、ディスクは維持されるので、必要に応じて、VM をすばやく再作成できます。これらのディスクは、Azure ストレージによってバックアップされる VHD の上に作成されます。ストレージ アカウントにはこれらの VHD があり、ディスクにはこれらの VHD のリースがあります。

## 参照

- [ストレージ アカウントの削除](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Microsoft Azure (PowerShell) で BLOB ストレージのロックされているリースを解除する方法)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0323_2016-->