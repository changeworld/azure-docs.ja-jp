<properties
   pageTitle="Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件 | Microsoft Azure"
   description="他のユーザーが購入できる仮想マシン イメージを作成して Azure Marketplace にデプロイするための要件を理解します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/09/2015"
  ms.author="hascipio; v-divte"/>

# Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件
始める前にプロセスをよく読み、各ステップをどこで、なぜ実行するのかを理解してください。可能な限り、会社の情報と他のデータを準備し、必要なツールをダウンロードし、技術コンポーネントを作成してから、プラン作成プロセスを開始する必要があります。この記事を読んでよく理解してください。

## 必要なツールとアプリケーションをダウンロードします。
プロセスを開始する前に、次の項目を準備してください。

- 対象とするオペレーティング システムに合わせて、Azure のダウンロード ページから、Azure PowerShell コマンドレットまたは Linux コマンド ライン インターフェイスのツールをインストールします。
- Azure Storage エクスプローラーを CodePlex からインストールします。
- 次のリンクから、「Azure 認定用の認定テスト ツール」をダウンロードおよびインストールします。
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913)。この認定ツールは、Windows ベースのコンピューターで実行する必要があります。使用可能な Windows ベースのコンピューターがない場合は、Azure で Windows ベースの VM を使用するとツールを実行できます。

## サポートされているプラットフォーム
Azure ベース のVM は、Windows または Linux 上で開発することができます。Azure と互換性のある VHD の作成などの発行プロセスの一部の要素では、使用されているオペレーティング システムに合わせて異なるツールや手順を使用します。

- Linux を使用している場合は、[Virtual Machine Image Publishing Guide (仮想マシン イメージの発行ガイド)](marketplace-publishing-vm-image-creation.md) の「Create an Azure-compatible VHD (Linux-based) (Azure と互換性のある VHD の作成 (Linux ベース))」セクションを参照してください。
- Windows を使用している場合は、[Virtual Machine Image Publishing Guide (仮想マシン イメージの発行ガイド)](marketplace-publishing-vm-image-creation.md) の「Create an Azure-compatible VHD (Windowsbased) (Azure と互換性のある VHD の作成 (Windows ベース))」セクションを参照してください。

> [AZURE.NOTE]認定検証ツールを実行し、VHD 認定の提出に使用する VHD SAS URL を作成するには、Windows コンピューターにアクセスする必要があります。

## VHD の開発
Azure VHD は、**クラウド** または**オンプレミス**でも開発できます。

- クラウド ベースで開発する場合、すべての開発手順は、Azure 上の VHD でリモート実行することになります。
- オンプレミスで開発する場合、VHD をダウンロードして、オンプレミス インフラストラクチャを使用して VHD を開発する必要があります。これを実行することは可能ですが、お勧めしません。オンプレミスで Windows 用または SQL 用に開発する場合は、関連するオンプレミスのライセンス キーが必要になることに注意してください。VM の作成後に SQL Server をインストールまたは追加することはできないほか、Azure ポータルからの承認済み SQL イメージに基づいてプランを作成する必要があります。オンプレミスで開発する場合は、クラウドで開発する場合とは異なる手順をいくつか行わなければなりません。関連情報については、[Creating a VM image on-premise (オンプレミスでの VM イメージ作成)](marketplace-publishing-vm-image-creation-on-premise.md) を参照してください。

## 次のステップ
前提条件を確認し、必要なタスクを完了したら、[Virtual Machine Image Publishing Guide (仮想マシン イメージの発行ガイド)](marketplace-publishing-vm-image-creation.md) で詳しく説明されている、仮想マシン イメージのプランの作成を始められます。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)
- [Create a virtual machine running Windows in the Azure preview portal (Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する)](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Nov15_HO3-->