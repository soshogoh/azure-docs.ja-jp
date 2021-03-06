---
title: Azure IoT Central アプリケーションでデバイス データを分析する | Microsoft Docs
description: Azure IoT Central アプリケーションでデバイス データを分析します。
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 851a509421924a2ea6607020e7749c10dcbec01d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772977"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>分析を使用してデバイス データを分析する方法


*この記事は、オペレーター、ビルダー、および管理者に適用されます。*


Azure IoT Central には、大量のデバイス データを理解するための豊富な分析機能が用意されています。 最初に、左側のナビゲーション メニューで **[分析]** を選択します。 

## <a name="querying-your-data"></a>データのクエリ

始めるには、**デバイス セット**を選択し、**フィルター**を追加して (省略可能)、**期間**を選択する必要があります。 これを行ったら、*[結果の表示]* をクリックしてデータの視覚化を開始します。


* **デバイス セット:**[デバイス セット](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)は、ユーザーが定義したデバイスのグループです。 たとえば、オークランドのすべての冷蔵庫や、すべての回転 2.0 風力タービンなどです。

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **フィルター:** 必要に応じて検索にフィルターを追加し、データを絞り込むことができます。 一度に最大 10 個のフィルターを追加できます。 たとえば、オークランドのすべての冷蔵庫で、温度が 60 度を超えるものを検索します。 
* **期間:** 既定では、過去 10 分間のデータを取得します。 この値を定義済みの時間範囲のいずれかに変更したり、カスタムの期間を選択したりできます。 

 ![分析クエリ](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>データの視覚化

データをクエリすると、視覚化を始めることができます。 測定値の表示/非表示、データの集計方法の変更、異なるデバイス プロパティによるデータの分割などを行うことができます。  

* **分割基準:** デバイスのプロパティでデータを分割すると、データをさらにドリル ダウンできます。 たとえば、デバイス ID や場所で結果を分割できます。
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **測定:** デバイスから一度に報告される異なるテレメトリ項目の表示/非表示を最大 10 個まで選択できます。 測定値は、温度や湿度などです。 
* **集計:** 既定では平均でデータを集計しますが、ニーズに合わせて他のデータ集計に変更できます。 

   ![分析の視覚化の分割](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>データの操作

視覚化のニーズに合わせてクエリ結果をさまざまな方法でさらに変更できます。 グラフ ビューとグリッド ビューの切り替え、拡大と縮小、データ セットの更新、線の表示方法の変更などができます。

* **グリッドの表示:** 結果が表形式で表示され、各データ ポイントの特定の値を見ることができます。 このビューは、アクセシビリティの標準も満たしています。 
* **グラフの表示:** 結果が線形式で表示され、上昇/下降の傾向や異常を簡単に特定できます。 

 ![分析のグリッド ビューの表示](media/howto-create-analytics-experimental/analytics-showgrid.png)

ズームを使用すると、データを絞り込むことができます。 結果セットで注目したい期間がある場合、カーソルを使用して拡大する領域をグラブし、使用可能なコントロールを使用して次の操作のいずれかを実行できます。
* **拡大:** 期間を選択すると、拡大が有効になり、データを拡大できます。
* **縮小:** このコントロールを使用すると、最後のズームから 1 レベルだけ縮小することができます。 たとえば、データを 3 回拡大した場合、縮小すると一度に 1 ステップだけ戻ります。
* **ズームのリセット:** さまざまなレベルのズームを実行した後、ズーム リセット コントロールを使用して元の結果セットに戻すことができます。 

 ![データにズームを実行する](media/howto-create-analytics-experimental/analytics-zoom.png)


ニーズに合わせて線のスタイルを変更できます。 4 つのオプションから選択できます。
* **線:** 各データ ポイントの間に直線が形成されます。 
* **スムーズ:** 各ポイント間に曲線が形成されます
* **ステップ:** グラフ上の各ポイント間の線でステップ グラフが作成されます
* **散布図:** すべてのポイントがグラフにプロットされ、それらを結ぶ線は表示されません。 

 ![分析で使用できるさまざまな線のタイプ](media/howto-create-analytics-experimental/analytics-linetypes.png)

最後に、次の 3 つのモードのいずれかを選択して、Y 軸にデータを配置できます。

* **積み上げ:** すべての測定のグラフが積み上げられ、各グラフに独自の Y 軸があります。 積み上げグラフは、複数の測定が選択されており、これらの測定の個別のビューを表示したい場合に役立ちます。
* **非積み上げ:** すべての測定のグラフが 1 つの Y 軸に対してプロットされますが、Y 軸の値は強調表示された測定に基づいて変更されます。 非積み上げグラフは、複数の測定を重ね合わせ、同じ時間範囲のこれらの測定にまたがるパターンを表示したい場合に役立ちます。
* **共有 Y 軸**:すべてのグラフが同じ Y 軸を共有し、この軸の値は変更されません。 共有 Y 軸グラフは、分割基準を使用してデータをスライスしながら、1 つの測定を表示したい場合に役立ちます。

 ![異なる視覚化モードで Y 軸にデータを配置する](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションのカスタム分析を作成する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Node.js アプリケーションを準備して接続する](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)