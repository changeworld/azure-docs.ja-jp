<properties 
   pageTitle="Azure ゲスト OS に適用される更新プログラムの一覧 | Azure" 
   description="ゲスト OS リリースに適用される更新プログラムの一覧" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="03/18/2015"
   ms.author="adegeo"/>

# Azure ゲスト OS 

次の表に、Azure ゲスト OS に適用される Microsoft セキュリティ レスポンス センター (MSRC) の更新プログラムを示します。この記事を検索して、使用しているゲスト OS に特定の更新プログラムが適用されるかどうかを確認できます。更新プログラムは、それが導入された特定の[ファミリ][family-explain]内で常に繰り越されます。 


## 3 月のゲスト OS

| セキュリティ情報 ID | 親のサポート技術情報の記事   | 脆弱性の説明                                                   | ゲスト OS         | 最初に導入された日付 |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-018    | [3032359][3032359] | Internet Explorer 用の累積的なセキュリティ更新プログラム                             | 4.18、3.25、2.37 | 該当なし |
| MS15-019    | [3040297][3040297] | VBScript スクリプト エンジンの脆弱性により、リモートでコードが実行される | 4.18、3.25、2.37 | 該当なし |
| MS15-020    | [3041836][3041836] | Windows の脆弱性により、リモートでコードが実行される                 | 4.18、3.25、2.37 | 該当なし |
| MS15-021    | [3032323][3032323] | Adobe フォント ドライバーの脆弱性により、リモートでコードが実行される          | 4.18、3.25、2.37 | 該当なし |
| MS15-023    | [3034344][3034344] | カーネル モード ドライバーの脆弱性により、特権が昇格される     | 4.18、3.25、2.37 | 該当なし |
| MS15-024    | [3035132][3035132] | PNG 処理の脆弱性により、情報漏えいが起こる           | 4.18、3.25、2.37 | 該当なし |
| MS15-025    | [3038680][3038680] | Windows カーネルの脆弱性により、特権が昇格される         | 4.18、3.25、2.37 | 該当なし |
| MS15-027    | [3002657][3002657] | NETLOGON の脆弱性により、なりすましが行われる                               | 4.18、3.25、2.37 | 該当なし |
| MS15-029    | [3035126][3035126] | Windows フォト デコーダー コンポーネントの脆弱性により、情報の漏えいが起こる | 4.18、3.25、2.37 | 該当なし |

> [AZURE.NOTE] セキュリティ情報 MS15-031 は、アンインストールされているかのように表示される場合がありますが、このゲスト OS リリースには適用されません。

## 2 月のゲスト OS

| セキュリティ情報 ID | 親のサポート技術情報の記事   | 脆弱性の説明                                                   | ゲスト OS         | 最初に導入された日付 |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-009    | [3034682][3034682] | Internet Explorer 用の累積的なセキュリティ更新プログラム                                                   | 4.17、3.24、2.36 | 該当なし |
| MS15-010    | [3036220][3036220] | Windows の脆弱性により、リモートでコードが実行される        | 4.17、3.24、2.36 | 該当なし |
| MS15-011    | [3000483][3000483] | グループ ポリシーの脆弱性により、リモートでコードが実行される                         | 4.17、3.24、2.36 | 該当なし |
| MS15-014    | [3004361][3004361] | グループ ポリシーの脆弱性により、セキュリティ機能のバイパスが起こる                                | 4.17、3.24、2.36 | 該当なし |
| MS15-015    | [3031432][3031432] | Vulnerability in Microsoft Windows Could Allow Elevation of Privilege (Microsoft Windows の脆弱性により、特権が昇格される)                   | 4.17、3.24、2.36 | 該当なし |
| MS15-016    | [3029944][3029944] | Microsoft Graphics コンポーネントの脆弱性により、情報漏えいが起こる        | 4.17、3.24、2.36 | 該当なし |
| 該当なし         | [3004375][3004375] | Windows コマンド ライン監査の更新プログラム<p/>**注**: この更新プログラムはインストールされていますが、これを有効にするためのレジストリ キーが無効になっています。 | 4.17、3.24、2.36 | 該当なし |
 

## 1 月のゲスト OS

| セキュリティ情報 ID | 親のサポート技術情報の記事   | 脆弱性の説明                                                   | ゲスト OS         | 最初に導入された日付 |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-001    | [3023266][3023266]  | Windows Application Compatibility Cache の脆弱性により、特権が昇格される  | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-002    | [3020393][3020393]  | Windows Telnet サービスの脆弱性により、リモートでコードが実行される   | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-003    | [3021674][3021674]  | Windows User Profile Service の脆弱性により、特権が昇格される   | 4.16、3.23、2.35 | 2015 年 1 月 19 日    |
| MS15-004    | [3019978][3019978]  | Windows コンポーネントの脆弱性により、特権が昇格される      | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-005    | [3022777][3022777]  | Network Location Awareness Service の脆弱性により、セキュリティ機能のバイパスが起こる                    | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-006    | [3004365][3004365]  | Windows エラー報告の脆弱性により、セキュリティ機能のバイパスが起こる | 4.16、3.23、2.35 | 2015 年 1 月 19 日          |
| MS15-007    | [3014029][3014029]  | ネットワーク ポリシー サーバーの RADIUS 実装の脆弱性により、サービス拒否が起こる | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-008    | [3019215][3019215]  | Windows カーネル モード ドライバーの脆弱性により、特権が昇格される | 4.16、3.23、2.35 | 2015 年 1 月 19 日        |
| MS14-080    | [3008923][3008923]  | Internet Explorer 用の累積的なセキュリティ更新プログラム                            | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-002    | [3020393][3020393]  | Windows Telnet サービスの脆弱性により、リモートでコードが実行される   | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
| MS15-002    | [3020393][3020393]  | Windows Telnet サービスの脆弱性により、リモートでコードが実行される   | 4.16、3.23、2.35 | 2015 年 1 月 19 日           |
 
## 12 月のゲスト OS

| セキュリティ情報 ID | 親のサポート技術情報の記事   | 脆弱性の説明                                                   | ゲスト OS         | 最初に導入された日付 |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| 該当なし         | [3013776][3013776]  | System freezes when you use a domain account to start an application (ドメイン アカウントを使用してアプリケーションを起動すると、システムがフリーズする) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 | 
| 該当なし         | [3013043][3013043]  | File system data is corrupted on a Windows-based computer that has more than one NUMA node (複数の NUMA ノードがある Windows ベースのコンピューターで、ファイル システム データが破損する) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3012712][3012712]  | New data blocks initialize incorrectly when a differencing VHD is expanded (差分 VHD を拡張すると、新しいデータ ブロックが正しく初期化されない) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3004905][3004905]  | Windows Hyper-V improvement for Linux virtual machines that have file systems that are larger than 2 TB (2 TB を超えるファイル システムがある Linux 仮想マシンに対する Windows Hyper-V の機能強化) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3004394][3004394]  | December 2014 update for Windows Root Certificate Program in Windows (Windows での Windows ルート証明書プログラムに対する 2014 年 12 月の更新) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [2999323][2999323]  | The text for event ID 17 is changed (イベント ID 17 のテキストが変更される) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3013488][3013488]  | Long wait to reset WSUS server when you import CSA files in Windows Server 2012 R2 or Windows Server 2012 (Windows Server 2012 R2 または Windows Server 2012 で CSA ファイルをインポートするときに、WSUS サーバーをリセットするのに長時間待機する) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3012325][3012325]  | Windows APN database entries update for DIGI, Vodafone, and Telekom mobile operators in Windows 8.1 and Windows 8 (Windows 8.1 と Windows 8 での DIGI、Vodafone、および Telekom の各携帯電話会社向け Windows APN データベースのエントリの更新) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [3007054][3007054]  | PIN-protected printing option always shows when you print a document within a Windows Store application in Windows (Windows で Windows ストア アプリケーション内のドキュメントを印刷するときに、PIN で保護された印刷オプションが常に表示される) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [2999802][2999802]  | Solid lines instead of dotted lines are printed in Windows (点線ではなく実線が Windows で印刷される) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |
| 該当なし         | [2896881][2896881]  | Long logon time when you use the AddPrinterConnection VBScript command to map printers for users during logon process in Windows (Windows でのログオン プロセス中にユーザーのプリンターをマップする AddPrinterConnection VBScript コマンドを使用するとログオン時間が長い) | 4.15、3.22、2.34 | 2015 年 1 月 13 日 |




[3034682]: http://support.microsoft.com/kb/3034682
[3036220]: http://support.microsoft.com/kb/3036220
[3000483]: http://support.microsoft.com/kb/3000483
[3004361]: http://support.microsoft.com/kb/3004361
[3031432]: http://support.microsoft.com/kb/3031432
[3029944]: http://support.microsoft.com/kb/3029944
[3004375]: http://support.microsoft.com/kb/3004375

[3023266]: http://support.microsoft.com/kb/3023266
[3020393]: http://support.microsoft.com/kb/3020393
[3021674]: http://support.microsoft.com/kb/3021674
[3019978]: http://support.microsoft.com/kb/3019978
[3022777]: http://support.microsoft.com/kb/3022777
[3004365]: http://support.microsoft.com/kb/3004365
[3014029]: http://support.microsoft.com/kb/3014029
[3019215]: http://support.microsoft.com/kb/3019215
[3008923]: http://support.microsoft.com/kb/3008923
[3020393]: http://support.microsoft.com/kb/3020393

[3013776]: http://support.microsoft.com/kb/3013776
[3013043]: http://support.microsoft.com/kb/3013043
[3012712]: http://support.microsoft.com/kb/3012712
[3004905]: http://support.microsoft.com/kb/3004905
[3004394]: http://support.microsoft.com/kb/3004394
[2999323]: http://support.microsoft.com/kb/2999323
[3013488]: http://support.microsoft.com/kb/3013488
[3012325]: http://support.microsoft.com/kb/3012325
[3007054]: http://support.microsoft.com/kb/3007054
[2999802]: http://support.microsoft.com/kb/2999802
[2896881]: http://support.microsoft.com/kb/2896881

[3032359]: http://support.microsoft.com/kb/3032359
[3040297]: http://support.microsoft.com/kb/3040297
[3041836]: http://support.microsoft.com/kb/3041836
[3032323]: http://support.microsoft.com/kb/3032323
[3034344]: http://support.microsoft.com/kb/3034344
[3035132]: http://support.microsoft.com/kb/3035132
[3038680]: http://support.microsoft.com/kb/3038680
[3002657]: http://support.microsoft.com/kb/3002657
[3035126]: http://support.microsoft.com/kb/3035126


[archive]: https://msdn.microsoft.com/library/azure/dn391773.aspx
[family-explain]: cloud-services-guestos-update-matrix.md#guest-os-family-version-and-release-explanation


<!--HONumber=52-->