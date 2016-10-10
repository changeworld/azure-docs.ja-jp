<properties
	pageTitle="Windows VM の FAQ | Microsoft Azure"
	description="Resource Manager モデルで作成された Windows 仮想マシンについてよく寄せられる質問への回答を示します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Windows Virtual Machines についてのよく寄せられる質問 


この記事では、Resource Manager デプロイ モデルを使用して Azure で作成された Windows 仮想マシンについてよく寄せられる質問に回答します。このトピックの Linux バージョンについては、「[Linux Virtual Machines についてのよく寄せられる質問](virtual-machines-linux-faq.md)」を参照してください。

## Azure VM では何を実行できますか。

すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。Microsoft サーバー ソフトウェアを Azure で実行するためのサポートポリシーの詳細については、「[Microsoft Azure Virtual Machines のマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/kb/2721672)」を参照してください。

MSDN Azure 特典のサブスクライバーと MSDN 開発テスト用従量課金制プラン (開発およびテスト用) のサブスクライバーを対象に、特定のバージョンの Windows 7 および Windows 8.1 が利用可能となっています。詳細については、「[Windows Client images for MSDN subscribers](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)」を参照してください


## 仮想マシンではどれくらいのストレージ容量を使用できますか。

各データ ディスクで最大 1 TB (テラバイト) を利用できます。使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。詳細については、「[仮想マシンのサイズ](virtual-machines-windows-sizes.md)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージを利用できます。各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。価格の詳細については、「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。


## 仮想マシンへのアクセス方法を教えてください。

Windows VM 用のリモート デスクトップ接続 (RDP) を使用してリモート接続を確立します。手順については、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md)」をご覧ください。最大 2 つの同時接続がサポートされます (サーバーがリモート デスクトップ サービスのセッション ホストとして構成されている場合を除く)。


リモート デスクトップで問題が発生した場合は、[Windows ベースの Azure Virtual Machine へのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)に関するページをご覧ください。

Hyper-V に慣れている場合は、VMConnect と同様のツールを検討されるかもしれませんが、Azure では、仮想マシンへのコンソール アクセスがサポートされていないため、類似のツールは提供されていません。

## 一時ディスク (既定では D: ドライブ) をデータを格納するために使用できますか。

データの格納に一時ディスクを使用しないでください。一時ディスクは一時的なストレージであるため、データ損失の発生時にデータを復旧できない恐れがあります。データ損失は、仮想マシンを別のホストに移動するときに発生する可能性があります。仮想マシンが移動される理由としては、ホストの更新、仮想マシンのサイズ変更、ホスト上のハードウェア障害などが挙げられます。

アプリケーションで D: ドライブ文字を使用する必要がある場合は、一時ディスクで D: 以外を使用するようにドライブ文字を再割り当てできます。手順については、「[Windows 一時ディスクのドライブ文字を変更する方法](virtual-machines-windows-classic-change-drive-letter.md)」を参照してください。

## 一時ディスクのドライブ文字を変更する方法について教えてください。

ページ ファイルを移動してドライブ文字を再割り当てすることでドライブ文字を変更することができますが、正しい順序で手順を実行するよう注意する必要があります。手順については、「[Windows 一時ディスクのドライブ文字を変更する方法](virtual-machines-windows-classic-change-drive-letter.md)」を参照してください。

## 既存の VM を可用性セットに追加できますか。

いいえ。VM を可用性セットの一部にする場合は、VM を作成するときにセットに追加する必要があります。作成後に VM を可用性セットに追加する方法は現在ありません。

## 仮想マシンを Azure にアップロードすることができますか。

はい。手順については、「[Windows VM イメージを Microsoft Azure にアップロードする](virtual-machines-windows-upload-image.md)」を参照してください。

## OS ディスクのサイズを変更できますか。

はい。手順については、「[Azure リソース グループで仮想マシンの OS ドライブを展開する方法](virtual-machines-windows-expand-os-disk.md)」を参照してください。

## 既存の Azure VM をコピーまたは複製できますか。

はい。手順については、「[Resource Manager デプロイ モデルで Windows 仮想マシンのコピーを作成する方法](virtual-machines-windows-specialized-image.md)」を参照してください。

## Azure Resource Manager でカナダ中部およびカナダ東部のリージョンが表示されない理由を教えてください。

カナダ中部およびカナダ東部の 2 つの新しいリージョンは、既存の Azure サブスクリプションで仮想マシンを作成した場合、自動的には登録されません。Azure Resource Manager を使用してこれら以外の任意のリージョンに Azure ポータルから仮想マシンをデプロイすると、この登録は自動的に行われます。その他の任意の Azure リージョンに仮想マシンがデプロイされると、新しいリージョンを後続の仮想マシンで使用できるようになります。

## Azure では Linux VM がサポートされていますか?

はい。Linux VM をすばやく作成して試してみるには、「[ポータルを使用して Azure に Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)」を参照してください。

## VM の作成後、NIC を VM に追加できますか。

いいえ。NIC を追加できるのは VM の作成時のみです。

## コンピューター名の要件はありますか。

はい。コンピューター名は最大 15 文字の長さまで指定できます。リソースの名前付けの詳細については、「[インフラストラクチャの名前付けガイドライン](virtual-machines-windows-infrastructure-naming-guidelines.md)」を参照してください。

## VM を作成する際のユーザー名の要件は何ですか。

ユーザー名は最大 20 文字の長さまで指定できますが、末尾を句点 (".") にすることはできません 。

次のユーザー名を指定することはできません。

<table>
	<tr>
		<td style="text-align:center">administrator </td><td style="text-align:center"> 管理者 </td><td style="text-align:center"> ユーザー </td><td style="text-align:center"> user1</td>
	</tr>
	<tr>
		<td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
	</tr>
	<tr>
		<td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
	</tr>
	<tr>
		<td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
	</tr>
	<tr>
		<td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
	</tr>
	<tr>
		<td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
	</tr>
	<tr>
		<td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
	</tr>
	<tr>
		<td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
	</tr>
</table>

## VM を作成する際のパスワードの要件は何ですか。

パスワードは 8 ～ 123 文字の長さにし、次の 4 つの複雑性要件のうち 3 つを満たしている必要があります。

- 小文字を含む
- 大文字を含む
- 数字を含む
- 特殊文字を含む ([\\W\_] に一致する正規表現)

次のパスワードを指定することはできません。

<table>
	<tr>
		<td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
	</tr>
	<tr>
		<td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
	</tr>
</table>

<!---HONumber=AcomDC_0928_2016-->