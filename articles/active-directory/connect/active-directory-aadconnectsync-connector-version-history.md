---
title: コネクタ バージョンのリリース履歴 | Microsoft Docs
description: このトピックでは、Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタの全リリース一覧を提供します
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9bbf75f258f9853803ca4c00155eb186ceca54a3
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916408"
---
# <a name="connector-version-release-history"></a>コネクタ バージョンのリリース履歴
Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタは頻繁に更新されます。

> [!NOTE]
> このトピックでは、FIM と MIM のみを取り上げています。 これらのコネクタは、Azure AD Connect にはインストールできません。 リリースされたコネクタは、指定されたビルドにアップグレードするときに AADConnect にプレインストールされます。


このトピックでは、リリース済みのすべてのバージョンのコネクタを一覧にまとめています。

関連リンク:

* [最新コネクタのダウンロード](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Generic LDAP コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) リファレンス ドキュメント
* [Generic SQL コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) リファレンス ドキュメント
* [Web サービス コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) リファレンス ドキュメント
* [PowerShell コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) リファレンス ドキュメント
* [Lotus Domino コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) リファレンス ドキュメント


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>修正された問題:
* ConnectorsLog System.Diagnostics.EventLogInternal.InternalWriteEvent(メッセージ: システムに接続されたデバイスが機能していません) が解決されました。
* コネクタのこのリリースでは、miiserver.exe.config 内の 3.3.0.0-4.1.3.0 から 4.1.4.0 へのバインディング リダイレクトを更新する必要があります。
* 一般的な Web サービス:
    * 構成ツールで有効な JSON 応答を保存できなかった問題が解決されました。
* Generic SQL:
    * エクスポートでは常に、削除の操作に対して更新クエリのみが生成されます。 削除クエリを生成するために追加されました。
    * 差分インポートの操作のためのオブジェクトを取得する SQL クエリ ([Delta Strategy] (差分方式) が [変更の追跡] である場合) が修正されました。 この実装での既知の制限: [変更の追跡] モードの差分インポートでは、複数値属性の変更が追跡されません。
    * 複数値属性の最後の値を削除することが必要な場合のために、削除クエリを生成する可能性が追加されました。この行には、削除することが必要な値以外のデータは含まれません。
    * SP によって OUTPUT パラメーターが実装された場合の System.ArgumentException の処理 
    * varbinary(max) 型のフィールドへのエクスポートの操作を作成するためのクエリが正しくない
    * parameterList 変数が (ExportAttributes 関数と GetQueryForMultiValue 関数で) 2 回初期化された問題


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>修正された問題:

* Lotus Notes:
  * カスタム証明者のフィルター処理オプション
  * ImportOperations クラスのインポートで、"Views" モードで実行できる操作と "Search" モードで実行できる操作の定義が修正されました。
* Generic LDAP:
  * OpenLDAP ディレクトリでは、entryUUI ではなく DN をアンカーとして使用します。 GLDAP コネクタに、アンカーを変更できるオプションが追加されました。
* Generic SQL:
  * varbinary(max) 型のフィールドへのエクスポートが修正されました。
  * データ ソースのバイナリ データを CSEntry オブジェクトに追加するときに、DataTypeConversion 関数が 0 バイトで失敗していました。 CSEntryOperationBase クラスの DataTypeConversion 関数が修正されました。




### <a name="enhancements"></a>強化された機能:

* Generic SQL:
  * [Global Parameters]\(グローバル パラメーター\) ページの Generic SQL 管理エージェントの構成ウィンドウに、名前付きパラメーターを使用するかどうかを指定してストアド プロシージャの実行モードを構成する機能が追加されました。 [Global Parameters]\(グローバル パラメーター\) ページに、[Use named parameters to execute a stored procedure]\(名前付きパラメーターを使用してストアド プロシージャを実行する\) というラベルのチェック ボックスが用意されています。このチェック ボックスを使用して、ストアド プロシージャを実行する際に名前付きパラメーターを使用するかどうかを指定できます。
    * 現時点では、名前付きパラメーターを使用してストアド プロシージャを実行する機能は、IBM DB2 データベースと MSSQL データベースにのみ対応しています。 Oracle データベースと MySQL データベースでは、この方法は機能しません。 
      * MySQL の SQL 構文では、ストアド プロシージャでの名前付きパラメーターをサポートしていません。
      * Oracle の ODBC ドライバーでは、ストアド プロシージャの名前付きパラメーターをサポートしていません。

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>修正された問題:

* 一般的な Web サービス:
  * 複数のエンドポイントがあるときに SOAP プロジェクトが作成されない問題を修正しました。
* Generic SQL:
  * インポート操作において、コネクタ スペースに保存するときに GSQL で時間が正しく変換されませんでした。 GSQL のコネクタ スペースの既定の日付と時刻の形式が、"yyyy-MM-dd hh:mm:ssZ" から "yyyy-MM-dd HH:mm:ssZ" に変更されました。

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>修正された問題:

* 一般的な Web サービス:
  * Wsconfig ツールでは、JSON 配列が、REST サービス メソッドの "サンプル要求" から正しく変換されませんでした。 このため、REST 要求のこの JSON 配列で、シリアル化の問題が発生しました。
  * Web サービス コネクタの構成ツールでは、JSON 属性名で空白記号を使用できません。 
    * 代入パターンを、WSConfigTool.exe.config ファイルに手動で追加できます (例: ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```)。
> [!NOTE]
> エクスポートには JSONSpaceNamePattern キーが必要です。次のエラーが表示されます: メッセージ: 空の名前は無効です。 

* Lotus Notes:
  * **[Allow custom certifiers for Organization/Organizational Units]\(組織/組織単位のカスタム証明者を許可する\)** オプションが無効の場合、エクスポート (更新) 中のコネクタは失敗します。エクスポート フロー後は、すべての属性が Domino にエクスポートされていますが、KeyNotFoundException は、エクスポート時に同期に返されます。 
    * これは、以下の属性のいずれかを変更することで DN (UserName 属性) を変更しようとしているときに、名前の変更操作が失敗するために発生します。  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * **[Allow custom certifiers for Organization/Organizational Units]\(組織/組織単位のカスタム証明者を許可する\)** オプションが有効な場合、必要な証明者がまだ空だと、KeyNotFoundException が発生します。

### <a name="enhancements"></a>強化された機能:

* Generic SQL:
  * **シナリオ: デザインを刷新して実装済み:** "*" 機能
  * **ソリューションの説明:** [複数値参照属性処理](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)の手法を変更。


### <a name="fixed-issues"></a>修正された問題:

* 一般的な Web サービス:
  * WebService コネクタが存在する場合、サーバー構成をインポートできない
  * WebService コネクタが複数の Web サービスと連動しない

* Generic SQL:
  * 単一値参照属性に対して、オブジェクトの種類が一覧表示されない
  * 複数値テーブルから値が削除されるとき、変更追跡方針の差分インポートによりオブジェクトが削除される
  * GSQL コネクタの OverflowException、AS/400 に DB2

Lotus:
  * GlobalParameters ページを開く前に OU 検索の有効/無効を切り替えるオプションを追加

## <a name="114430"></a>1.1.443.0

リリース日: 2017 年 3 月

### <a name="enhancements"></a>強化された機能

* Generic SQL:</br>
  **シナリオの現象:** 1 つのオブジェクトの種類への参照のみが許可され、メンバーによる相互参照が必要であるということは、よく知られている SQL コネクタの制限事項です。 </br>
  **ソリューションの説明:** [*] オプションが選択されている参照の処理手順では、オブジェクトの種類の "すべての" 組み合わせが同期エンジンに返されます。

>[!Important]
- これにより、多数のプレースホルダーが作成されます。
- オブジェクトの種類全体で名前が一意であることを確認する必要があります。


* Generic LDAP:</br>
 **シナリオ:** 特定のパーティションで少数のコンテナーのみを選択した場合でも、検索はパーティション全体で実行されます。 特定のパーティションは Synchronization Service によってフィルター処理されますが、MA によってフィルター処理されることはありません。これにより、パフォーマンスの低下が発生することがあります。 </br>

 **ソリューションの説明:** パーティション全体を検索する代わりに、各コンテナでオブジェクトを検索できるように GLDAP コネクタのコードを変更しました。


* Lotus Domino:

  **シナリオ:** エクスポート時のユーザーの削除で、Domino メールの削除をサポートします。 </br>
  **ソリューション:** エクスポート時のユーザーの削除で、構成可能なメールの削除をサポートします。

### <a name="fixed-issues"></a>修正された問題:
* 一般的な Web サービス:
 * WebService 構成ツールを使用して既定の SAP wsconfig プロジェクト内のサービス URL を変更すると、次のエラーが発生します: パスの一部が見つかりません。

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Generic LDAP:
 * GLDAP コネクタは、AD LDS 内のすべての属性を認識しません。
 * LDAP ディレクトリ スキーマから UPN 属性が検出されない場合、ウィザードは中断します。
 * "objectclass" 属性が選択されていない場合、デルタ インポートは、フル インポート時には存在しない検出エラーで失敗します。
 * [パーティションと階層の構成] 構成ページに、その種類が Generic LDAP MA 内の Novel サーバーのパーティションと同じであるオブジェクトが表示されません  
。 RootDSE パーティションからのオブジェクトのみを表示していました。


* Generic SQL:
 * Generic SQL のウォーターマーク差分インポートで複数値属性がインポートされないバグを修正した。
 * 複数値属性の削除または追加された値をエクスポートしても、データ ソース内で値が削除または追加されることはありません。  


* Lotus Notes:
 * [完全な名前] フィールドはメタバースに正しく表示されますが、Notes にエクスポートすると、属性の値は Null または空白になります。
 * 証明者の重複エラーを修正しました。
 * Lotus Domino コネクタでデータのないオブジェクトと他のオブジェクトを同時に選択すると、フル インポートの実行中に検出エラーが発生します。
 * Lotus Domino コネクタでデルタ インポートを実行すると、実行の最後に、Microsoft.IdentityManagement.MA.LotusDomino.Service.exe サービスがアプリケーション エラーを返すことがあります。
 * グループ メンバーシップは全体的に問題なく動作します。ただし、エクスポートの実行時にメンバーシップからユーザーを削除しても、更新では成功したと表示されますが、ユーザーは実際に は Lotus Notes のメンバーシップから削除されていません。
 * Lotus MA の構成 GUI で、エクスポート モードとして “項目を末尾に追加する” ことを選択できようになりました。このモードでは、複数値属性のエクスポート時に、新しい項目は末尾に追加されます。
 * コネクタは、メール フォルダーと ID コンテナーからファイルを削除するために必要なロジックを追加します。
 * NAB メンバー間でのメンバーシップの削除は機能しません。
 * 値は、複数値属性から正常に削除する必要があります。

## <a name="111170"></a>1.1.117.0
リリース日: 2016 年 3

**新しいコネクタ**  
[Generic SQL コネクタ](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)の初回リリース。

**新機能:**

* Generic LDAP コネクタ:
  * Isode によるデルタ インポート対応になりました。
* Web サービス コネクタ:
  * csEntryChangeResult アクティビティと setImportErrorCode アクティビティが更新され、オブジェクト レベルのエラーを同期エンジンに返すことができるようになりました。
  * SAP6 テンプレートと SAP6User テンプレートが更新され、新しいオブジェクト レベルのエラー機能を利用できるようになりました。
* Lotus Domino コネクタ:
  * エクスポート用として、アドレス帳ごとに 1 人の証明者が必要です。 すべての証明に同じパスワードを利用できるようになり、管理が簡単になりました。

**修正された問題:**

* Generic LDAP コネクタ:
  * IBM Tivoli DS の場合、一部の参照属性が正しく検出されませんでした。
  * Open LDAP の場合、デルタ インポートのとき、文字列の始めと終わりの空白文字が切り詰められました。
  * Novell と NetIQ では、OU/コンテナー間でオブジェクトを移動すると同時にオブジェクトの名前を変更した場合、エクスポートに失敗していました。
* Web サービス コネクタ:
  * Web サービスで同じバインディングに対して複数のエンドポイントが存在するとき、コネクタはそのようなエンドポイントを正しく検出しませんでした。
* Lotus Domino コネクタ:
  * fullName 属性をメールイン データベースにエクスポートできませんでした。
  * 追加されたメンバーと削除されたメンバーの両方をグループからエクスポートすると、追加されたメンバーだけがエクスポートされました。
  * Notes ドキュメントが無効の場合 (isValid 属性が false に設定されている場合)、コネクタでエラーが発生します。

## <a name="older-releases"></a>以前のリリース
2016 年 3 月まで、コネクタはサポート トピックとしてリリースされていました。

**Generic LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597、2015 年 9 月
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549、2015 年 3 月
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534、2015 年 1 月
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419、2014 年 9 月
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082、2014 年 3 月

**Web サービス**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419、2014 年 9 月

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419、2014 年 9 月

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597、2015 年 9 月
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549、2015 年 3 月
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712、2014 年 8 月
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003、2014 年 2 月  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721、2013 年 10 月
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534、2013 年 8 月

## <a name="troubleshooting"></a>トラブルシューティング 

> [!NOTE]
> ECMA2 コネクタを使用して、Microsoft Identity Manager または AADConnect を更新する場合に適用されます。 

アップグレード時にコネクタ定義を更新する必要があります。そうしないと、アプリケーション イベント ログに、"Assembly version in AAD Connector configuration ("X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll""\(AAD コネクタ構成 ("X.X.XXX.X") のアセンブリ バージョンが、"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" の実際のバージョン ("X.X.XXX.X") より前のバージョンです\) という、警告 ID 6947 で始まるエラーが表示されます。

定義を更新するには、次の手順を実行します。
* コネクタ インスタンスのプロパティを開きます。
* [接続] をクリックし、[接続] タブに移動します。
  * コネクタ アカウントのパスワードを入力します。
* 各プロパティ タブを順番にクリックします。
  * このコネクタの種類に、[最新の情報に更新] ボタンが含まれた [パーティション] タブがある場合、そのタブで [最新の情報に更新] をクリックします。
* すべてのプロパティ タブにアクセスしたら、[OK] をクリックして変更を保存します。


## <a name="next-steps"></a>次の手順
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。
