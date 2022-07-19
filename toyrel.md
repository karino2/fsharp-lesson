---
title: "第二回 Relational Algebraの処理系、ToyRelを作ろう"
layout: page
---
第二回では [LEAP RDBMS : Home](http://leap.sourceforge.net/) のようなrelational algebraの処理系を作ってみます。
いわゆるリレーショナルデータベースのおもちゃを作る感じです。
名前をtoyのrelational algebraという事でToyRelと呼ぶ事にします。

なおLEAPを題材にRelational Algebraやデータベースの基礎を教える本として、以下があります。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00OD5CB50&linkId=031b79722ee8b82c89df4ef320cc8118"></iframe>

ちょっと高くて古い本ですが、ちゃんと基本的なrelational modelやrelational algebra関連の理論がまとまっていながら不要に難しいと思う所はちゃんとカットしていたりと悪くない本なので興味があったらどうぞ。
自分は第二回の内容を書く為に買いました。

なお、第二回をやるのに特にこの本の内容を前提にはしません。
上のリンクから買ってくれるとちょっとアフィリエイトが入って嬉しいくらい。
牛の本とか持ってるなら買わなくてもいいと思います（自分は昔読んだが捨ててしまって手元に無い…）

## toyrelとrelational model

RDBの基礎は、relational modelとrelational algebraです。詳細は上記書籍を参照してもらうとして、
ここでは実装上の簡単な説明を行います。

relational modelとは、データベースのテーブルというのは、なんらかの制約を満たしたタプルの集合とする、というモデルです。
rowが重複しないテーブルと思っておけばだいたいよろしい。

そしてrelational algebraというのはこのrelational model同士を演算して新しいrelational modelを作るという合成方法です。
relational algebraの各演算がまたrelational modelを返すので、この演算は代数をなす、という事ですが、
実装的に重要なのは、演算の結果もテーブルも区別がなく同じrelational modelになる、という事です。

toyrelとしては、relational modelとしてcsvファイルを使います。relationの名前として、csvファイルのbasename、つまり拡張子を取り除いたものを使います。

実装するrelational algebraとしては、以下を実装する事にします。

- project
- select
- union
- difference
- intersect
- product
- join

本格的なrelational algebra処理系となるので、全仕様を最初に詳細に決めるのは難しいし理解をするのも大変なので、これを徐々にすすめていこうと思います。

## こんなものを動かしたい、というイメージ

以下のような処理を実行すると、ランダムに生成された名前のrelationが保存されます。

```
> project (シラバス) (専門, 学年)
Relation zzybac returned.
```

名前はこの場合zzybac.csvというファイルになります。
リレーションはprintで表示出来ます。

```
> print zzybac
専門   学年
----  ----
数学    1
物理    1
数学    2
```

同じ行は削除される事に注意。

リレーションは変数に入れる事もできる。
以下のような文を実行すると、

```
> シラバス_専門_学年 = project (シラバス) (専門, 学年)
```

「シラバス_専門_学年.csv」というファイルが生成されて、中身は先程と同じ。

TODO: 生徒と成績のテーブルを作って、それと合わせてなんかjoinしたりとかdifferenceしたりとかする例を書く。

あまり考えずに書いているので、実装してみるともうちょっと仕様は変わるかも。

## プロジェクトの名前と場所

とりあえずプロジェクトの名前はToyRelとしましょう。
作る場所はsourcesの直下にします（PlayDeedleなどより一つ上のディレクトリになることに注意）。

ブランチは`toyrel/...`の形式の名前にしていきましょう。

また、FParsecとDeedleはfsprojに加えておいてください。（Arguは使うか未定なのでまだいいです）

## まずはprojectの仕様を暫定的に考える

さて、とりあえず一番簡単な例を作ってみる所から始めます。projectが簡単で良いでしょう。

projectは最終的にはかなり本格的なExpressionの仕様が必要になりますが、いきなり最終形を考えるのは良いプログラムのスタイルではありません。
まずは小さく限定的な仕様から始めて、徐々に本格的に育てていくのが良い。

という事でここでは、暫定的な仕様から始めたいと思います。

### databaseという概念

### projectの文法

### expressionの暫定的な仕様


## 実装するものの概要

以下は自分向けメモ。あとでもうちょっと整理して、上のコンテンツに統廃合していく。

- project
- select
- union
- difference
- intersect
- product
- join

relationは全てcsvで保存される。
toyrel上では拡張子を取り除いたものがrelationの名前として使われる。

```
> シラバスcls = project (シラバス) (class)
> インデックスcls = project (インデックス) (class)
> result = difference (シラバスcls) (インデックスcls)
```

```
> シラバスと建物 = join (シラバス) (建物) (シラバス.専門 = 建物.所属)
```

relationは全てその場で適当に作られたランダムな名前のファイルに保存される。
左辺がある場合はその名前のcsvに保存される。

```
> project (シラバス) (専門, 学年)
Relation zzybac returned.
> print zzybac
専門   学年
----  ----
数学    1
物理    1
数学    2
```

printでリレーションを表示する。
同じ行は削除される事に注意。

{% capture comment1 %}
**コメントテスト**  
これは何か囲み記事

空行も使える
{% endcapture %}
{% include myquote.html body=comment1 %}



