<properties
	pageTitle="Azure ストレージ アカウント、コンテナー、または VHD の削除に関するトラブルシューティング| Microsoft Azure"
	description="Azure ストレージ アカウント、コンテナー、または VHD の削除に関するトラブルシューティング"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor="tysonn"
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;genli;robinsh"/>

# Azure ストレージ アカウント、コンテナー、または VHD の削除に関するトラブルシューティング

## 概要
[Azure ポータル](https://portal.azure.com/) または [Azure クラシック ポータル](https://manage.windowsazure.com/)で Azure ストレージ アカウント、コンテナー、または VHD を削除しようとすると、エラーが表示される場合があります。問題は、次の状況で発生する可能性があります。

-	バーチャル マシンを削除すると、ディスクと VHD は自動的に削除されません。これがストレージ アカウントの削除に失敗する理由である場合があります。ディスクを別の VM にマウントできるように、マイクロソフトはディスクを削除しません。

-	ディスクまたはディスクに関連付けられている BLOB のリースがまだ存在します。

この記事で Azure の問題に対処できない場合は、[MSDN と Stack Overflow の Azure フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。問題をこれらのフォーラムまたは Twitter の @AzureSupport に投稿できます。また、[Azure サポート](https://azure.microsoft.com/support/options/) サイトの **[サポートの要求]** を選択して、Azure サポート要求を提出することもできます。

## 解決策
最も一般的な問題を解決するには、次の方法をお試しください。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に切り替えます。
2. **[仮想マシン]** > **[ディスク]** を選択します。

	![Azure クラシック ポータルでのバーチャル マシン上のディスクのイメージ。](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 削除するストレージ アカウント、コンテナー、または VHD に関連付けられているディスクを検索します。ディスクの場所をチェックすると、関連付けられたストレージ アカウント、コンテナー、または VHD が表示されます。

	![Azure クラシック ポータルでディスクの場所情報を示すイメージ](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. ディスクの **[接続先]** フィールドに VM が表示されていないことを確認してから、ディスクを削除します。

 	> [AZURE.NOTE] ディスクが VM に接続されている場合は、ディスクを削除できません。削除された VM からディスクが非同期で切断されます。このフィールドの VM が削除された後に、クリアするまで数分間かかる場合があります。

5. **[仮想マシン]** > **[イメージ]** を選択して、ストレージ アカウント、コンテナー、または VHD に関連付けられているイメージを削除します。

	その後、そのストレージ アカウント、コンテナー、または VHD をもう一度削除します。

> [AZURE.WARNING] アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。これはアカウントのリソースにも当てはまります。 VHD、BLOB、テーブル、キュー、またはファイルを削除すると、それは完全に削除されます。リソースが使用されていないことを確認します。

## 一般的な問題

Azure ストレージ アカウント、コンテナー、または VHD を削除しようとするきに表示される可能性がある一般的なエラーを次のセクションに示します。

### シナリオ 1: ストレージ アカウントを削除できない

[Azure ポータル](https://portal.azure.com/)または [Azure クラシック ポータル](https://manage.windowsazure.com/)のストレージ アカウントに移動して **[削除]** を選択すると、次のエラー メッセージが表示される場合があります。

**Azure ポータルの場合**:

*ストレージ アカウント <vm-storage-account-name> を削除できませんでした。ストレージ アカウント <vm-storage-account-name> を削除できません。ストレージ アカウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

**Azure クラシック ポータルの場合**:

*ストレージ アカウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります (例: xxxxxxxxx- xxxxxxxxx-O-209490240936090599)。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

このエラーも表示される場合があります。

**Azure ポータルの場合**:

*ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください*。

**Azure クラシック ポータルの場合**:

*送信失敗ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください。ストレージ アカウントを削除しようとした際に、それに関連付けられているアクティブなディスクがまだ存在している場、削除する必要があるアクティブなディスクがあることを示すメッセージが表示されます*。

### シナリオ 2: コンテナーを削除できない

ストレージ コンテナーを削除しようとしたときに、次のエラーが表示される場合があります。

*ストレージ コンテナー <container name> を削除できませんでした。エラー: 現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした*。

### シナリオ 3: VHD を削除できない

VM を削除してから、関連する VHD の BLOB を削除しようと、次のメッセージが表示される場合があります。

*BLOB 'path/XXXXXX-XXXXXX-os-1447379084699.vhd' を削除できませんでした。エラー: 現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。*

## 停止済み (割り当て解除済み) について

従来のデプロイ モデルで作成されて保持されている VM は、[Azure ポータル](https://portal.azure.com/)または [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[停止済み (割り当て解除)]** 状態になります。

**Azure クラシック ポータル**:

![Azure ポータルでの [停止済み (割り当て解除)] 状態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure ポータル**:

![Azure クラシック ポータルでの [停止済み (割り当て解除)] 状態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

[停止済み (割り当て解除)] 状態は、CPU、メモリ、およびネットワークなど、コンピューター リソースを解放します。ただし、ディスクは引き続き維持されますので、ユーザーは必要に応じて VM を簡単にもう一度作成できます。これらのディスクは、Azure ストレージによって支えられている VHD の上に作成されます。ストレージ アカウントにはこれらの VHD があり、ディスクにはこれらの VHD のリースがあります。

## 次のステップ

- [ストレージ アカウントの削除](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Microsoft Azure (PowerShell) で BLOB ストレージのロックされているリースを解除する方法)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->