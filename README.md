# SPARQLによる家庭内で発生する危険な状況の検出
[ナレッジグラフ推論チャレンジ実社会版2022](https://challenge.knowledge-graph.jp/2022/index.html)の応募作品のデータ、およびプログラムをまとめたレポジトリです。

公開内容は以下の通りです。
## 1. リスク検索の実行例
   - 重量を用いた検索  
     http://hozo.jp/KGRC2022ab/weightA.html
   - 時間を用いた検索  
     http://hozo.jp/KGRC2022ab/timeB.html
   - プログラムのソースコード  
     https://github.com/oecu-kozaki-lab/KGRC2022ab/tree/main/src
## 2. 物体の重量に関するデータ
   - 作成したデータ（N-Triple）  
     https://github.com/oecu-kozaki-lab/KGRC2022ab/blob/main/wegiht-ex3.nt
   - SPARQLエンドポイント  
     http://kozaki-lab.osakac.ac.jp/agraph/KGRCext  
     ※プログラムからのアクセス用のため、Webブラウザ等で実行したい場合は  
 　    http://yasgui.triply.cc/　等の利用が必要
## 3. 検索に使用したSPARQLクエリ
**SPARQL Query1**：重さ5kg以上のものつかんでいる（grab）イベントを検索する
```
PREFIX ex: <http://example.org/virtualhome2kg/instance/>
PREFIX vh2kg: <http://example.org/virtualhome2kg/ontology/>
PREFIX vh2kgaction: <http://example.org/virtualhome2kg/ontology/action/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
select DISTINCT * where {
    ?event vh2kg:action vh2kgaction:grab. 
    ?event vh2kg:place ?place.
    ?event vh2kg:mainObject ?mainobject.
    ?mainobject a ?objectClass.   
   service <http://kozaki-lab.osakac.ac.jp/agraph/KGRCext>{
     ?objectClass <http://kozaki-lab.osakac.ac.jp/KGRCext/weight> ?weight .
     ?objectClass rdfs:label ?objLabel.
     FILTER(?weight >= 5)
  }
}

```
**SPARQL Query2**：トイレで5秒以上座っている（sit）イベントを検索する
```
PREFIX ex: <http://example.org/virtualhome2kg/instance/>
PREFIX vh2kg: <http://example.org/virtualhome2kg/ontology/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX vh2kgAction: <http://example.org/virtualhome2kg/ontology/action/>
select ?Temporal ?place (sum(?Duration) as ?sum)  where {
?Event vh2kg:action vh2kgAction:sit;
       vh2kg:time ?Temporal;
       vh2kg:place ?place.
?place a vh2kg:Toilet .
?Temporal <http://www.w3.org/2006/time#numericDuration> ?Duration
FILTER(?Duration > 5)
} Group by ?Temporal ?place 
```
