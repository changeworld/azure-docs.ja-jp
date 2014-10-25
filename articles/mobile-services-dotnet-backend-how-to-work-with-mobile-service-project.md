<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# .NET バックエンド モバイル サービスの使用

この記事では、Azure Mobile Services でモバイル サービスを定義する .NET バックエンド Visual Studio プロジェクトを使用する方法についての詳細な情報と例を提供します。このトピックのセクションは次のとおりです。

-   [はじめに][はじめに]
-   [テーブル操作][テーブル操作]
    -   [方法: テーブル操作に登録する][方法: テーブル操作に登録する]
    -   [方法: 既定の応答をオーバーライドする][方法: 既定の応答をオーバーライドする]
    -   [方法: execute success をオーバーライドする][方法: execute success をオーバーライドする]
    -   [方法: 既定のエラー処理をオーバーライドする][方法: 既定のエラー処理をオーバーライドする]
    -   [方法: カスタム パラメーターを追加する][方法: カスタム パラメーターを追加する]
    -   [方法: テーブルのユーザーを処理する][方法: テーブルのユーザーを処理する]
-   [カスタム API][カスタム API]
    -   [方法: カスタム API を定義する][方法: カスタム API を定義する]
    -   [方法: HTTP メソッドを実装する][方法: HTTP メソッドを実装する]
    -   [方法: データを XML として送受信する][方法: データを XML として送受信する]
    -   [方法: カスタム API でユーザーとヘッダーを操作する][方法: カスタム API でユーザーとヘッダーを操作する]
    -   [方法: カスタム API で複数のルートを定義する][方法: カスタム API で複数のルートを定義する]
-   [ジョブ スケジューラ][ジョブ スケジューラ]
    -   [方法: スケジュールされたジョブ スクリプトを定義する][ジョブ スケジューラ]
-   [ソース管理、共有コード、およびヘルパー関数][ソース管理、共有コード、およびヘルパー関数]
    -   [方法: Node.js モジュールを読み込む][方法: Node.js モジュールを読み込む]
    -   [方法: ヘルパー関数を使用する][方法: ヘルパー関数を使用する]
    -   [方法: ソース管理を使用してコードを共有する][方法: ソース管理を使用してコードを共有する]
    -   [方法: アプリケーションの設定を操作する][方法: アプリケーションの設定を操作する]
-   [コマンド ライン ツールの使用][コマンド ライン ツールの使用]
-   [テーブルの操作][テーブルの操作]
    -   [方法: スクリプトからテーブルにアクセスする][方法: スクリプトからテーブルにアクセスする]
    -   [方法: 一括挿入を実行する][方法: 一括挿入を実行する]
    -   [方法: JSON 型をデータベース型にマッピングする][方法: JSON 型をデータベース型にマッピングする]
    -   [テーブルにアクセスするための Transact-SQL の使用][テーブルにアクセスするための Transact-SQL の使用]
-   [デバッグおよびトラブルシューティング][デバッグおよびトラブルシューティング]
    -   [方法: 出力をモバイル サービス ログに書き込む][方法: 出力をモバイル サービス ログに書き込む]

## <a name="intro"></a> はじめに

.NET バックエンド モバイル サービスを使用すると、サーバーに格納されて実行される JavaScript コードとして、カスタムのビジネス ロジックを定義できます。このサーバー スクリプト コードは、次のサーバー機能のいずれかに割り当てられます。

-   [特定のテーブルの挿入、読み取り、更新、または削除操作][テーブル操作]。
-   [スケジュールされたジョブ][ジョブ スケジューラ]。
-   [カスタム API で定義された HTTP メソッド][カスタム API]。

サーバー スクリプトのメイン関数のシグネチャは、スクリプトが使用されるコンテキストによって異なります。スクリプト間で共有される共通スクリプト コードを nodes.js モジュールとして定義することもできます。詳細については、「[ソース管理と共有コード][ソース管理、共有コード、およびヘルパー関数]」を参照してください。

個々のサーバー スクリプト オブジェクトおよび関数の詳細については、[モバイル サービスのサーバー スクリプト リファレンス]を参照してください。

<!-- Anchors. -->

  [はじめに]: #intro
  [テーブル操作]: #table-scripts
  [方法: テーブル操作に登録する]: #register-table-scripts
  [方法: 既定の応答をオーバーライドする]: #override-response
  [方法: execute success をオーバーライドする]: #override-success
  [方法: 既定のエラー処理をオーバーライドする]: #override-error
  [方法: カスタム パラメーターを追加する]: #access-headers
  [方法: テーブルのユーザーを処理する]: #work-with-users
  [カスタム API]: #custom-api
  [方法: カスタム API を定義する]: #define-custom-api
  [方法: HTTP メソッドを実装する]: #handle-methods
  [方法: データを XML として送受信する]: #api-return-xml
  [方法: カスタム API でユーザーとヘッダーを操作する]: #get-api-user
  [方法: カスタム API で複数のルートを定義する]: #api-routes
  [ジョブ スケジューラ]: #scheduler-scripts
  [ソース管理、共有コード、およびヘルパー関数]: #shared-code
  [方法: Node.js モジュールを読み込む]: #modules-helper-functions
  [方法: ヘルパー関数を使用する]: #helper-functions
  [方法: ソース管理を使用してコードを共有する]: #shared-code-source-control
  [方法: アプリケーションの設定を操作する]: #app-settings
  [コマンド ライン ツールの使用]: #command-prompt
  [テーブルの操作]: #working-with-tables
  [方法: スクリプトからテーブルにアクセスする]: #access-tables
  [方法: 一括挿入を実行する]: #bulk-inserts
  [方法: JSON 型をデータベース型にマッピングする]: #JSON-types
  [テーブルにアクセスするための Transact-SQL の使用]: #TSQL
  [デバッグおよびトラブルシューティング]: #debugging
  [方法: 出力をモバイル サービス ログに書き込む]: #write-to-logs
