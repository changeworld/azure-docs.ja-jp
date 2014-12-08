<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning サンプル: K 平均法クラスタリングを使用した色彩量子化 | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning サンプル: K 平均法クラスタリングを使用した色彩量子化

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


##問題の説明

[色彩量子化](http://en.wikipedia.org/wiki/Color_quantization "Color quantization")とは、画像の色の種類を減らし、圧縮するプロセスです。通常、その目的は、メモリの制限や圧縮のために、色の数を減らした状態で可能な限り画像の色の見た目を保つことにあります。 

##データ

このサンプル実験では、任意の 24 ビットの RGB 画像に 256 x 256 x 256 色が使用される可能性があることを前提としています。そして、その輝度の値に基づいて、標準色の度数分布図を作成します。別の方法として、明示的に画像を量子化し、16 色または 64 色に色の数を*減らす*方法があります。これは実質的にスペースを小さくし、(理論的に言えば) ノイズと分散を少なくします。このために、画素データ (データセットの行としての各画素) を K 平均法クラスタリング モジュールに渡しました。 

##モデル

モデルの作成方法を下の画像に示します。

![Model][image1]

5 つの分岐に K = 10 から 500 を使用して K 平均法クラスタリングを実行しました。まず、クラスターを計算し、そのクラスタリングをピクセルの色の平均に集計しました (R スクリプトを使用)。 
最後に、各ピクセルと集計されたクラスターの色に関連付け、新しい画像を CSV 形式で送信します。また、元の画像を使用して、新しい画素の色の二乗平均平方根の差も計算し、R プロットでそれらを示しました ("R スクリプトの実行" を使用)。 

##結果

下の実験モデルに示すさまざまな数のクラスター (色) で結果を検証しました。後述するように、追加のクラスタリングは高品質で圧縮率の低い画像を作成します。

||
------------ | ---------
**元の画像** | ![Original][image2a]
**K = 10**     | ![K=10][image2b]
**K = 20**     | ![K=20][image2c]
**K = 50**     | ![K=50][image2d]
**K = 100**    | ![K=100][image2e]
**K = 500**    | ![K=500][image2f]


また、元の画像の色に対する二乗平均平方根の差を使用して、精度も測定しました。これは、"R スクリプトの実行" モジュールの 2 番目の出力ポートから見ることができます。

![Output of Execute R Script module][image3]

色のクラスターが多くなるほど、元の画像に一致する色が多くなる (品質が向上する) ことがわかります。 

##画像を CSV に変換し CSV を画像に変換するコード

画像を ML Studio へ送るために、画像ファイルを ML Studio が使用できる CSV 形式に変換し、また、CSV を変換して画像に戻すこともできる、単純な変換コードを作成しました。遠慮なく次のコードをご使用ください。将来的には、画像を読み込むためのモジュールも追加する予定です。 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - K 平均法クラスタリングを使用した色ベースの画像圧縮 - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###Bil Gates の RGB 画像
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

