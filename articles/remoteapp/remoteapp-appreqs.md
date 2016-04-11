
<properties
    pageTitle="Azure RemoteApp のアプリの要件 | Microsoft Azure"
    description="Azure RemoteApp で使用するアプリの要件について説明します。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="elizapo" />



# アプリの要件
Azure RemoteApp は、Windows Server 2012 R2 イメージの 32 ビットまたは 64 ビットの Windows ベース アプリケーションのストリーミングをサポートします。既存の 32 ビットまたは 64 ビットの Windows ベース アプリケーションのほとんどは、Azure RemoteApp (リモート デスクトップ サービス、以前のターミナル サービス) 環境で "そのまま" 動作します。ただし、"動作すること" と "うまく動作すること" は異なります。アプリケーションの中には正常に問題なく機能するものもあれば、そうでないものもあります。以下の情報は、リモート デスクトップ サービス環境でのアプリケーション開発、および互換性を保証するためのテストのガイダンスを示したものです。

ヒント: Microsoft では、アプリケーションの実行例の作成に取り組んでいます。RemoteApp での Microsoft Access、QuickBooks、App-V の使用に関する新しいトピックを掲載する予定です。

## 必要条件
これらの 3 つの要件に従うと、アプリケーションが RemoteApp で快適に動作するのに役立ちます。

1.	[Windows デスクトップ アプリ認定要件](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx)のすべてを満たし、[リモート デスクトップ サービスのプログラミング ガイドライン](https://msdn.microsoft.com/library/aa383490.aspx)に準拠しているアプリケーションは、RemoteApp との互換性があります。
2.	アプリケーションで、失われる可能性があるイメージ上または RemoteApp インスタンス上にローカルにデータを格納しないでください。RemoteApp コレクションを作成した後、インスタンスは複製されてステートレスとなり、アプリケーションのみを含む必要があります。外部ソース、またはユーザーのプロファイル内にデータを格納します。
3.	カスタム イメージには、失われる可能性があるデータを含めないでください。  

## アプリケーションのテスト
次の手順を使用して、アプリケーションをテストします。

1.	Windows Server 2012 R2 とアプリケーションのインストール
2.	リモート デスクトップの有効化
3.	2 つのユーザー アカウントの作成 (UserA と UserB、両方のユーザー アカウントをリモート デスクトップ セキュリティ グループに追加する)
4.	アプリケーションの起動時に PC に対する 2 つの同時 RDS セッションを確立することにより、マルチセッションの互換性を確認
5.	アプリケーションの動作の検証

## アプリケーション開発のガイドライン
以下のガイドラインを使用して、RemoteApp 向けのアプリケーションを開発します。

### 複数のユーザー

- [単一ユーザー向けのアプリケーション](https://msdn.microsoft.com/library/aa380661.aspx)をインストールすることにより、マルチユーザー環境で問題が発生する可能性があります。
- アプリケーションは、すべてのユーザーに適用されるグローバル情報とは隔離して、ユーザー固有の場所に[ユーザー固有の情報を格納する](https://msdn.microsoft.com/library/aa383452.aspx)必要があります。
- RemoteApp は、[カーネル オブジェクト用の名前空間](https://msdn.microsoft.com/library/aa382954.aspx)を複数使用します。グローバル名前空間は、主にクライアント/サーバー アプリケーションのサービスで使用されます。
- 1 つのリモート デスクトップ セッション ホスト (RD セッション ホスト) サーバーには複数のユーザーが同時にログオンできるため、コンピューター名やコンピューターに割り当てられている [IP アドレス](https://msdn.microsoft.com/library/aa382942.aspx)が単一のユーザーのものであると想定するのは安全ではありません。

### パフォーマンス
- アプリケーションを RemoteApp に追加する前に、[グラフィック効果](https://msdn.microsoft.com/library/aa380822.aspx)を無効にします。
- すべてのユーザーに対して CPU の可用性を最大にするため、[バックグラウンド タスク](https://msdn.microsoft.com/library/aa380665.aspx)を無効にするか、リソースを集中的に使用しない効率的なバックグラウンド タスクを作成します。
- マルチユーザー、マルチプロセッサ環境向けに、アプリケーションの[スレッド使用状況](https://msdn.microsoft.com/library/aa383520.aspx)を調整する必要があります。
- パフォーマンスを最適化するために、クライアント セッションで動作しているかどうかをアプリケーションで[検出する](https://msdn.microsoft.com/library/aa380798.aspx)ことをお勧めします。

<!---HONumber=AcomDC_0330_2016-->