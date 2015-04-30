<properties 
   pageTitle="StorSimple について" 
   description="StorSimple の機能、アーキテクチャ、コンポーネントについて説明します。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>

# StorSimple について 

Microsoft Azure StorSimple は、企業のストレージとデータ保護に伴う多くの問題を解消し、費用を削減する、効率と経済性、管理性に優れたソリューションです。独自開発のデバイス (Microsoft Azure StorSimple デバイス) と統合管理ツールを使用して、クラウド ストレージを含め、企業が保有するすべてのストレージをシームレスに表示します。

## StorSimple を使用する理由

Microsoft Azure StorSimple には、次のような利点があります。

- **透過的な統合** - Microsoft Azure StorSimple は、Internet Small Computer System Interface (iSCSI) プロトコルを使用して、ユーザーに意識させることなくデータ ストレージ設備を連結します。データは、その保存先がクラウドであれ、データ センターであれ、リモート サーバーであれ、すべて同じ場所に保存されているように見えます。
- **ストレージ コストの削減** - Microsoft Azure StorSimple は、現在の需要と照らして妥当な量のローカル ストレージまたはクラウド ストレージを割り当て、必要なときにのみクラウド ストレージを拡張します。また、重複するデータを排除 (重複除去) したり圧縮を使用したりすることによって、ストレージの要件と費用を削減します。
- **ストレージ管理の単純化** - Microsoft Azure StorSimple には、システム管理ツールが用意されており、オンプレミス、リモート サーバー、クラウドに保存されているいずれのデータも、このツールを使って構成、管理することができます。加えて、バックアップと復元の機能は、Microsoft 管理コンソール (MMC) スナップインから管理することができます。StorSimple にオプションで用意されている独立したインターフェイスを使うと、StorSimple の管理およびデータ保護サービスの利用範囲を、SharePoint サーバーに保存されているコンテンツにまで拡大することができます。 
- **障害復旧とコンプライアンスの強化** - Microsoft Azure StorSimple は、無駄に長い時間を復旧に費やしません。データは必要に応じて復元されます。つまり、最小限の中断で通常の業務を継続することができます。また、バックアップ スケジュールとデータ保持の設定を指定したポリシーを構成できます。
- **データのモビリティ** - Microsoft Azure クラウド サービスにアップロードしたデータに他のサイトからアクセスし、復旧や移行の用途に利用することができます。また、Microsoft Azure 内で実行されている仮想マシン (VM) に対し、StorSimple を使って StorSimple 仮想デバイスを構成することができます。この VM は、仮想デバイスを使用して、保存されているデータにアクセスし、テスト目的や復旧に利用することができます。 

次の図は、Microsoft Azure StorSimple ソリューションの全体像を示したものです。

![StorSimple アーキテクチャ](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple アーキテクチャ**

## StorSimple のコンポーネント

Microsoft Azure StorSimple ソリューションには、次のコンポーネントが含まれています。

- **Microsoft Azure StorSimple デバイス** - ソリッド ステート ドライブ (SSD) とハード ディスク ドライブ (HDD) に、冗長コントローラーと自動フェールオーバー機能を組み合わせたオンプレミスのハイブリッド ストレージ アレイです。ストレージの階層管理はコントローラーによって行われます。現在使用されているデータ (ホット データ) はローカル ストレージ (デバイスまたはオンプレミス サーバー) に配置され、使用頻度の低いデータはクラウドに移動されます。
- **StorSimple 仮想デバイス** - 物理ハイブリッド ストレージ デバイスのアーキテクチャと機能をそのまま再現したソフトウェア版の StorSimple デバイスです (仮想アプライアンスともいいます)。StorSimple 仮想デバイスは、Azure 仮想マシン内の単一ノードで動作します。仮想デバイスは、テスト シナリオや小規模な試験的シナリオに適しています。StorSimple デバイスやオンプレミスのサーバーに StorSimple 仮想デバイスを作成することはできません。
- **StorSimple 用 Windows PowerShell** - StorSimple デバイスの管理に使用するコマンドライン インターフェイスです。StorSimple 用 Windows PowerShell の機能を使って、StorSimple デバイスの登録、デバイスのネットワーク インターフェイスの構成、特定の種類の更新プログラムのインストール、サポート セッションへのアクセスによるデバイスのトラブルシューティング、デバイスの状態の変更などを行うことができます。StorSimple 用 Windows PowerShell には、シリアル コンソールに接続するか、Windows PowerShell リモート処理を使用してアクセスできます。
- **StorSimple Manager サービス** - StorSimple デバイスまたは StorSimple 仮想デバイスを単一の Web インターフェイスから管理できる、Azure の管理ポータルの拡張機能です。StorSimple Manager サービスを使用して、サービスの作成と管理、デバイスの表示と管理、アラートの表示、ボリュームの管理、バックアップ ポリシーやバックアップ カタログの表示と管理を行うことができます。
- **StorSimple Snapshot Manager** - ボリューム グループと Windows ボリューム シャドウ コピー サービスを使用してアプリケーション整合性のあるバックアップを生成する MMC スナップインです。また、StorSimple Snapshot Manager を使用して、バックアップ スケジュールを作成したり、ボリュームを複製または復元したりすることもできます。 
- **SharePoint 用 StorSimple アダプター** - Microsoft Azure StorSimple のストレージとデータ保護を SharePoint サーバー ファームにまで透過的に拡張すると共に、SharePoint 管理ポータルで Azure StorSimple ストレージを表示して管理できるようにするツールです。

## 次のステップ

[StorSimple のコンポーネント](https://technet.microsoft.com/library/cc754482.aspx)に関する記事と [StorSimple のリリース ノート](https://msdn.microsoft.com/library/azure/dn772367.aspx)を読む




<!--HONumber=52-->