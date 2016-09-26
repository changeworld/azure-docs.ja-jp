<properties
	pageTitle="Cloud Services に関する FAQ |Microsoft Azure"
	description="Cloud Services に関してよく寄せられる質問。"
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# Cloud Services に関する FAQ
この記事では、Microsoft Azure Cloud Services についてよく寄せられる質問 (FAQ) とその回答を紹介します。Azure の価格およびサポートに関する一般的な情報については、「[Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083)」も参照してください。サイズについては、[Cloud Services VM サイズのページ](cloud-services-sizes-specs.md)を参照してください。

## 証明書

### 証明書はどこにインストールすればいいですか?

- **My** 秘密キーを持つアプリケーション証明書 (*.pfx、*.p12)。

- **CA** すべての中間証明書をこのストア内に保存します (ポリシー CA および下位 CA)。

- **ROOT** ルート CA ストア。メインのルート CA 証明書をここに保存します。

### 有効期限が切れた証明書を削除できません

Azure では、証明書の使用中にその証明書を削除することはできません。証明書を使用するデプロイメントを削除するか、別の証明書または更新した証明書でデプロイメントを更新する必要があります。

### 有効期限切れの証明書を削除してください

証明書が使用されていない限りは、[Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell コマンドレットを使って証明書を削除できます。

### Windows Azure Service Management for Extensions という名前の証明書の期限が切れています

リモート デスクトップ拡張機能などの拡張機能がクラウド サービスに追加されるとかならず、これらの証明書が作成されます。これらの証明書は、拡張機能のプライベート構成を暗号化および復号化するためだけに使用されます。これらの証明書の有効期限が切れていても問題はありません。有効期限の日付はチェックされません。

### 削除した証明書が再表示され続けます

そのような証明書が再表示され続ける原因は、ほとんどの場合、ご使用になっているツール (Visual Studio など) です。証明書を使用しているツールに再接続するたびに、ツールが Azure に証明書を再アップロードします。

### 証明書が表示されません

仮想マシン インスタンスをリサイクルすると、ローカルのすべての変更は失われます。ロールが起動するたびに、[スタートアップ タスク](cloud-services-startup-tasks.md) を使用して、仮想マシンに証明書をインストールしてください。

### ポータルで管理証明書が見つかりません

[管理証明書](..\azure-api-management-certs.md)は Azure クラシック ポータルでのみ使用できます。現在の Azure ポータルでは、管理証明書は使用しません。

### 管理証明書を無効にするにはどうすればよいですか

[管理証明書](..\azure-api-management-certs.md)を無効にすることはできません。使用しなくなった場合は、Azure クラシック ポータルで削除できます。

### 特定の IP アドレスの SSL 証明書を作成するにはどうすればよいですか

[証明書を作成するためのチュートリアル](cloud-services-certs-create.md)の手順に従います。DNS 名として IP アドレスを使用します。

## トラブルシューティング

### マルチ VIP クラウド サービスの IP アドレスを予約できません

まず、IP アドレスを予約しようとしている仮想マシン インスタンスが有効であることを確認してください。次に、ステージング デプロイメントと運用環境デプロイメントの両方で予約済みの IP を使っていることを確認します。デプロイメントをアップグレードする間、この設定を**変更しない**でください。

<!---HONumber=AcomDC_0914_2016-->