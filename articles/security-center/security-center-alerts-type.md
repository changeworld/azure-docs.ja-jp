---
title: "Azure Security Center の種類別のセキュリティの警告 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center で利用できるセキュリティの警告の種類についてわかりやすく説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4ddc691f4f3d53dbff2e55b7a97446427b93a8a4


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Azure Security Center の種類別のセキュリティの警告
このドキュメントでは、Azure Security Center で利用できるさまざまな種類のセキュリティの警告についてわかりやすく説明します。 警告を管理する方法の詳細については、「 [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」を参照してください。

> [!NOTE]
> 高度な検出を有効にするには、Azure Security Center Standard にアップグレードしてください。 90 日間の無料試用版が提供されています。 アップグレードするには、 [[セキュリティ ポリシー]](security-center-policies.md)の [価格レベル] を選択してください。 詳細については、 [価格のページ](https://azure.microsoft.com/pricing/details/security-center/) を参照してください。
> 
> 

## <a name="what-type-of-alerts-are-available"></a>使用できる警告の種類
Azure Security Center では、サイバー キル チェーンの各段階に応じた各種の警告が用意されています。 次の図は、これらの段階のいくつかに関連するさまざまな警告の例を部分的に示したものです。

![Kill chain](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**ターゲットと攻撃**

* 受信 RDP/SSH 攻撃
* アプリケーションと DDoS 攻撃 (WAF パートナー)
* 不正侵入検出 (NG ファイアウォール パートナー)

**インストールとエクスプロイト**

* 既知のマルウェア シグネチャ (AM パートナー)
* インメモリ マルウェアとエクスプロイトの試行
* 疑わしいプロセスの実行
* 検出を防ぐ回避行動
* 侵入拡大
* 内部偵察
* 疑わしい PowerShell アクティビティ

**侵害後**  

* 既知の悪意のある IP との通信 (データの搾取、遠隔操作)
* セキュリティの侵害されたリソースを使用した追加の攻撃の開始 (送信ポート スキャン RDP/SSH ブルート フォース攻撃、スパム)

さまざまな種類の攻撃が各段階に関連し、さまざまなサブシステムをターゲットとしています。 これらの段階の攻撃に対処するために、Security Center では警告に 3 つのカテゴリを設けています。

* 仮想マシンの動作分析 (VMBA)
* ネットワーク分析
* リソース分析

## <a name="virtual-machine-behavioral-analysis"></a>仮想マシンの動作分析
Azure Security Center は動作分析を使用し、(プロセス作成イベント、ログイン イベントなどの) 仮想マシンのイベント ログの分析に基づいて、セキュリティの侵害されたリソースを特定します。 また、他のシグナルとの間には、蔓延している攻撃の裏付けとなる兆候を確認できる相関関係が存在します。

> [!NOTE]
> Security Center の検出機能に関する詳細については、「 [Azure Security Center の検出機能](security-center-detection-capabilities.md)」を参照してください。
> 
> 

### <a name="crash-analysis"></a>クラッシュ分析
クラッシュ ダンプ メモリ分析は、従来のセキュリティ ソリューションを回避することができる高度なマルウェアの検出に使用される方法です。 さまざまな形式のマルウェアは、ディスクへの書き込みを行わないことや、ディスクに書き込まれたソフトウェア コンポーネントを暗号化することで、ウイルス対策製品によって検出される可能性を減らすよう試みます。 このことが、従来のマルウェア対策手法を使ったマルウェア検出を困難にしています。 しかし、マルウェアが機能するためにはメモリに痕跡を残す必要があるので、メモリ分析を使用すればそのようなマルウェアを検出することができます。

ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。 クラッシュは、マルウェア、一般的なアプリケーション、またはシステムの問題によって引き起こされる可能性があります。 Security Center では、クラッシュ ダンプでメモリを分析することによって、ソフトウェアの脆弱性の悪用や機密データへのアクセスに使用された手法を検出できます。また、侵入したコンピューターに密かに常駐するタイプの手法であっても検出が可能です。 Security Center バックエンドによって分析が実行されるため、この方法ではホストのパフォーマンスに対する影響が最小限に抑えられます。

次に示したのは、クラッシュ ダンプ分析の警告に共通するフィールドです。

* DUMPFILE (ダンプ ファイル): クラッシュ ダンプ ファイルの名前
* PROCESSNAME (プロセス名): クラッシュしているプロセスの名前
* PROCESSVERSION (プロセスのバージョン): クラッシュしているプロセスのバージョン

### <a name="shellcode-discovered"></a>シェルコードの検出
シェルコードは、マルウェアがソフトウェアの脆弱性を突破した後に実行されるペイロードです。 この警告は、悪質なペイロードに共通した振る舞いをする実行可能コードがクラッシュ ダンプ分析によって検出されたことを意味します。 悪意のないソフトウェアによる振る舞いである可能性もありますが、ソフトウェア開発の標準的な慣例からは逸脱しています。

この警告には、他にも次のフィールドがあります。

* ADDRESS (アドレス): メモリ内におけるシェルコードの場所

このタイプの警告の例を次に示します。

![Shellcode alert](./media/security-center-alerts-type/security-center-alerts-type-fig2.png) 

### <a name="module-hijacking-discovered"></a>モジュールのハイジャックの検出
Windows システムの共通の機能をソフトウェアから利用できるようにするために、Windows にはダイナミック リンク ライブラリ (DLL) が使用されています。 DLL ハイジャックは、マルウェアが DLL の読み込み順序を改変することによって行われます。このときメモリに悪質なペイロードが読み込まれ、任意のコードを実行できる状態となります。 この警告は、2 つの異なるパスから同様の名前のモジュールが読み込まれた (なおかつ読み込み元となったパスのいずれかが Windows システムの共通のバイナリ ファイルの格納場所である) ことをクラッシュ ダンプ分析が検出したことを意味します。

悪意からではなく、インストルメント化や Windows OS の拡張、Windows アプリケーションの拡張といった目的で、通常のソフトウェア開発者が DLL の読み込み順序を変更することも皆無ではありません。 DLL 読み込み順序に対する悪意のある変更と、無害である可能性の高い変更とを判別しやすいように、Azure Security Center では、読み込まれているモジュールに疑わしい特徴が見られるかどうかをチェックします。 このチェックの結果は、警告の "SIGNATURE (シグネチャ)" フィールドに表示され、警告の重大度、説明、修正手順に反映されます。 ハイジャック モジュールが悪質なものであるかどうかについては、ディスク上のハイジャック モジュールのコピーを分析する (たとえばファイルのデジタル署名の検証、ウイルス対策スキャンを実行する) ことで、さらに詳しい情報を得ることができます。

前出の「シェルコードの検出」のセクションで取り上げた共通のフィールドに加え、この警告には次のフィールドがあります。

* SIGNATURE (シグネチャ): ハイジャック モジュールが、疑わしい動作の特徴と一致しているかどうかを示します。
* HIJACKEDMODULE (ハイジャックされたモジュール): ハイジャックされた Windows システム モジュールの名前。
* HIJACKEDMODULEPATH (ハイジャックされたモジュールのパス): ハイジャックされた Windows システム モジュールのパス。
* HIJACKINGMODULEPATH (ハイジャック モジュールのパス): ハイジャック モジュールのパス。

このタイプの警告の例を次に示します。

![Module hijacking alert](./media/security-center-alerts-type/security-center-alerts-type-fig3.png) 

### <a name="masquerading-windows-module-detected"></a>Windows モジュールのなりすましの検出
マルウェアには、Windows システムに共通するバイナリ名 (SVCHOST.EXE など) やモジュール名 (NTDLL.DLL など) が使用されている場合があります。悪質なソフトウェアを "紛れ込ませる" ことで、その正体をシステム管理者に見破られないようにするためです。 この警告は、Windows システムのモジュール名と同じ名前のモジュールがクラッシュ ダンプ ファイルに含まれているものの、それ以外の点は一般的な Windows モジュールの基準をそのモジュールは満たしていないことが、クラッシュ ダンプ分析によって検出されたことを意味します。 そのモジュールが悪質なものであるかどうかについては、ディスク上のなりすましモジュールのコピーを分析することによって、さらに詳しい情報を得ることができます。 実行される分析の例を次に示します。

* 問題になっているファイルが正規ソフトウェア パッケージの一部として出荷されていること確認する
* ファイルのデジタル署名を確認する
* ファイルに対してウイルス対策スキャンを実行する

前出の「シェルコードの検出」のセクションで取り上げた共通のフィールドに加え、この警告には次のフィールドもあります。

* DETAILS (詳細): モジュールのメタデータが有効であるかどうかと、モジュールがシステム パスから読み込まれたかどうかを表します。
* NAME (名前): Windows のモジュールになりすましているモジュールの名前。
* PATH (パス): Windows のモジュールになりすましているモジュールのパス。

また、この警告にはモジュールの PE ヘッダーから特定のフィールド ("CHECKSUM"、"TIMESTAMP" など) が抽出されて表示されます。 これらのフィールドは、モジュールに存在する場合にのみ表示されます。 これらのフィールドの詳細については、「 [Microsoft PE and COFF 仕様](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) 」を参照してください。

このタイプの警告の例を次に示します。

![Masquerading Windows alert](./media/security-center-alerts-type/security-center-alerts-type-fig4.png) 

### <a name="modified-system-binary-discovered"></a>システム バイナリの改変の検出
マルウェアは、侵入したシステム上でこっそりデータにアクセスしたり人目に付かないよう常駐したりするために、主要なシステム バイナリを改変する場合があります。 この警告は、Windows OS の主要なバイナリがメモリ内またはディスク上で改変されたことがクラッシュ ダンプ分析によって検出されたことを意味します。
悪意からではなく、迂回やアプリケーションの互換性といった目的で、通常のソフトウェア開発者がメモリ内のシステム モジュールに変更を加えることも皆無ではありません。 悪質なモジュールとそうでないモジュールを判別しやすいよう、Azure Security Center は、改変されたモジュールに疑わしい特徴が見られるかどうかをチェックします。 このチェックの結果は、警告の重大度、説明、修正手順に反映されます。

前出の「シェルコードの検出」のセクションで取り上げた共通のフィールドに加え、この警告には次のフィールドもあります。

* MODULENAME (モジュール名): 改変されたシステム バイナリの名前。
* MODULEVERSION (モジュールのバージョン): 改変されたシステム バイナリのバージョン。

このタイプの警告の例を次に示します。

![System binary alert](./media/security-center-alerts-type/security-center-alerts-type-fig5.png) 

### <a name="suspicious-process-executed"></a>疑わしいプロセスの実行
Security Center は、ターゲット仮想マシンで実行されている疑わしいプロセスを識別し、警告をトリガーします。 検出は、特定の名前を探して行われるのではなく、パラメーターによって行われます。このため、攻撃者が実行できるものに名前を変更したとしても、Security Center は依然として疑わしいプロセスを検出できます。

このタイプの警告の例を次に示します。

![Suspicious process alert](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>複数のドメイン アカウントのクエリ
Security Center は、ドメイン アカウントに対しクエリが複数回試行されていることを検出できます。このような試行は通常、ネットワーク偵察時に攻撃者が実行することです。 攻撃者は、この手法を使ってドメインに問い合わせ、ユーザー、ドメインの管理者アカウント、ドメイン コントローラーであるコンピューターを特定できるほか、他のドメインとの潜在的なドメイン信頼関係も特定できます。

このタイプの警告の例を次に示します。

![Multiple domains account alert](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>ネットワーク分析
Security Center のネットワーク脅威検出は、Azure IPFIX (Internet Protocol Flow Information Export) トラフィックからセキュリティ情報を自動的に収集することによって機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。

### <a name="suspicious-outgoing-traffic-detected"></a>疑わしい送信トラフィックの検出
ネットワーク デバイスは、他の種類のシステムとほぼ同じように検出、プロファイリングされることがあります。 通常、攻撃者は初めにポート スキャンまたはポート スイープを行います。 次の例では、VM からの疑わしい SSH トラフィックが存在しており、外部リソースに対する SSH ブルート フォース攻撃またはポート スイープ攻撃が行われている可能性があります。

![Suspicious outgoing traffic alert](./media/security-center-alerts-type/security-center-alerts-type-fig8.png) 

この警告では、攻撃の開始に使用されたリソース、セキュリティが侵害されたコンピューター、検出の日時、使用されたプロトコルとポートを特定できる情報が表示されます。 このブレードでは、問題を軽減するために使用できる修復手順の一覧も表示されます。

### <a name="network-communication-with-a-malicious-machine"></a>悪意のあるコンピューターとのネットワーク通信
Azure Security Center は、悪意のある IP アドレス (多くの場合、コマンド アンド コントロール センター) と通信している、セキュリティが侵害されたコンピューターを、Microsoft 脅威インテリジェンス フィードを利用して検出できます。 この例では、Azure Security Center によって、Pony Loader マルウェア ( [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)とも呼ばれる) を使用した通信が行われたことが検出されました。

![network communication alert](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

この警告では、この攻撃の開始に使用されたリソース、攻撃されたリソース、被害を受けた IP、攻撃者の IP、検出の日時を特定できる情報が表示されます。

[AZURE.NOTE] ライブ IP アドレスは、プライバシー保護の目的でこのスクリーンショットからは削除しています。

### <a name="possible-outgoing-denialofservice-attack-detected"></a>送信サービス拒否攻撃の可能性の検出
ある仮想マシンによって送信された異常なネットワーク トラフィックが原因で、Security Center が潜在的なサービス拒否型の攻撃をトリガーする場合があります。

このタイプの警告の例を次に示します。

![Outgoing DOS](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>リソース分析
Security Center リソース分析は、 [Azure SQL Db 脅威の検出](../sql-database/sql-database-threat-detection-get-started.md) の機能との統合など、PaaS サービスに重点を置いています。 これらの領域の分析結果に基づいて、Security Center は警告に関連するリソースをトリガーします。

### <a name="potential-sql-injection"></a>SQL インジェクションの可能性
SQL インジェクションとは、後で SQL Server のインスタンスに渡して解析と実行の対象とする文字列に、悪意のあるコードが挿入される攻撃です。 SQL ステートメントを構成するすべてのプロシージャにおいて、挿入に対する脆弱性を確認する必要があります。SQL Server は、受け取った有効な構文のクエリをすべて実行してしまうからです。 SQL 脅威の検出では、機械学習、動作分析、異常検出を使用して、Azure SQL データベースで発生しているおそれのある疑わしいイベントを特定します。 次に例を示します。

* 以前の従業員によるデータベース アクセスの試行 
* SQL インジェクション攻撃 
* 在宅中のユーザーからの運用データベースへの不自然なアクセス

![Potential SQL Injection alert](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

この警告では、攻撃を受けたリソース、検出の日時、攻撃の状態を特定する情報が表示され、さらに詳しい調査手順へのリンクも表示されます。

### <a name="vulnerability-to-sql-injection"></a>SQL インジェクションにつながる脆弱性
この警告は、SQL インジェクション攻撃につながる潜在的な脆弱性を示すアプリケーション エラーがデータベースで検出された場合にトリガーされます。

![Potential SQL Injection alert](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>不明な場所からの不自然なアクセス
この警告は、最後の期間に見られなかった不明な IP アドレスからのアクセスがサーバーで検出された場合にトリガーされます。

![Unusual access alert](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center のさまざまな種類のセキュリティの警告について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ インシデントの処理](security-center-incident.md)
* [Azure Security Center の検出機能](security-center-detection-capabilities.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。




<!--HONumber=Nov16_HO2-->


