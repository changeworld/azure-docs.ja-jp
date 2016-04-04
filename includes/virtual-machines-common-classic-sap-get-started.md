クラウド コンピューティングという言葉は、今や多くの人々に認知され、小規模な企業から大企業、多国籍企業にいたるまで、IT 業界における存在感を日増しに高めています。Microsoft Azure はマイクロソフトが提供する Cloud Services プラットフォームで、多方面にわたって新たな可能性を生み出しています。アプリケーションを Cloud Services として迅速にプロビジョニングしたり、プロビジョニング解除したりすることができるようになった今、技術や予算の制約に縛られることはありません。企業は、ハードウェア インフラストラクチャに貴重な時間と予算を費やすことなく、アプリケーションやビジネス プロセス、そして顧客とユーザーの利益にのみ目を向けることができます。

Microsoft Azure Virtual Machines サービスを通じて、マイクロソフトは包括的な IaaS (Infrastructure as a Service) プラットフォームを提供しています。SAP NetWeaver ベースのアプリケーションは、Azure Virtual Machines (IaaS) でサポートされます。以下のホワイトペーパーには、SAP NetWeaver ベースのアプリケーションに最適なプラットフォームをプランニングし、Microsoft Azure に導入する方法が説明されています。

## 計画と導入

タイトル: Azure Virtual Machines への SAP NetWeaver の導入 – 計画/導入ガイド

概要: SAP NetWeaver を Azure Virtual Machines で実行することを検討している方は、まずこのホワイト ペーパーをお読みください。既にあるまたは導入を予定している SAP NetWeaver ベースのシステムを Azure Virtual Machines 環境にデプロイできるかどうかを評価するうえで参考になります。SAP NetWeaver のデプロイに伴うさまざまなシナリオのほか、Azure に固有の SAP 構成も取り上げています。このホワイト ペーパーには、ハイブリッド SAP ランドスケープを運用するために SAP/Azure 側で必要となる構成情報が詳細に説明されています。SAP NetWeaver ベースのシステムの高可用性を IaaS で確保するために講じることのできる対策についても触れています。

更新日: 2015 年 8 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkId=397963)
## デプロイ
タイトル: Azure Virtual Machines への SAP NetWeaver の導入 – デプロイ ガイド

概要: SAP NetWeaver ソフトウェアを Azure 内の仮想マシンにデプロイする手順について説明します。このホワイト ペーパーでは、Azure Monitoring Extensions for SAP の有効化に重点を置きつつ (Azure Monitoring Extensions for SAP で発生する問題のトラブルシューティングに関する推奨事項など)、3 つのデプロイ シナリオを中心に説明しています。既に計画/導入ガイドを読んでいることを前提としています。

更新日: 2015 年 9 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkId=397964)

## Azure における SAP DBMS
タイトル: Azure への SAP DBMS のデプロイ ガイド

概要: このホワイト ペーパーでは、SAP と連携させる DBMS システムの計画と導入に関する注意点を取り上げています。最初のパートでは、全般的な注意点が列挙されています。その後、SAP でサポートされる各種 DBMS を Azure にデプロイする作業に関連した説明が続きます。列挙されている DBMS は、Linux、Unix、および Windows 用の SQL Server、SAP ASE、Oracle、SAP MaxDB および IBM DB2 です。その個々の領域において、これらの DBMS と共に SAP システムを Azure で実行する際に考慮する必要のある事柄を説明します。Azure 上の各種 DBMS でサポートされるバックアップと高可用性の手法といったテーマは、SAP アプリケーションの使用法の観点から取り上げています。

更新日: 2015 年 12 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkId=397965)

## Azure における SAP NetWeaver

タイトル: SAP NetWeaver - Azure ベースの障害復旧ソリューションの構築

概要: このドキュメントでは、Azure ベースの障害復旧ソリューションを SAP NetWeaver 向けに構築するための手順を説明します。ここで取り上げているソリューションは、Hyper-V を基盤とする仮想化されたオンプレミス環境で SAP ランドスケープが実行されていることを前提としています。その構成要素の Azure Site Recovery (ASR) サービスがこのドキュメントの第 1 部で紹介されています。第 2 部では、SAP NetWeaver ベースのランドスケープについて具体的に説明しています。SAP NetWeaver アプリケーション インスタンスと SAP Central Services に、ASR を組み合わせて使用することの可能性について触れています。第 2 部で主に取り上げているテーマは、Windows Server フェールオーバー クラスター構成で保護された SAP Central Services への ASR の活用です。

更新日: 2015 年 9 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkID=521971)

## Azure における SAP NetWeaver - HA

タイトル: Azure への SAP NetWeaver の導入 - Azure 上の Windows Server フェールオーバー クラスターと SIOS DataKeeper を使用して SAP ASCS/SCS インスタンスをクラスタリングする

概要: このドキュメントでは、SIOS DataKeeper を使用して可用性に優れた SAP ASCS/SCS 構成を Azure 上にセットアップする方法を説明しています。SAP では、その単一障害点となるコンポーネント (SAP ASCS/SCS、Enqueue Replication Services など) を、共有ディスクを使った Windows Server フェールオーバー クラスター構成によって保護しています。これらの SAP コンポーネントは、SAP システムの機能に不可欠です。それらのコンポーネントがサーバーの障害や VM の障害に耐えることができるよう、ベアメタル環境や Hyper-V 環境向けの Windows クラスター構成を使って高可用性機能を設定する必要があります。2015 年 8 月時点では、こうしたクリティカルな SAP コンポーネントに要求される Windows ベースの高可用性構成に必要となる共有ディスクを Azure 単体で提供することができません。しかし、SIOS 社の DataKeeper という製品を利用すれば、SAP ASCS/SCS に必要な Windows Server フェールオーバー クラスター構成を Azure IaaS プラットフォーム上に構築することが可能です。このホワイト ペーパーには、SIOS Datakeeper によって提供される共有ディスクを使用して、Windows Server フェールオーバー クラスター構成を Azure にインストールする手順が説明されています。このホワイト ペーパーでは、この高可用性構成を適切に機能させるために必要な Azure、Windows、SAP 側の構成について詳しく説明しています。特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイする際の主要なリソースである SAP インストール ドキュメントと SAP Notes を補足する内容となっています。

更新日: 2015 年 8 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkId=613056)

## Azure SUSE Linux Virtual Machines における SAP NetWeaver

タイトル: Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト

概要: 現時点では、SAP は Azure Linux VMs での SAP NetWeaver の実行を正式にはサポートしていません。それでも、SAP サポートに連絡する必要がない限り、顧客はテストを実施する、または Azure Linux VMs で SAP のデモやトレーニング システムを実行することを検討する場合があります。この記事では、SAP を実行するために必要な Azure SUSE Linux VMs の設定方法と、潜在的な落とし穴を回避するための基本的なヒントをいくつか示します。

更新日: 2015 年 12 月

[記事はこちら](virtual-machines-linux-sap-on-suse-quickstart.md)

<!---HONumber=AcomDC_0323_2016-->