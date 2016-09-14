<properties
	pageTitle="DevTest Labs を使用する理由 | Microsoft Azure"
	description="Azure Virtual Machines を簡単に作成、管理、監視するために DevTest ラボを使用する方法について説明します"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="tarcher"/>

#DevTest ラボを使用する理由

> [AZURE.NOTE]
この記事は、3 部構成のシリーズの第 2 部です。
> 
> 1. [DevTest ラボとは](devtest-lab-overview.md)
> 1. **[DevTest Labs を使用する理由](devtest-lab-why.md)**
> 1. [DevTest ラボの概念](devtest-lab-concepts.md)

##Overview
DevTest ラボを使用することで、クラウド内の開発者とテスト向けの環境の作成、構成、管理において次の利点が提供されます。

##心配無用のセルフサービス

DevTest ラボを使用することで、ユーザーごとの仮想マシン (VM) の数やラボあたりの VM の数など、ラボでのポリシーの設定が可能になり、コストの管理が容易になります。DevTest ラボでは、VM を自動的にシャット ダウンし、起動するためのポリシーを作成することもできます。

##すばやくテスト準備完了状態へ

DevTest ラボを使用することで、チームがアプリケーションの開発とテストを開始するために必要なものがすべて事前にプロビジョニングされている環境を作成できます。アプリケーションの最新の良好なビルドがインストールされた環境を要求し、作業を開始するだけです。あるいは、コンテナーを使えば環境の作成が一層迅速かつ効率的になります。

##1 回作成すれば、どこでも使える

チーム内や組織内で環境テンプレートとアーティファクトのキャプチャと共有を行い、開発者とテスト向けの環境を簡単に作成できます。これらすべてをソース管理の制御のもとで行えます。

##既存のツール チェーンとの統合

事前作成済みのプラグインや Microsoft の API を活用して、好みの継続的インテグレーション (CI) ツール、統合開発環境 (IDE)、自動リリース パイプラインから、開発/テスト環境のプロビジョニングを直接行うことができます。Microsoft の包括的なコマンド ライン ツールを使用することもできます。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

##次のステップ

[DevTest ラボの概念](devtest-lab-concepts.md)

<!---HONumber=AcomDC_0831_2016-->