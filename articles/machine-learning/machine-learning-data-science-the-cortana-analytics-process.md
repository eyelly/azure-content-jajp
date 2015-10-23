<properties 
	pageTitle="Cortana Analytics Process とは何か | Microsoft Azure" 
	description="Cortana Analytics Process は、高度な分析技術を利用したインテリジェントなアプリケーションを構築するための体系的なデータ サイエンス手法です。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="bradsev;gopitk" />


# Cortana Analytics Process (CAP) とは何か

Cortana Analytics Process (CAP) は、高度な分析技術を利用してインテリジェントなアプリケーションを構築する一連のステップを体系的に示したデータ サイエンス手法です。問題を定義し、関連するデータを分析して、予測モデルの構築と評価を行い、さらに、それによって得たモデルをインテリジェント アプリケーションにデプロイするために進むべき**方向性**を CAP のステップを通じて見いだすことができます。

以下に、**Cortana Analytics Process** のステップを示します。

![CAP ワークフロー](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

これは**反復的**なプロセスです。モデルの新たな部分や既にある部分、改良点への理解は絶えず変化し、その流れの中で一度完成したものを作り替える手順が必要となります。CAP によって定義された一連のステップには、組織開発やプロジェクト計画に伴う既存のプロセスを**容易に適応させる**ことができます。

以降このプロセスの各ステップについて説明します。

## 準備ステップ 

## 1\. 分析プロジェクトの計画を立てる 

分析プロジェクトへの取り組みで最初に行うことは、ビジネスの目標と問題を明らかにすることです。これらの目標と問題は**ビジネス要件**の観点で定義します。それらの要件を満たすために分析で予測すべき主要なビジネス変数 (売上予測、注文が詐欺である確率など) を特定することが、このステップの核となる目的です。通常、必要な**データ ソース**への理解を深め、プロジェクトの目的に分析的観点から取り組むためには計画の積み重ねが欠かせません。たとえば、既存のシステムで問題を解決し、プロジェクトの目標を達成するために、従来とは異なる種類のデータを収集、記録しなければならないことはよくあります。

## 2\. 分析環境のセットアップ 

Cortana Analytics Process の分析環境はいくつかの要素で構成されます。それらの構成要素を次に示します。

- **データ ワークスペース**: 分析とモデリング用にデータをステージングする場所です。 
- **データ処理インフラストラクチャ**: データの前処理、調査、モデリングに使用します。
- **ランタイム インフラストラクチャ**: 分析モデルを運用可能な状態にし、そのモデルを利用するインテリジェントなクライアント アプリケーションを実行するための基盤です。  

セットアップすべき分析インフラストラクチャが帰属する環境は多くの場合、主要な運用システムからは独立しています。しかし多くの場合、そのインフラストラクチャによって、社内だけでなく社外のさまざまなシステムから得られるデータを活用することができます。分析インフラストラクチャには、純粋なクラウド環境、オンプレミス環境、または両者のハイブリッド環境があります。

## 分析ステップ:  

## 1\.分析環境にデータを取り込む 

最初のステップは、関連するデータを各種のソースから取り込むことです。データ処理を目的とした分析環境に社内または社外のソースからデータを取り込みます。ソース データの**形式**が、取り込み先で決められている形式と異なる場合があります。その場合、取り込み機能側で何らかのデータ変換が必要になります。

多くのインテリジェント アプリケーションは、データの初回取り込みに加え、日常の学習プロセスで定期的にデータを最新の状態に更新する必要があります。これは、**データ パイプライン**またはワークフローを設定することによって行うことができます。ソリューションのデプロイ先となるインテリジェント アプリケーションには、その分析モデルの再構築と再評価を含む反復的なプロセスの一部として、このデータ パイプラインまたはワークフローが適用されます。


## 2\.データの調査と前処理 

次のステップは、データの**要約統計量**と関係を調べ、**視覚化**などの手法を使って、データに対する理解を深めることです。**データの品質**と整合性 (欠損値など)、データ型の不一致、データ関係の不整合などの問題にも、このステップで対処します。生データは、前処理変換でクリーンアップされてその後の分析やモデリングに使用されます。


## 3\.データの特徴を明らかにする 

データ サイエンティストは、分野ごとの専門家と連携し、プランニングの段階で特定した主要なビジネス変数を予測する目的に最も適し、かつデータセットの際立った性質を表す特徴を把握する必要があります。こうした新たな特徴は、既存のデータから導くことができる場合もあれば、さらに別のデータを収集することが必要になる場合もあります。このプロセスは**素性設計**とも呼ばれ、効果的な予測分析システムを構築するうえでの主要なステップの 1 つとなっています。このステップでは、特定分野の専門知識とデータの調査ステップから得られた知見とを創造的に組み合わせる必要があります。


## 4\.予測モデルを作成する 

プランニング ステップではビジネス要件を明らかにしました。そのビジネス要件によって特定された主要な変数は、データ サイエンティストが分析モデルを構築し、クリーニングと素性選択の済んだデータを使用して予測します。機械学習システムは、多様な事例に適用できるさまざまな**モデリング アルゴリズム**に対応しています。

データ サイエンティストは、予測タスクに最適なモデルを選択する必要があります。また、最適な結果を得るためには、複数のモデルから得られた結果を組み合わせなければならない場合も少なくありません。モデリングに使用する入力データは通常、無作為に分割されて次の 3 つのデータセットが構築されます。

- トレーニング データセット 
- 検証データセット 
- テスト データセット 

モデルは、**トレーニング データセット**を使用して構築されます。最適なモデルの組み合わせ (パラメーターのチューニングを含む) は、モデルを実行して**検証データセット**の予測誤差を測定することによって選択されます。最後の**テスト データセット**は、モデルのトレーニングにも検証にも使われなかった独立したデータに対して、選択したモデルのパフォーマンスを評価する目的に使用されます。


## 5\.モデルをデプロイして利用する 

うまく機能する一連のモデルが得られたら、他のアプリケーションから利用できるように、それらのモデルを**運用可能な状態**にします。予測は、ビジネス要件に応じて**リアルタイム**または**バッチ処理**で行われます。モデルを運用可能な状態にするためには、オンライン Web サイト、スプレッドシート、ダッシュボード、基幹業務アプリケーション、バックエンド アプリケーションなど各種のアプリケーションから利用しやすいよう**オープン API インターフェイス**でそのモデルを公開する必要があります。

## まとめと次のステップ

Cortana Analytics Process は、繰り返し行う一連のステップとしてモデル化されます。高度な分析技術を使ってインテリジェントなアプリケーションを構築するために必要なタスクについて、**進むべき方向性**をそれらのステップを通じて見いだすことができます。見出したタスクをマイクロソフトの各種テクノロジを使って遂行する方法も、それぞれのステップから得られます。

CAP は、特定の種類の**ドキュメント** アーティファクトを規定するものではありません。データの調査、モデリング、評価の結果を記録し、必要なときにその分析を再現できるよう、関連する体系を保存するためのベスト プラクティスです。これによって、同様のデータや予測タスクを伴う他のアプリケーションに取り組むときに、同じ分析作業を再利用することができます。

また、**特定のシナリオ**のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。

 

<!---HONumber=Oct15_HO2-->