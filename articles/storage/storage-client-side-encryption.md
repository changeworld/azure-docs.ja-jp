<properties 
	pageTitle="Microsoft Azure Storage | Microsoft Azure のクライアント側の暗号化" 
	description="Azure Storage Client Library for .NET プレビューはクライアント側の暗号化と Azure Key Vault との統合に役立ちます。クライアント側の暗号化は Azure Storage アプリケーションのセキュリティを最大まで高めます。サービスがアクセス キーを認識できないためです。クライアント側の暗号化は BLOB、キュー、テーブルに利用できます。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Microsoft Azure Storage のクライアント側の暗号化 (プレビュー)

## 概要

[新しい Azure Storage Client Library for .NET のプレビュー](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview)にようこそ。このプレビュー ライブラリには、開発者が Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化し、ダウンロード中にデータを復号化する作業を支援する新しい機能が含まれています。このプレビュー ライブラリはまた、Azure [Key Vault](http://azure.microsoft.com/services/key-vault/) の統合にも役立ち、ストレージ アカウント キー管理に利用できます。

## クライアント側の暗号化を使用する理由

クライアント側の暗号化はアカウント アクセス キーを完全に制御できるという点でサーバー側の暗号化より優れています。クライアント側の暗号化はアプリケーションのセキュリティを最大まで高めます。Azure Storage はキーを認識せず、データを復号できないためです。このプレビュー ライブラリは [GitHub](https://github.com/Azure/azure-storage-net/tree/preview) で公開されています。このライブラリがデータを暗号化するしくみを見て、必要な水準を満たすことを確認できます。

## クライアント側の暗号化を支援するライブラリを提供する理由

開発者がアップロードに先立ってクライアント側でデータを暗号化するとき、暗号化の専門知識が必要になります。パフォーマンスとセキュリティの設計も必要です。開発者がそれぞれ独自の暗号化ソリューションを設計しなければならないとすれば、ソリューションごとに異なり、連動しないでしょう。

プレビュー ライブラリは次の目的で設計されています。

- セキュリティのベスト プラクティスを開発者の代わりに実装します。
- パフォーマンスを最大化します。
- 一般的なシナリオで使いやすくします。
- 言語間の相互運用性を支援します。.NET クライアント ライブラリで暗号化されたデータは、将来、Java、Node.js、C++ など、その他のサポート言語で復号化可能になる予定です (逆もまた同様)。

## 現在のところ、何を利用できますか。

現在のところ、このプレビュー ライブラリは BLOB、テーブル、エンベロープ手法によるキューの暗号化に対応しています。非対称キーによる暗号化と復号化は計算が非常に複雑になります。そのため、エンベロープ手法では、データ自体は非対称キーで直接暗号化されず、代わりに、ランダムな対称コンテンツ暗号化キーで暗号化されます。次に、このコンテンツ暗号化キーが公開キーで暗号化されます。Azure Key Vault のサポートにより、キーを効率的に管理できます。

クライアント側の暗号化は簡単に利用できます。暗号化ポリシー (BLOB、キュー、テーブル) と共に要求オプションを指定し、データのアップロード/ダウンロード API に渡すことができます。クライアント ライブラリは Azure Storage にアップロードするときにクライアントのデータを自動的に暗号化し、取得時にデータを復号化します。詳細情報とコード サンプルはブログ投稿の「 [Getting Started with Client-Side Encryption for Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)」にあります。

クライアント側の暗号化に関する追加詳細:

- **セキュリティ**: 顧客のストレージ アカウント キーが危険にさらされても、暗号化されたデータを読み取ることはできません。
- **オーバーヘッドが固定された暗号化**: 暗号化されたデータのサイズは元のサイズから予測できます。
- **自己完結型の暗号化**: すべての BLOB、テーブル エンティティ、キュー メッセージはオブジェクト自体またはそのメタデータにすべての暗号化情報を保存します。必要な外部の値は暗号化キーだけです。
- **キー ローテーション**: ユーザーは自分でキーをローテーションさせることができます。キー ローテーション プロセスでは複数のキーがサポートされます。
- **クリーンなアップグレード パス**: 将来、暗号化アルゴリズムとプロトコル バージョンが追加される予定ですが、コードを大幅に変更する必要はありません。
- **BLOB 暗号化サポート**:
	- **BLOB 完全アップロード**: 文書、写真、動画などのファイルをまるごと暗号化し、アップロードできます。
	- **BLOB の完全または範囲基準ダウンロード**: BLOB をまるごと、あるいは範囲を指定してダウンロードし、復号化できます。


>[AZURE.IMPORTANT]プレビュー ライブラリを利用するとき、以上の重要な点に注意してください。
>
>- 本稼動データにプレビュー ライブラリを使用しないでください。将来、ライブラリの変更は使用されるスキーマに影響を与えます。プレビュー ライブラリで暗号化されたデータの復号化は将来のバージョンでは保証されません。  
>- 暗号化された BLOB を読み書きするときは、完全 BLOB アップロード コマンドと範囲/完全 BLOB ダウンロード コマンドを使用します。Put Block、Put Block List、Write Pages、Clear Pages などのプロトコル操作で暗号化された BLOB に書き込む行為は避けてください。暗号化された BLOB が壊れたり、読み取り不可能になったりすることがあります。
>- テーブルの場合、同様の制約があります。暗号化メタデータを更新せずに暗号化されたプロパティを更新する行為は避けてください。
>- 暗号化された BLOB にメタデータを設定する場合、復号化に必要な暗号化関連メタデータを上書きできます。メタデータの設定は付加的ではないためです。これはスナップショットの場合にも該当します。暗号化された BLOB のスナップショットを作成するときはメタデータを指定しないでください。

## 関連項目

- [Getting Started with Client-Side Encryption for Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [Azure Storage Client Library for .NET NuGet package (Preview)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [Azure Storage Client Library for .NET Source Code (Preview)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->