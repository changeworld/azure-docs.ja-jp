<properties 
   pageTitle="StorSimple について | Microsoft Azure" 
   description="StorSimple のデータ管理と保護プロセス、利点、アーキテクチャについて説明し、StorSimple のコンポーネントを紹介します。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/25/2015"
   ms.author="v-sharos@microsoft.com"/>

# StorSimple 8000 シリーズ: ハイブリッド クラウド ストレージ ソリューション 

## 概要

Microsoft Azure StorSimple は、企業のストレージとデータ保護に伴う多くの問題を解消し、費用を削減する、効率と経済性、管理性に優れた SAN ソリューションです。クラウド サービスと連携し、独自開発のデバイス (Microsoft Azure StorSimple デバイス)、管理ツールを使用して、クラウド ストレージを含め、企業が保有するすべてのストレージをシームレスに表示します。

StorSimple では、ストレージ階層化を使用し、さまざまなストレージ メディアに格納されているデータを管理します。現在作業中のセットはソリッド ステート ドライブ (SSD) へ、あまり頻繁に使用しないデータはハード ディスク ドライブ (HDD) へ格納され、アーカイブ化したデータはクラウドへプッシュされます。また、StorSimple 圧縮を使用して、データが消費するストレージの量を削減します。ストレージの階層化プロセスは次のように行われます。

1. システム管理者が、Microsoft Azure のクラウド ストレージ アカウントを設定します。
2. 管理者がシリアル コンソールと StorSimple Manager サービス (Microsoft Azure 管理ポータルで実行) を使用して、デバイスを構成し、ボリュームとデータ保護ポリシーを作成します。オンプレミスのコンピューター (ファイル サーバーなど) は、Internet Small Computer System Interface (iSCSI) を使用して StorSimple デバイスにアクセスします。
3. 最初、StorSimple はデバイスの高速 SSD 階層にデータを格納します。
4. SSD 階層が容量に近づくと、StorSimple は最も古いデータ ブロックを重複除去して圧縮し、HDD 階層に移動します。
5. HDD 階層が容量に近づくと、StorSimple は最も古いデータ ブロックを暗号化し、HTTPS 経由で Microsoft Azure ストレージ アカウントに安全に送信します。
6. Microsoft Azure はデータセンターとリモート データセンターにデータの複数のレプリカを作成し、障害が発生した場合にデータを復旧できるようにします。 
7. クラウドに格納されているデータをファイル サーバーが要求すると、StorSimple はデータをシームレスに返送し、StorSimple デバイスの SSD 階層にコピーを格納します。

ストレージ管理のほか、StorSimple データ保護機能では、オン デマンドやスケジュールされたバックアップを作成して、それをローカルやクラウドで格納できるようになります。バックアップは増分スナップショットの形式で実行されます。これは短時間で作成し、復元できることを意味しています。クラウド スナップショットはセカンダリ ストレージ システム (テープ バックアップなど) を置き換え、データセンターにデータを復元したり、必要に応じてサイトを交代できるため、非常に重要になります。

>[AZURE.NOTE]ソフトウェア Update 1 以降の StorSimple 8000 シリーズでは、Amazon S3 with RRS、HP、OpenStack クラウド サービス、Microsoft Azure をサポートします(デバイスの管理が目的の場合は Microsoft Azure ストレージ アカウントが必要になります)。 詳細については、「[サービスの新しいストレージ アカウントを構成する](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service)」をご覧ください。

## StorSimple を使用する理由

Microsoft Azure StorSimple には、次のような利点があります。

- **透過的な統合** - Microsoft Azure StorSimple は、iSCSI プロトコルを使用して、ユーザーに意識させることなくデータ ストレージ設備を連結します。データは、その保存先がクラウドであれ、データ センターであれ、リモート サーバーであれ、すべて同じ場所に保存されているように見えます。
- **ストレージ コストの削減** - Microsoft Azure StorSimple は、現在の需要と照らして妥当な量のローカル ストレージまたはクラウド ストレージを割り当て、必要なときにのみクラウド ストレージを拡張します。また、重複するデータを排除 (重複除去) したり圧縮を使用したりすることによって、ストレージの要件と費用を削減します。
- **ストレージ管理の単純化** - Microsoft Azure StorSimple には、システム管理ツールが用意されており、オンプレミス、リモート サーバー、クラウドに保存されているいずれのデータも、このツールを使って構成、管理することができます。加えて、バックアップと復元の機能は、Microsoft 管理コンソール (MMC) スナップインから管理することができます。StorSimple にオプションで用意されている独立したインターフェイスを使うと、StorSimple の管理およびデータ保護サービスの利用範囲を、SharePoint サーバーに保存されているコンテンツにまで拡大することができます。 
- **障害復旧とコンプライアンスの強化** - Microsoft Azure StorSimple は、無駄に長い時間を復旧に費やしません。データは必要に応じて復元されます。つまり、最小限の中断で通常の業務を継続することができます。また、バックアップ スケジュールとデータ保持の設定を指定したポリシーを構成できます。
- **データのモビリティ** - Microsoft Azure クラウド サービスにアップロードしたデータに他のサイトからアクセスし、復旧や移行の用途に利用することができます。また、Microsoft Azure 内で実行されている仮想マシン (VM) に対し、StorSimple を使って StorSimple 仮想デバイスを構成することができます。この VM は、仮想デバイスを使用して、保存されているデータにアクセスし、テスト目的や復旧に利用することができます。 

次の図は、Microsoft Azure StorSimple ソリューションの全体像を示したものです。

![StorSimple のアーキテクチャ](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple アーキテクチャ**

## StorSimple のコンポーネント

Microsoft Azure StorSimple ソリューションには、次のコンポーネントが含まれています。

- **Microsoft Azure StorSimple デバイス** - ソリッド ステート ドライブ (SSD) とハード ディスク ドライブ (HDD) に、冗長コントローラーと自動フェールオーバー機能を組み合わせたオンプレミスのハイブリッド ストレージ アレイです。ストレージの階層管理はコントローラーによって行われます。現在使用されているデータ (ホット データ) はローカル ストレージ (デバイスまたはオンプレミス サーバー) に配置され、使用頻度の低いデータはクラウドに移動されます。
- **StorSimple 仮想デバイス** - 物理ハイブリッド ストレージ デバイスのアーキテクチャと大部分の機能をそのまま再現したソフトウェア版の StorSimple デバイスです (別名 StorSimple 仮想アプライアンス)。StorSimple 仮想デバイスは、Azure 仮想マシン内の単一ノードで動作します。仮想デバイスは、テスト シナリオや小規模な試験的シナリオに適しています。StorSimple デバイスやオンプレミスのサーバーに StorSimple 仮想デバイスを作成することはできません。
- **StorSimple 用 Windows PowerShell** - StorSimple デバイスの管理に使用するコマンドライン インターフェイスです。StorSimple 用 Windows PowerShell の機能を使って、StorSimple デバイスの登録、デバイスのネットワーク インターフェイスの構成、特定の種類の更新プログラムのインストール、サポート セッションへのアクセスによるデバイスのトラブルシューティング、デバイスの状態の変更などを行うことができます。StorSimple 用 Windows PowerShell には、シリアル コンソールに接続するか、Windows PowerShell リモート処理を使用してアクセスできます。
- **StorSimple 用 Azure PowerShell コマンドレット** – コマンドラインからのサービス レベル タスクと移行タスクを自動化することができる Windows PowerShell コマンドレットのコレクションです。StorSimple 用の Azure PowerShell コマンドレットの詳細については、「[コマンドレット リファレンス](https://msdn.microsoft.com/library/dn920427.aspx)」を参照してください。
- **StorSimple Manager サービス** - StorSimple デバイスまたは StorSimple 仮想デバイスを単一の Web インターフェイスから管理できる、Azure 管理ポータルの拡張機能です。StorSimple Manager サービスを使用して、サービスの作成と管理、デバイスの表示と管理、アラートの表示、ボリュームの管理、バックアップ ポリシーやバックアップ カタログの表示と管理を行うことができます。
- **StorSimple Snapshot Manager** - ボリューム グループと Windows ボリューム シャドウ コピー サービスを使用してアプリケーション整合性のあるバックアップを生成する MMC スナップインです。また、StorSimple Snapshot Manager を使用して、バックアップ スケジュールを作成したり、ボリュームを複製または復元したりすることもできます。 
- **SharePoint 用 StorSimple アダプター** - Microsoft Azure StorSimple のストレージとデータ保護を SharePoint サーバー ファームにまで透過的に拡張すると共に、SharePoint 管理ポータルで Azure StorSimple ストレージを表示して管理できるようにするツールです。

## 次のステップ

[StorSimple の詳細情報](https://azure.microsoft.com/documentation/services/storsimple/)

[StorSimple コンポーネントと用語集](storsimple-components.md)についてさらに読む。


 

<!---HONumber=Oct15_HO1-->