---
title: Azure Data Factory の Mapping Data Flow のソース変換
description: Azure Data Factory の Mapping Data Flow のソース変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 38a01b4f81b76ba90a5fda4909d0e65e6307057e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408716"
---
# <a name="mapping-data-flow-source-transformation"></a>Mapping Data Flow のソース変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ソース変換では、データをデータ フローに変換するために使用するデータ ソースが構成されます。 1 つのデータ フローで複数のソース変換を設定できます。 常に、ソースによるデータ フローの設計から始めてください。

> [!NOTE]
> どのデータ フローにも、少なくとも 1 つのソース変換が必要です。 データ変換を完了するには、必要なだけの数のソースを追加します。 これらのソースを結合またはユニオン変換を使用して結合できます。

![ソース変換のオプション](media/data-flow/source.png "ソース")

各データ フロー ソース変換は、正確に 1 つの Data Factory データセットに関連付けられている必要があります。これにより、書き込みや読み取りを行うデータの形状と場所が定義されます。 ソース内でワイルドカードやファイル一覧を使用すると、同時に複数のファイルを操作できます。

## <a name="data-flow-staging-areas"></a>データ フローのステージング領域

データ フローは、すべてが Azure に存在する "ステージング" データセットを操作します。 これらのデータ フロー データセットは、データ変換を実行するためのデータ ステージングに使用されます。 Data Factory は、約 80 種類のネイティブ コネクタにアクセスできます。 これらの他のソースからのデータをデータ フローに含めるには、まずコピー アクティビティを使用して、そのデータをこれらのデータ フロー データセットのステージング領域のいずれかにステージングします。

## <a name="options"></a>オプション

### <a name="allow-schema-drift"></a>[Allow Schema Drift] (スキーマの誤差を許可)
ソース列が頻繁に変更される場合は、[Allow Schema Drift] (スキーマの誤差を許可) を選択します。 この設定により、ソースからのすべての受信フィールドが変換を通してシンクに流れることができます。

### <a name="validate-schema"></a>[スキーマの検証]

![パブリック ソース](media/data-flow/source1.png "パブリック ソース 1")

ソース データの受信バージョンが定義済みのスキーマに一致しない場合、データ フローの実行は失敗します。

### <a name="sampling"></a>サンプリング
ソースからの行数を制限するには、サンプリングを使用します。  これは、テストやデバッグのためにソース データのサンプルだけが必要な場合に役立ちます。

## <a name="define-schema"></a>スキーマを定義する

![ソース変換](media/data-flow/source2.png "ソース 2")

厳密に型指定されていないソース ファイルの種類 (つまり、Parquet ファイルとは対照的なフラット ファイル) の場合は、このソース変換で各フィールドのデータ型を定義する必要があります。 その後、[選択] 変換で列名を、[派生列] 変換でデータ型を変更できます。 

![ソース変換](media/data-flow/source003.png "データ型")

厳密に型指定されたソースの場合は、次を変更できます。 

### <a name="optimize"></a>最適化

![ソース パーティション](media/data-flow/sourcepart.png "パーティション分割")

ソース変換の [最適化] タブには、[ソース] と呼ばれる追加のパーティション分割の種類が表示されます。 これは、ソースとして Azure SQL DB を選択している場合にのみ表示されます。 これは、Azure SQL DB ソースに対して大規模なクエリを実行するために ADF が接続を並列化しようとするためです。

SQL DB ソースに対するデータのパーティション分割は省略可能ですが、大規模なクエリに役立ちます。 2 つのオプションがあります。

### <a name="column"></a>列

ソース テーブルからパーティション分割する列を選択します。 接続の最大数も設定する必要があります。

### <a name="query-condition"></a>[Query Condition] (クエリ条件)

オプションで、クエリに基づいて接続をパーティション分割することを選択できます。 このオプションでは、単純に WHERE 述語の内容  (year > 1980 など) を入力します。

## <a name="source-file-management"></a>ソース ファイルの管理
![新しいソース設定](media/data-flow/source2.png "新しい設定")

* パターンに一致するソース フォルダーから一連のファイルを選択するためのワイルドカード パス。 これにより、データセット定義で設定したすべてのファイルがオーバーライドされます。
* ファイルの一覧。 ファイル セットと同じです。 処理する相対パス ファイルの一覧と共に、作成するテキスト ファイルを指します。
* ファイル名を格納する列には、データ内の列にあるソースのファイルの名前が格納されます。 ファイル名文字列を格納するには、ここに新しい名前を入力します。
* 完了したら (データ フローの実行後、ソース ファイルに何もしないことを選択できます)、ソース ファイルを削除するか、またはソース ファイルを移動します。 移動のパスは相対パスです。

### <a name="sql-datasets"></a>SQL データセット

ソースとして Azure SQL DB または Azure SQL DW を使用している場合は、追加のオプションがあります。

* クエリ:ソースに対する SQL クエリを入力します。 クエリを設定すると、データセットで選択したすべてのテーブルがオーバーライドされます。 ここでは Order By 句がサポートされないことに注意してください。

* バッチ サイズ: 大量データをバッチ サイズの読み取りにまとめるバッチ サイズを入力します。

> [!NOTE]
> ファイル操作の設定は、パイプライン内のデータ フローの実行アクティビティを使用して、パイプライン実行 (パイプラインのデバッグまたは実行) からデータ フローが実行されている場合にのみ実行されます。 データ フロー デバッグ モードでは、ファイル操作は実行されません。

### <a name="projection"></a>プロジェクション

![プロジェクション](media/data-flow/source3.png "プロジェクション")

データセット内のスキーマと同様に、ソース内のプロジェクションでは、ソース データのデータ列、データ型、およびデータ形式が定義されます。 定義済みのスキーマを含まないテキスト ファイルがある場合は、[データ型の検出] をクリックして、ADF にサンプリングしてデータ型を推測するよう指示します。 [Define Default Format] (既定の形式の定義) ボタンを使用して、自動検出での既定のデータ形式を設定できます。 列のデータ型は、以降の [派生列] 変換で変更できます。 列名は、[選択] 変換を使用して変更できます。

![既定の形式](media/data-flow/source2.png "既定の形式")

## <a name="next-steps"></a>次の手順

[[派生列]](data-flow-derived-column.md) と [[選択]](data-flow-select.md) を使用してデータ変換の構築を開始します。
