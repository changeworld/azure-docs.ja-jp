<properties 
	pageTitle="ディレクトリ統合ツールの比較"
	description="このページの包括的な表では、さまざまなディレクトリ統合ツールを比較しています。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# ディレクトリ統合ツールの比較

ディレクトリ統合ツールは、長年にわたって成長および進化してきました。このドキュメントでは、このようなツールをまとめた表と、各ツールで使用できる機能の比較を示しています。

>[AZURE.NOTE]Azure AD Connect には、以前 Dirsync と AAD Sync としてリリースされたコンポーネントと機能が組み込まれています。これらのツールは、個別にリリースされなくなりました。今後の機能強化はすべて Azure AD Connect の更新プログラムに含まれるため、最新機能の入手場所はいつでもわかります。
>
>現在 Dirsync は引き続きサポートされていますが、今後のある時点で廃止される予定です。廃止された後、一定期間のみサポートされます。この期間の経過後は、Dirsync のサポートが終了します。


各表で使用されるキーは次のとおりです。

● = 現在利用可能</br> FR = 今後リリース</br> PP = パブリック プレビュー</br>


## オンプレミスからクラウドへの同期

| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス (AAD Sync) | Azure Active Directory 同期ツール (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 単一のオンプレミス AD フォレストへの接続 | ● | ● | ● | ● |
| 複数のオンプレミス AD フォレストへの接続 |● | ● | | ● |
| 複数のオンプレミス Exchange 組織への接続 | ● | | | |
| 単一のオンプレミス LDAP ディレクトリへの接続 | FR | | | ● |
| 複数のオンプレミス LDAP ディレクトリへの接続 |FR | | | ● |
| オンプレミス AD とオンプレミス LDAP ディレクトリへの接続 |FR | | | ● |
| カスタム システム (SQL、Oracle、MySQL など) への接続 | FR | | | ● |
| ユーザー定義属性の同期 (ディレクトリ拡張機能) | ● | | | |

## クラウドからオンプレミスへの同期

| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス | Azure Active Directory 同期ツール (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| デバイスの書き戻し | ● | | ● | |
| 属性の書き戻し (Exchange ハイブリッド デプロイの場合) | ● | ● | ● | ● |
| ユーザー オブジェクトとグループ オブジェクトの書き戻し | ●| | | |
| (セルフ サービスによるパスワードのリセット (SSPR) とパスワードの変更による) パスワードの書き戻し | ● | ● | | |



## 認証機能のサポート

| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス | Azure Active Directory 同期ツール (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 単一のオンプレミス AD フォレストのパスワード同期 | ● | ● | ● | |
| 複数のオンプレミス AD フォレストのパスワード同期 | ●| ● | | |
| フェデレーションを使用したシングル サインオン | ● | ● | ● | ● |
| (SSPR とパスワードの変更による) パスワードの書き戻し |● | ● | | |



## セットアップとインストール

| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス | Azure Active Directory 同期ツール (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| ドメイン コントローラーへのインストールのサポート | ● | ● | ● | |
| SQL Express を使用したインストールのサポート | ● | ● | ● | |
| DirSync からの簡単なアップグレード |● | | | |
| Windows Server の言語のローカライズ | FR | ● | ● | |
| Windows Server 2008 および Windows Server 2008 R2 のサポート | ● (同期の場合。フェデレーションの場合は利用不可)| ● | ● | ● |
| Windows Server 2012 および Windows Server 2012 R2 のサポート | ● | ● | ● | 2012 のみ |


## フィルター処理と構成

機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス | Azure Active Directory 同期ツール (DirSync) | Forefront Identity Manager 2010 R2 (FIM)  
:-------- |:--------:|:--------:|:--------:|:--------:|
ドメインおよび組織単位でのフィルター処理 | ● | ● | ● | ●  
オブジェクトの属性値でのフィルター処理 | ● | ● | ● | ● 
最小の属性セットの同期 (MinSync) の許可 | ● | ● | |   
属性フローへのさまざまなサービス テンプレートの適用の許可 |● | ● | |   
AD から Azure AD に流れる属性の削除の許可 | ● | ● | |  
属性フローの高度なカスタマイズの許可 | ● | ● | | ●  

<!---HONumber=August15_HO9-->