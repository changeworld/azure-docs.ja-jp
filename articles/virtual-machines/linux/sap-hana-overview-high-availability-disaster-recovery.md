---
title: "SAP HANA on Azure (L インスタンス) の高可用性と障害復旧 | Microsoft Docs"
description: "高可用性を確立し、SAP HANA on Azure (L インスタンス) の障害復旧を計画します。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 047eeddecc33b637a4f0dec3aa591c42ada83706
ms.openlocfilehash: 9e988659dac046067c53765fc1ddc90ee152e27f


---
# <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) の高可用性と障害復旧

高可用性 (HA) と障害復旧 (DR) は、ミッション クリティカルな SAP HANA on Azure (L インスタンス) サーバーを実行する場合の重要な要素です。 正しい HA/DR 戦略を適切に構築および実装するには、SAP、システム インテグレーター、および Microsoft と協力することが重要です。 環境に固有の目標復旧時点 (RPO) と目標復旧時間 (RTO) のような重要な考慮事項を検討する必要があります。

## <a name="high-availability"></a>高可用性

Microsoft では、次のような &quot;すぐに利用できる&quot; SAP HANA 高可用性手法をサポートしています。

- **ストレージ レプリケーション:** すべてのデータを別の場所 (同じデータ センター内、または別のデータ センター内) にレプリケートする、ストレージ システム自体の機能です。 SAP HANA は、この方法とは別に動作します。
- **システム レプリケーション:** 別の SAP HANA システムへの SAP HANA のすべてのデータのレプリケーション。 一定間隔でのデータのレプリケーションによって、RTO が最小化されます。 SAP HANA では、非同期、メモリ内同期、および同期 (同じデータ センター内または 100 km 未満の距離のデータ センター内の SAP HANA システムだけに推奨します) の各モードがサポートされています。
- **ホストの自動フェールオーバー:**&1; つまたは複数のスタンバイ SAP HANA ノードが &quot;スケールアウト&quot; モードで構成されていて、マスター ノードが使用できなくなると、SAP HANA が自動的に別のノードに &quot;フェールオーバー&quot; されます。

SAP HANA の高可用性の詳細については、以下の SAP 情報を参照してください。

- [SAP HANA の高可用性のホワイトペーパー](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理ガイド](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA システム レプリケーションに関する SAP Academy ビデオ](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP サポート ノート #1999880 - SAP HANA システム レプリケーションに関する FAQ](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP サポート ノート #2165547 - SAP HANA システム レプリケーション環境での SAP HANA のバックアップと復元](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP サポート ノート #1984882 - 最小/ゼロ ダウンタイムでのハードウェア交換のための SAP HANA システム レプリケーションの使用](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>障害復旧

SAP HANA on Azure (L インスタンス) は、1 つの地政学的リージョン内の&2; つの Azure リージョンで提供されます。 2 つの異なるリージョンの&2; つの L インスタンス スタンプ間には、障害復旧時にデータをレプリケートするための直接ネットワーク接続があります。 データのレプリケーションは、ストレージ インフラストラクチャ ベースです。 データのレプリケーションは、既定では行われません。お客様が障害復旧を指定した構成の場合に行われます。

ただし、障害復旧を利用するには、2 つの異なる Azure リージョンへのそれぞれのネットワーク接続の設計を開始する必要があります。 つまり、メイン Azure リージョンでオンプレミスから接続している ExpressRoute 回線と、オンプレミスから DR リージョンに接続しているもう&1; つの回線が必要になります。 この方法では、MSEE の場所を含む Azure リージョン全体で問題が発生した状況に対処できます。

2 つ目の方法では、理想的には、いずれかのリージョンの SAP HANA on Azure (L インスタンス) に接続するすべての Azure VNet を、これらの ExpressRoute 回線の両方に接続する必要があります。 そうすれば、オンプレミスの場所を Azure と接続する&1; つの MSEE の場所だけが動作時間外になった場合に対処できます。

次の図は、障害復旧に最適な構成を示しています。

![障害復旧に最適な構成](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

ネットワークの DR 構成に最適なケースでは、オンプレミスから&2; つの異なる Azure リージョンへの ExpressRoute 回線が&2; つあります。 1 つの回線は、実稼働インスタンスを実行しているリージョン #1 につながっています。 2 つ目の ExpressRoute 回線は、いくつかの非実稼働 HANA インスタンスを実行しているリージョン #2 につながっています (このことは、MSEE と L インスタンス スタンプを含む Azure リージョン全体が切断される場合に重要です)。

2 つ目の方法では、すべての VNet それぞれが、SAP HANA on Azure (L インスタンス) に接続している異なる ExpressRoute 回線に接続されます。 後で説明するように、MSEE の障害が発生している場所をバイパスしたり、障害復旧の RPO を短縮したりすることができます。

DR セットアップの次の要件は、以下のとおりです。

- DR リージョンで指定およびデプロイされた製品 SKU と同じサイズの SAP HANA on Azure (L インスタンス) SKU が必要です。 これらのインスタンスは、テスト、サンドボックス、または QA HANA インスタンスを実行するために使用できます。
- さらに、必要に応じて、DR サイトで回復する SAP HANA on Azure (L インスタンス) SKU ごとに DR プロファイルを指定する必要があります。 こうすることで、ストレージ ボリュームが割り当てられます。これらのストレージ ボリュームは、実稼働リージョンから DR リージョンへのストレージ レプリケーションのターゲットになります。

これがすべてセットアップされたら、ストレージ レプリケーションを開始する必要があります。 SAP HANA on Azure (L インスタンス) に使用されるストレージ インフラストラクチャで、ストレージ レプリケーションの基盤となるのは、ストレージ スナップショットです。 DR レプリケーションを開始するには、以下を実行する必要があります。

- 前に説明した、ブート LUN のスナップショット。
- 前に説明した、HANA 関連ボリュームのスナップショット。

これらのスナップショットの実行後、DR リージョンの DR プロファイルに関連付けられたボリュームで、ボリュームの初期レプリカがシード処理されます。

その後&1; 時間ごとに、最新のストレージ スナップショットを利用して、その間にストレージ ボリュームで開発された差分がレプリケートされます。

この構成で達成される RPO は、60 ～ 90 分です。 DR のケースでより良い RPO を達成するには、SAP HANA on Azure (L インスタンス) の HANA トランザクション ログ バックアップを他の Azure リージョンにコピーする必要があります。 そのためには、次の操作を行います。

- HANA トランザクション ログを、できるだけ頻繁に /hana/log/backup にバックアップします。
- 次に、トランザクション ログのバックアップが終了したら、それを Azure VM にコピーします。この VM は、SAP HANA on Azure (L インスタンス) サーバーに接続されている VNet 内にあります。
- その VM から、DR リージョンの VNet 内にある VM にバックアップをコピーします。
- そのリージョンの VM にトランザクション ログ バックアップを保持します。

障害が発生した場合は、DR プロファイルが実際のサーバーにデプロイされた後で、トランザクション ログ バックアップを VM から DR リージョンでプライマリになった SAP HANA on Azure (L インスタンス) にコピーし、これらのバックアップを復元します。 これが可能なのは、DR ディスクでの HANA の状態が HANA スナップショットの状態であるためです。 これが、トランザクション ログ バックアップを今後復元する際のオフセット ポイントになります。

## <a name="backup-and-restore"></a>バックアップと復元

データベースを運用するうえで最も重要なことの&1; つは、さまざまな種類の危機的な事象からデータベースを確実に保護することです。 こうした事象の原因には、自然災害から単純なユーザー エラーまで、あらゆることが考えられます。

データベースをバックアップし、任意の時点 (たとえば、だれかが重要なデータを削除してしまう前など) に復元できるようにすることで、中断が発生する前の状態にできるだけ近い状態への復元が可能になります。

最良の結果を得るには、次の&2; 種類のバックアップを実行する必要があります。

- データベースのバックアップ
- トランザクション ログのバックアップ

アプリケーション レベルで実行されるデータベースの完全バックアップに加え、ストレージ スナップショットによるバックアップを実行することによって、さらに万全を期すことができます。 データベースを復元するには、ログ バックアップを実行すること (および、既にコミットされたトランザクションをログから削除すること) も重要です。

SAP HANA on Azure (L インスタンス) には、次の&2; つのバックアップおよび復元オプションがあります。

- DIY (自分で実行する)。 計算して十分なディスク領域を確保した後、ディスク バックアップ方法を使用して、(これらのディスクへの) データベースとログの完全バックアップを実行します。 時間の経過と共に、バックアップは Azure Storage にコピーされます (実質的には無制限の標準ストレージで Azure ベースのファイル サーバーをセットアップした後)。または、Azure Backup コンテナーまたは Azure コールド ストレージを使用します。 別のオプションでは、Commvault などのサード パーティ製データ保護ツールを使用して、バックアップが Azure 標準ストレージにコピーされた後にそのバックアップを保存できます。 DIY バックアップ オプションは、コンプライアンスと監査用に長期間保存する必要があるデータにとって必要な場合もあります。
- SAP HANA on Azure (L インスタンス) の基になるインフラストラクチャによって提供されるバックアップと復元の機能を使用します。 このオプションはバックアップのニーズに応えており、手動バックアップがほぼ不要になります (データのバックアップがコンプライアンスのために必要な場合を除く)。 このセクションの残りの部分では、HANA L インスタンスで提供されるバックアップと復元の機能について説明します。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) のストレージ スナップショットの使用

SAP HANA on Azure (L インスタンス) の基になっているストレージ インフラストラクチャでは、ボリュームのストレージ スナップショットの概念がサポートされています。 特定のボリュームのバックアップと復元の両方がサポートされますが、以下の点を考慮する必要があります。

- データベース バックアップの代わりに、ストレージ ボリューム スナップショットが頻繁に作成されます。
- ストレージ スナップショットの実行前に、ストレージ スナップショットによって SAP HANA スナップショットが開始されます。 この HANA スナップショットは、ストレージ スナップショットの復旧後に、最終的なログ復元を設定するポイントになります。
- ストレージ スナップショットが正常に実行された時点で、SAP HANA スナップショットは削除されます。
- ログ バックアップが頻繁に作成され、ログ バックアップ ボリュームまたは Azure に保存されます。
- データベースを特定の時点に復元する必要がある場合は、Microsoft Azure サポート (運用停止) または SAP HANA on Azure サービス管理に対して、特定のストレージ スナップショットへの復元の要求が作成されます (たとえば、サンドボックス システムの元の状態への計画的な復元)。
- ストレージ スナップショットに含まれている SAP HANA スナップショットは、ストレージ スナップショットの作成後に実行および保存されたログ バックアップを適用するためのオフセット ポイントです。
- これらのログ バックアップは、データベースを特定の時点に復元するために作成されます。

backup\_name を指定すると、次のボリュームのスナップショットが作成されます。

- hana/data
- hana/log
- hana/log\_backup (バックアップとして hana/log にマウントされます)
- hana/shared

### <a name="storage-snapshot-considerations"></a>ストレージ スナップショットに関する考慮事項

>[!NOTE] 
>ストレージ スナップショットは、追加の記憶域スペースを割り当てる必要があるため、無料では提供され "_ません_"。

SAP HANA on Azure (L インスタンス) のストレージ スナップショットに固有のメカニズムは、次のとおりです。

- 作成時点での特定のストレージ スナップショットは、ごくわずかな記憶域しか使用しません。
- データの内容は変化し、ストレージ ボリュームで SAP HANA データ ファイルの内容が変化するため、スナップショットは元のブロックの内容を保存する必要があります。
- ストレージ スナップショットのサイズは増加します。 スナップショットの存在期間が長くなるほど、ストレージ スナップショットが大きくなります。
- ストレージ スナップショットの有効期間に SAP HANA データベース ボリュームに対する変更が多くなるほど、ストレージ スナップショットの領域の消費量が大きくなります。

SAP HANA on Azure (L インスタンス) には、SAP HANA のデータおよびログ ボリューム用の固定ボリューム サイズがあります。 これらのボリュームのスナップショットを実行すると、ボリューム領域が消費されるため、ユーザーの責任において、(SAP HANA on Azure L インスタンス プロセス内で) ストレージ スナップショットをスケジュールする必要があります。

以降のセクションでは、これらのスナップショットの実行に関して、次のような一般的な推奨事項も含めて説明します。

- ハードウェアはボリュームごとに 255 個のスナップショットを保持できますが、この数よりも十分に少なくしておくことを強くお勧めします。
- 空き領域を監視し、ストレージ スナップショットの実行前に空き容量を知っておく必要があります。
- 空き領域に基づいて、ストレージ スナップショットの数を減らします。 保持されるスナップショットの数を減らすか、ボリュームを拡張する必要がある場合があります (追加ストレージは 1 TB 単位で指定できます)。
- システム移行ツールを使って SAP HANA にデータを移動するような操作 (R3Load で、または SAP HANA データベースをバックアップから復元して) を行っている間は、ストレージ スナップショットを実行しないことを強くお勧めします  (通常、システムの移行は新しい SAP HANA システムに対して行われるため、その場合にはストレージ スナップショットを実行する必要がありません)。
- SAP HANA テーブルの大規模な再編成中は、できるだけストレージ スナップショットを避けてください。
- また、ストレージ スナップショットは、SAP HANA on Azure (L インスタンス) の DR 機能を利用するための前提条件です。

### <a name="setting-up-storage-snapshots"></a>ストレージ スナップショットのセットアップ

1. HANA L インスタンス サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。

2. /etc/ssh/ssh\_config を変更して、_MACs hmac-sha1_ という行を追加します。

3. 実行している各 SAP HANA インスタンスのマスター ノードで、SAP HANA バックアップ ユーザー アカウントを作成します (該当する場合)。

4. すべての HANA L インスタンス サーバーに、SAP HANA HDB クライアントをインストールする必要があります。

5. スナップショットの作成を制御する基底ストレージ インフラストラクチャにアクセスするために、各リージョンの最初の HANA L インスタンス サーバーで公開キーを作成する必要があります。

6. スクリプト azure\_hana\_backup.pl を、/scripts から SAP HANA インストールの **hdbsql** の場所にコピーします。

7. HANABackupDetails.txt ファイルを、/scripts から Perl スクリプトと同じ場所にコピーします。

8. 適切な顧客仕様での必要に応じて、HANABackupDetails.txt ファイルを変更します。

### <a name="detailed-storage-snapshot-steps"></a>詳細なストレージ スナップショットの手順

**手順 1: SAP HANA HDBClient をインストールする**

SAP HANA on Azure (L インスタンス) にインストールされている Linux には、バックアップと障害復旧の目的で SAP HANA ストレージ スナップショットを実行するために必要なフォルダーとスクリプトが含まれています。 ただし、SAP HANA のインストール中に、ユーザーが SAP HANA HDBclient をインストールする必要があります   (Microsoft は、HDBclient も SAP HANA もインストールしません)。

**手順 2: /etc/ssh/ssh\_config を変更する**

以下に示すように、_MACs hmac-sha1_ 行を追加して /etc/ssh/ssh\_config を変更します。
以下のセクションを編集して、**MACs hmac-sha1** を追加します。
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

**手順 3: 公開キーを作成する**

各 Azure リージョンの最初の SAP HANA on Azure (L インスタンス) サーバーで、スナップショットの作成を可能にするストレージ インフラストラクチャにアクセスするために使用される公開キーを作成する必要があります。 公開キーを使用すると、ストレージへのログインにパスワードが不要になり、パスワード資格情報を保持しなくて済みます。 公開キーを生成するには、HANA L インスタンス サーバーの Linux で、以下のコマンドを実行します。
```
  ssh-keygen –t dsa –b 1024
```
新しい場所は _/root/.ssh/id\_dsa.pub_ です。実際のパスフレーズを入力 "_しないで_" ください。そうしないと、ログインするたびに、続行する前にパスフレーズの入力が要求されるようになります。 代わりに、**Enter** キーを&2; 回押して、ログイン時のパスフレーズ入力要求を削除します。

フォルダーを /root/.ssh/ に変更し、**ls** コマンドを実行して、公開キーが期待どおりに修正されたことを確認します。キーが存在する場合は、次のコマンドを実行すると公開キーがコピーされ、その後、文字列全体がコピーされます。

![このコマンドを実行すると公開キーがコピーされる](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

この時点で、SAP HANA on Azure サービス管理に連絡し、キーを提供します。 サービス管理では、公開キーを使用して、基になるストレージ インフラストラクチャに公開キーを登録します。

**手順 4: SAP HANA ユーザー アカウントを作成する**

バックアップのために、SAP HANA Studio 内で SAP HANA ユーザー アカウントを作成します。 このアカウントには、BACKUP ADMIN と CATALOG READ という特権を設定する必要があります。 この例では、SCADMIN というユーザー アカウントが作成されます。

![HANA Studio でのユーザーの作成](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

**手順 5: SAPA HANA ユーザー アカウントを承認する**

SAPA HANA ユーザー アカウントを承認します (スクリプトで使用するのに、スクリプトが実行されるたびに承認が要求されないようにするため)。 SAP HANA コマンド **hdbuserstore** を使用すると、1 つ以上の SAP HANA ノードに格納される SAP HANA ユーザー キーの作成が可能になり、ユーザーは、この後で説明するスクリプト プロセス内からパスワードを管理しなくても SAP HANA にアクセスできるようになります。

>[!IMPORTANT]
>次のコマンドは、_root_ として実行する必要があります。 そうしないと、スクリプトは正しく動作しません。

**hdbuserstore** コマンドは、次のように入力します。

![hdbuserstore コマンドが入力される ](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

次の例では、ユーザーは SCADMIN01、ホスト名は lhanad01 であるため、コマンドは次のようになります。
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
スケールアウトの HANA インスタンスでは、すべてのスクリプトを&1; つのサーバーから管理することを選択します。 この例では、SCADMIN01 の SAP HANA キーを、ホストごとに、キーに関連付けられているホストが反映されるように変更する必要があります (つまり、SAP HANA バックアップ アカウントには、HANA DB **lhanad** のインスタンス番号が追加されます)。 キーは、割り当て先のホストに対する管理特権を持つ必要があります。また、スケールアウトのためのバックアップ ユーザーは、すべての SAP HANA インスタンスへのアクセス権を持つ必要があります。
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

**手順 6: /scripts フォルダーからアイテムをコピーする**

/scripts フォルダーから次のアイテム (インストールのゴールド イメージに含まれています) を **hdbsql** の作業ディレクトリにコピーします。 現在の HANA インストールでは、/hana/shared/D01/exe/linuxx86\_64/hdb です。
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
また、スケールアウトまたは OLAP を実行している場合は、次のアイテムをコピーします。
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
HANABackupCustomerDetails.txt ファイルは、スケールアップ デプロイの場合、次のように変更できます。 これは、基本的には、ストレージ スナップショットを実行するスクリプトのための管理および構成ファイルです。 インスタンスがデプロイされたときに、SAP HANA on Azure サービス管理から "_ストレージ バックアップ名_" と "_ストレージ IP アドレス_" を受け取っているはずです。 どの変数も、シーケンス、順序、またはスペースを変更することは "_できません_"。変更すると、スクリプトが正常に動作しなくなります。

スケールアップ デプロイの場合、構成ファイルは次のようになります。
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
スケールアウト構成の場合、HANABackupCustomerDetailsBW.txt ファイルは次のようになります。
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
実際の HANA ストレージ スナップショット スクリプトでは、現在、Node 1 の詳細だけが使用される点に注意してください。しかし、マスター バックアップ ノードを変更しなくてはならなくなった場合に、Node 1 の詳細を変更すれば他のどのノードでも代替できることが既に確認できているようにするために、すべての HANA ノードとの間のアクセスをテストしておくことをお勧めします。

構成ファイル内の構成が正しいことや、HANA インスタンスへの接続が適切であることを確認するには、スケールアップ構成の場合は以下の一覧の最初のスクリプトを実行し (SAP ワークロードとは別に)、スケールアウト構成の場合は一覧の&2; つ目のスクリプトを実行する必要があります。
```
testHANAConnection.pl
```
スケールアウト構成では、次のとおりです。
```
testHANAConnectionBW.pl
```
マスター HANA インスタンスが、必要なすべての HANA サーバーにアクセスできることを確認します。 スクリプトへのパラメーターはありませんが、スクリプトを正しく実行するには適切な HANABackupCustomerDetails/HANABackupCustomerDetailsBW ファイルを作成する必要があります。 シェル コマンド エラー コードしか返されないため、スクリプトで個々のすべてのインスタンスをエラー チェックすることはできませんが、二重チェックのためのいくつかの有益なコメントをスクリプトから得ることができます。

スクリプトを実行するには、次の手順を実行します。
```
 ./testHANAConnection.pl
```
このスクリプトは、HANA インスタンスの状態の取得に成功した場合、HANA 接続が成功したというメッセージを出力します。

さらに、マスター HANA インスタンス サーバーがストレージにログインできることを確認するためのスクリプトがもう&1; 種類あります。 azure\_hana\_backup(\_bw).pl スクリプトを実行する前に次のスクリプトを実行することが必要です。 ボリュームにスナップショットが存在しない場合は、スナップショットが含まれていないボリュームと、スナップショットの詳細の取得時の ssh エラーとの違いを判断することができません。  このため、次のスクリプトの実行では、2 つのステップが実行されます。

- ストレージ コンソールにアクセスできることを確認する。
- HANA インスタンスでボリュームごとにテスト (ダミー) スナップショットを作成する。

このため、HANA インスタンスが引数として含まれます。 ここでも、エラー チェックはストレージの接続に関連するために行うことができませんが、実行が失敗した場合に役立つヒントをスクリプトから得ることができます。

スクリプトは、次のように実行されます。
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
または
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
このスクリプトは、ユーザーが所有するサーバー インスタンスによって使用される LUN を中心にして編成されたデプロイ済みストレージ テナントにユーザーが適切にログインできるというメッセージも出力します。

最初のストレージ スナップショット ベースのバックアップを実行する前に、これらのスクリプトを実行して、構成が正しいことを確認する必要があります。

これらのスクリプトの実行後、次を実行して、スナップショットを削除する必要があります。
```
./removeTestStorageSnapshot.pl <hana instance>
```
または
```
./removeTestStorageSnapshot.pl <hana instance>
```

**手順 7: オンデマンドのスナップショットを実行する**

スケールアップ構成では、次のスクリプトを実行することによって、以下で説明するようにオンデマンドのスナップショット (および cron を使用した定期スナップショットのスケジュール) を実行します。
```
./azure_hana_backup.pl lhanad01 customer 20
```
また、スケールアウト構成の場合は、次のように実行します。
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
スケールアウトのスクリプトは、SAP HANA またはストレージ スナップショットの作成に進む前に、すべての HANA サーバーにアクセスできることと、すべての HANA インスタンスが適切な状態を返すことを確認するために、いくつかの追加のチェックを行います。

以下の引数は必須です。

- バックアップを要求する HANA インスタンス。
- ストレージ スナップショットのスナップショット プレフィックス。
- 特定のプレフィックスで保持するスナップショットの数。
```
./azure_hana_backup.pl lhanad01 customer 20
```
スクリプトを実行すると、次の&3; つのフェーズで、ストレージ スナップショットが作成されます。

- HANA スナップショットを実行する。
- ストレージ スナップショットを実行する。
- HANA スナップショットを削除する。

スクリプトを、コピー先の HDB 実行可能フォルダーから呼び出して実行します。 これにより、少なくとも以下のボリュームがバックアップされますが、ボリューム名内に明示的な SAP HANA インスタンス名を含むボリュームもバックアップされます。
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
保有期間は、スクリプト実行時にパラメーターとして指定されたスナップショットの数 (上の例では 20) で厳密に管理されます。 そのため、その期間は、実行の期間と、スクリプトの呼び出し内のスナップショット数との相関関係にあります。 保持されるスナップショットの数が、スクリプトの呼び出しでパラメーターとして指定された数を超える場合は、新しいスナップショットの実行前に、このラベル (上の例では _custom_) の最も古いストレージ スナップショットが削除されます。 つまり、呼び出しの最後のパラメーターとして指定した数が、スナップショットの数を制御するために使用できる数になります。

>[!NOTE] 
>ラベルを変更するとすぐに、カウントが再開されます。

マルチノード環境のスナップショットを作成している場合は、SAP HANA on Azure サービス管理によって提供される HANA インスタンス名を引数として含める必要があります。 単一ノード環境では SAP HANA on Azure (L インスタンス) ユニットの名前で十分ですが、引き続き HANA インスタンス名をお勧めします。

また、同じスクリプトを使用して boot volumes\luns をバックアップする機能が用意されています。 cron でブートの毎週または毎晩のバックアップ スケジュールを設定することをお勧めしますが、初めて HANA を実行する際に、ブート ボリュームを少なくとも&1; 回はバックアップする必要があります。 SAP HANA インスタンス名を追加するのではなく、次のように、単にスクリプトに引数として _boot_ を挿入します。
```
./azure_hana_backup boot customer 20
```
ブート ボリュームにも、同じ保持ポリシーが適用されます。 前に説明したように、特別な場合 (SAP EHP アップグレード中や、別のストレージ スナップショットを作成する必要があるときなど) にのみ、オンデマンド スナップショットを使用する必要があります。

cron を使用して、スケジュールされたストレージ スナップショットを実行することを強くお勧めします。また、すべてのバックアップと障害復旧のニーズで同じスクリプトを (要求されるさまざまなバックアップ時刻に合わせてスクリプトの入力を変更して) 使用することをお勧めします。 実行時間に応じて、cron でさまざまなスケジュールを設定できます。状況に応じて、1 時間ごと、12 時間ごと、毎日、または毎週に設定できます。 cron スケジュールは、前に説明した長期間のオフサイト バックアップ用の保持ラベルに適したストレージ スナップショットを作成するように設計されています。 スクリプトには、要求される頻度に応じてすべての実稼働ボリュームをバックアップするためのコマンドが含まれています (ブート ボリュームは毎日バックアップされるだけですが、データとログ ファイルは毎時バックアップされます)。

次の cron スクリプト内のエントリは、毎時 10 分、12 時間ごとの 10 分、および毎日 10 分に実行されます。 cron ジョブは、任意の時間内に SAP HANA ストレージ スナップショットが&1; つだけ実行されるように作成されます。そのため、毎時と毎日のバックアップの両方が同時 (午前&12;:10) に行われることはありません。 SAP HANA on Azure サービス管理では、スナップショットの作成とレプリケーションが最適化されるようにバックアップを実行するための推奨時間が示されます。

/etc/crontab の既定の cron スケジュールは、次のとおりです。
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
上の cron 命令では、HANA ボリューム (ブート ボリュームを除く) は hourly のラベルでスナップショットを取得します。 これらのスナップショットのうち、66 個が保持されます。 さらに、12hour というラベルのスナップショットは 14 個が保持されます。そのため、基本的に、3 日分の hourly のスナップショットと、さらに 4 日分の 12hour のスナップショットを持つ可能性があります。これで、これらのスナップショットによりまる 1 週間がカバーされることになります  (hourly のスナップショットは、12 時間ごとのスナップショット時にはスケジュールされません。 12hour で作成されるのは、hourly と 12hour 両方のスナップショットではなく、1 つのスナップショットのみです)。

cron でのスケジュールは、スクリプトが数分間隔で実行されない限り、特定の時間には&1; つのスクリプトだけが実行されるため、わかりにくくなる場合があります。 長期的な保有用に毎日のバックアップが必要な場合、毎日のバックアップを 12 時間ごとのバックアップと共に保持する (それぞれの保有数は 7) か、毎時のスナップショットが 10 分後に行われるようにずらしますが、実稼働ボリュームに保持されるのは毎日のスナップショット 1 つだけです。
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7 
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
表示されている頻度は、単なる例です。 以下の条件に基づいて、スナップショットの最適な数を把握する必要があります。

- 特定の時点への復旧のための RTO の要件。
- 領域の使用量。
- 潜在的な障害復旧のための RPO と RTO の要件。
- ディスクに対する HANA データベースの完全バックアップの最終的な実行。 ディスクまたは _backint_ インターフェイスに対するデータベースの完全バックアップが実行されるたびに、ストレージ スナップショットの実行は失敗します。 ストレージ スナップショットに加えてデータベースの完全バックアップを実行する予定がある場合は、その実行中にストレージ スナップショットの実行が無効になるようにする必要があります。

>[!IMPORTANT]
> SAP HANA バックアップに対するストレージ スナップショットの使用は、SAP HANA ログ バックアップと共に行われる場合にだけ有効です。 これらのログ バックアップは、ストレージ スナップショット間の期間をカバーできる必要があります。 ユーザーに 30 日間の特定の時点への復旧が確約されている場合は、以下が必要です。

- 30 日前のストレージ スナップショットにアクセスできること。
- 過去 30 日間の連続したログ バックアップ。

ログ バックアップの範囲では、バックアップ ログ ボリュームのスナップショットも作成します。 ただし、以下の目的で定期的なログ バックアップが実行されていることを確認する必要があります。

- 特定の時点への復旧を実行するために必要な連続したログ バックアップを作成する。
- SAP Hana ログ ボリュームの領域不足を回避する。

最後の手順の&1; つとして、SAP HANA Studio で SAP HANA バックアップ ログをスケジュールします。 SAP HANA バックアップ ログのターゲットの場所は、特別に作成された hana/log\_backups ボリュームで、マウント ポイントは /hana/log/backups です。

![SAP HANA Studio での SAP HANA バックアップ ログのスケジュール](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

頻度が 15 分以下のバックアップを選択できます。 一部のユーザーは 1 分ごとにログ バックアップを実行する場合もありますが、15 分より "_短く_" することはお勧めしません。

最後の手順は、(SAP HANA の初期インストール後に) ファイル ベースのバックアップを作成し、バックアップ カタログ内に存在する必要がある単一のバックアップ エントリを作成することです。 そうしないと、SAP HANA は、指定されたログ バックアップを前に設定したように開始しません。

![ファイル ベースのバックアップで単一のバックアップ エントリが作成されるようにする](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-number-and-size-of-snapshots-on-disk-volume"></a>ディスク ボリューム上のスナップショットの数とサイズの監視

特定のストレージ ボリュームでは、スナップショットの数と、スナップショットによるストレージの使用量を監視できます。 **ls** コマンドは、スナップショットのディレクトリまたはファイルを表示しません。 一方、Linux OS コマンドの **du** は、以下のコマンドと共に使用すると、それらを表示します。
```
- du –sh .snapshot will provide a total of all snapshots within the snapshot directory.
- du –sh --max-depth=1 will list all snapshots saved in the .snapshot folder and the size of each snapshot.
- du –hc will just provide the total size used by all snapshots.
```
作成および格納したスナップショットがボリューム上のストレージを使用し尽くしていないことを確認するには、上記のコマンドを使用します。

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>サーバー上のスナップショットの数の削減

既に説明したように、特定のラベルで格納されるスナップショットの数を減らすことができます。 スナップショットを開始するコマンドの最後の&2; つのパラメーターは、ラベルと、保持するスナップショットの数です。
```
./azure_hana_backup.pl lhanad01 customer 20
```
上の例では、スナップショット ラベルは _customer_、このラベルの付いた保持するスナップショットの数は _20_ です。 ディスク領域の使用量に応じて、格納されるスナップショットの数を減らすことができます。 スナップショットの数を減らす簡単な方法は、最後のパラメーターを 5 に設定してスクリプトを実行することです。
```
./azure_hana_backup.pl lhanad01 customer 5
```
上の設定でスクリプトを実行した結果として、スナップショット数は少なくなります。そのため、新しいストレージ スナップショットを含むスナップショットの数は 5 になります。 以下の点にご注意ください。

- 削減できるのは、削減された数での実行前に作成された最後のスナップショットが&1; 時間より前である場合だけです。
- スクリプトは、1 時間未満のスナップショットも削除できません。

これらの制限は、提供されるオプションの DR 機能に関連しています。

特定のプレフィックスのスナップショットのセットを保持しなくてよくなった場合は、保持数を _0_ にしてスクリプトを実行します。スクリプトはプレフィックスが一致するすべてのスナップショットを削除し、終了します。 ただし、これは障害復旧の機能に影響します。

### <a name="recovering-to-the-latest-hana-snapshot"></a>最新の HANA スナップショットへの復旧

運用停止のシナリオが発生する場合は、SAP HANA on Azure サービス管理での顧客インシデントとして、ストレージ スナップショットからの復旧のプロセスを開始できます。 これは、実稼働システムでデータが削除され、実稼働データベースの復元を通じてそのデータを再取得することが唯一の対処法であるような場合は、予期しない緊急事態です。

一方、特定の時点への復旧は、緊急度が低く、数日前から計画できる場合があります。 この場合は、優先度の高い問題として提起する代わりに、SAP HANA on Azure サービス管理と実施を計画できます。 たとえば、新しい拡張パッケージを適用して SAP ソフトウェアのアップグレードを試した後、EHP アップグレードの前の状態を表すスナップショットに戻す必要があるような場合です。

要求を発行する前に、以下のような、いくつかの準備作業を行う必要があります。 そうすることで、SAP HANA on Azure サービス管理チームが要求を処理し、復元されたボリュームを提供できるようになります。 その後、スナップショットに基づいて HANA データベースを復元するのはユーザーです。

>[!NOTE]  
>次に示すスクリーンショットは、使用している SAP HANA リリースによって異なる場合があります。

1. 復元するスナップショットを決めます。 他に指示がない限り、hana/data ボリュームのみが復元されます。

2. HANA インスタンスをシャットダウンします。
![HANA インスタンスをシャットダウンする](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 各 HANA データベース ノードで、データ ボリュームのマウントを解除します。 データ ボリュームのマウントが解除されていない場合、スナップショットの復元は失敗します。
![各 HANA データベース ノードでデータ ボリュームのマウントを解除する](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 特定のスナップショットの復元を指示するための Azure サポート要求を開きます。

    **ストレージ スナップショットの復元中**は、SAP HANA on Azure サービス管理はボリュームのスナップショットを復元しており、失われているデータがないことを確認するために電話会議への出席を依頼する場合があります。

    **ストレージ スナップショットの復元後**は、SAP HANA on Azure サービス管理から、いつストレージ スナップショットが復元されたかが通知されます。 

5. 復元処理が完了したら、すべてのデータ ボリュームを再マウントします。
![すべてのデータ ボリュームを再マウントする](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio で回復オプションを選択します (HANA Studio で HANA DB に再接続したときに自動的に選択されない場合)。 次の例は、最新の HANA スナップショットへの復元を示しています。 ストレージ スナップショットには&1; つの HANA スナップショットが組み込まれています。最新のストレージ スナップショットに復元している場合、それは最も新しい HANA スナップショットになります  (それより前のストレージ スナップショットに復元している場合は、ストレージ スナップショットが作成された時刻に基づいて HANA スナップショットを探す必要があります)。![SAP HANA Studio 内で回復オプションを選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 回復の種類として、**[Recover the database to a specific data backup or storage snapshot (特定のデータ バックアップまたはストレージ スナップショットにデータベースを回復する)]** を選択します。
![回復の種類として [Recover the database to a specific data backup or storage snapshot (特定のデータ バックアップまたはストレージ スナップショットにデータベースを回復する)] を選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. **[Specify backup without catalog (カタログのないバックアップを指定する)]** オプション ボタンを選択します。
![[Specify backup without catalog (カタログのないバックアップを指定する)] オプション ボタンを選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. **[Destination Type (復旧先の種類)] の [Snapshot (スナップショット)]** を選択します。
![[Destination Type (復旧先の種類)] の [Snapshot (スナップショット)] を選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. **[Finish (完了)]** をクリックして復旧処理を開始します。
![[Finish (完了)] をクリックして復旧処理を開始する](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA データベースが復元され、ストレージ スナップショットに含まれている HANA スナップショットに復旧されます。
![HANA データベースが復元され、ストレージ スナップショットに含まれている HANA スナップショットに復旧される](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-most-recent-state"></a>最新の状態への復旧

この処理では、ストレージ スナップショットに含まれている HANA スナップショットに復元した後、ストレージ スナップショットを復元する前に、トランザクション ログ バックアップをデータベースの最新の状態に復元します。

>[!Important] 
>作業を進める前に、トランザクション ログ バックアップの完全な連続したチェーンがあることを確認します。 それがないと、データベースの現在の状態に戻ることができなくなります。

上で説明した、最新の HANA スナップショットに復旧する手順の 1. ～ 6. を実行します。

1. **[Recover the database to its most recent state (データベースを最新の状態に復旧する)]** を選択します。
![[Recover the database to its most recent state (データベースを最新の状態に復旧する)] を選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

2. 最新の HANA ログ バックアップの場所を指定します。 指定された場所には、HANA スナップショットから最新の状態までの HANA トランザクション ログ バックアップすべてが含まれている必要があります。
![最新の HANA ログ バックアップの場所を指定する](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

3. データベースを復旧するためのベースとなるバックアップを選択します。 この例では、ストレージ スナップショットに含まれている HANA スナップショットです  (次のスクリーンショットでは、これらのスナップショットのうちの&1; つだけが実行済みです)。![データベースを復旧するためのベースとなるバックアップを選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

4. HANA スナップショットの時刻から最新の状態までの間にバックアップが存在しない場合は、**[Use Delta Backups (差分バックアップを使用する)]** をオフにします。
![HANA スナップショットの時刻から最新の状態までの間にバックアップが存在しない場合は、[Use Delta Backups (差分バックアップを使用する)] をオフにする](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

5. 概要画面で **[Finish (完了)]** をクリックして復元処理を開始します。
![概要画面で [Finish (完了)] をクリックして復元処理を開始する](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

(ストレージ スナップショットに含まれている) HANA スナップショットから、HANA スナップショットより後のスナップショットまでの間の時点に復旧するには、次のようにして、特定の時点への復旧を行います。

- HANA スナップショットから復旧時点までのすべてのトランザクション ログ バックアップがあることを確認します。
- **[Recover the database to the following point in time (データベースを次の時点に復旧する)]** を選択します (「_最新の状態への復旧_」の手順 1. を参照してください)。
- 残りの手順は、上と同じです。

## <a name="monitoring-snapshot-execution"></a>スナップショットの実行の監視

ストレージ スナップショットの実行は、ユーザーが監視する必要があります。 ストレージ スナップショットを実行するスクリプトは、出力をファイルに書き込み、Perl スクリプトと同じ場所に保存します。 スナップショットごとに個別のファイルが書き込まれます。 そのファイルの出力は、スナップショット スクリプトが実行するさまざまなフェーズを明確に示します。各フェーズは、次のとおりです。

- スナップショットを作成する必要があるボリュームを探す。
- これらのボリュームから作成されたスナップショットを探す。
- 指定されたスナップショット数に一致するように、最終的に存在しているスナップショットを削除する。
- HANA スナップショットを作成する。
- 複数のボリュームにわたるストレージ スナップショットを作成する。
- HANA スナップショットを削除する。
- 最新のスナップショットの名前を **.0** に変更する。

スクリプトの最も重要な部分は、次のとおりです。
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
このサンプルから、スクリプトが HANA スナップショットの作成を記録する方法がわかります。 スケールアウトの場合は、これがマスター ノードで開始されます。 マスター ノードは各ワーカー ノードでスナップショットの同期的な作成を開始します。

その後、ストレージ スナップショットが作成されます。 ストレージ スナップショットの実行に成功すると、HANA スナップショットが削除されます。



<!--HONumber=Dec16_HO2-->


