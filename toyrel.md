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

## ToyRelとrelational modelとrelational algebra

RDBの基礎は、relational modelとrelational algebraです。

[Relational algebra - Wikipedia](https://en.wikipedia.org/wiki/Relational_algebra#:~:text=In%20database%20theory%2C%20relational%20algebra,Codd.)

より詳細なRelational algebraについては上記書籍を参照してもらうとして、
ここでは実装上の簡単な説明を行います。

relational modelとは「データベースのテーブルというのは、なんらかの制約を満たしたタプルの集合とする」というモデルです。
rowが重複しないテーブルと思っておけばだいたいよろしい。

そしてrelational algebraというのはこのrelational model同士を演算して新しいrelational modelを作るという合成方法です。
relational algebraの各演算がまたrelational modelを返すので、この演算は代数をなす、という事ですが、
実装的に重要なのは、演算の結果もテーブルも区別がなく同じrelational modelになる、という事です。

ToyRelとしては、relational modelとしてcsvファイルを使います。relationの名前として、csvファイルのbasename、つまり拡張子を取り除いたものを使います。

実装するrelational algebraとしては、以下を実装する事にします。

- project
- restrict (第一回でfilterと呼んでたもの、RAではselectと呼ばれる事も多い)
- union
- difference
- intersect
- rename
- product
- join

本格的なrelational algebra処理系となるので、全仕様を最初に詳細に決めるのは難しいし理解をするのも大変です。
そこで、これを徐々にすすめていく過程で何を作るかを明らかにしていこうと思います。

## tandpディレクトリのデータ紹介

基本的なrelational algebraのサンプルとして、LEAPのソースに含まれている

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00OD5CB50&linkId=031b79722ee8b82c89df4ef320cc8118"></iframe>

の例に使われているデータをsource/data/tandp下に作っておきました。（LEAPのソースはGPLなのでこれを再配布したい時はご注意を）

tandpはLEAPのソースコードで使われていた名前ですが、おそらく書籍のタイトル、Theory and Practice of Relational Databasesの最初のTheory and Practiceを略してt and pから来ていると思います。

LEAPのソースコードに書かれているページ数は手元の第二版とはずれてるので自分の調べたページ数を以下に書いておきます。

**図書館**

- book.csv (p57, Example 4.1)
- index.csv (p57, Example 4.1)
- subject.csv (p57, Example 4.1)
- auction.csv (p60, Example 4.2)

**divideのサンプル(p65, Example 4.7)**

- lc.csv
- q.csv

**ファッション雑貨店の在庫管理データベース(p67〜p68、4.3)**

- goods.csv
- delivery.csv
- stock.csv


TODO: 最初数行のテーブルを書く

TODO: それぞれのテーブルを簡単に説明（書籍にあるのと同じ内容でいいでしょう）


## こんなものを動かしたい、というイメージ

以下のような処理を実行すると、ランダムに生成された名前のrelationが保存されます。

```
> project (シラバス) 専門, 学年
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
> シラバス_専門_学年 = project (シラバス) 専門, 学年
```

「シラバス_専門_学年.csv」というファイルが生成されて、中身は先程と同じ。

あまり考えずに書いているので、実装してみるともうちょっと仕様は変わるかも。


TODO: tandpのデータの説明を足したあとに、tandpでのdifferenceとかjoinの例をここに書く


## プロジェクトの名前と場所

とりあえずプロジェクトの名前はToyRelとしましょう。
作る場所はsourcesの直下にします（PlayDeedleなどより一つ上のディレクトリになることに注意）。

ブランチは`toyrel/...`の形式の名前にしていきましょう。

また、FParsecとDeedleはfsprojに加えておいてください。（Arguは使うか未定なのでまだいいです）

## まずはprojectを簡易的に実装してみる

さて、とりあえず一番簡単な例を作ってみる所から始めます。projectが簡単で良いでしょう。

projectは最終的にはかなり本格的なExpressionの仕様が必要になりますが、いきなり最終形を考えるのは良いプログラムのスタイルではありません。
まずは小さく限定的な仕様から始めて、徐々に本格的に育てていくのが良い。

という事でここでは、暫定的な仕様から始めたいと思います。

### databaseという概念

将来的には複数のデータベースというのを持てるようにしたいと思っています。
データベースはリレーションの集まりです。
ディレクトリをデータベースとして扱いたい。

useでデータベースを切り替える。

例えばシラバスや成績などのデータベースと、住所とオーダーのデータベースなどというような。

ただ当面はとりあえず一つとしたい。

ToyRelのプロジェクトにdatabase/masterというディレクトリを作って、その中にsource/data/tandpのcsvと `シラバス.csv` をコピーするシェルスクリプトを作り、
普段の作業はそれに対してやります。
開発時はちょくちょくクリアしたくなると思うので、スクリプトはディレクトリを削除して作り直す所からやる感じにしておいてください。

setupdb.shとかそんな感じの名前で。別にbatファイルでもps1ファイルでもいいです。

当面はここのパスを決め打ちで作業していきます。
つまり、"シラバス"といったら、`source/ToyRel/database/master/シラバス.csv`を意味します。

### projectの文法

projectは最初の引数がカッコでくくったexpressionというもので、そのあとにカラムの名前のリストが並びます。
expressionは後述するので後回しにして、それ以外は以下のようなイメージ。（厳密な文法という訳じゃないので雰囲気で読み取ってください）

```
project_expression = 'project' expression columnlist

columnlist = column
  | column ',' columnlist

column = '[' string ']'
     | identifier

identifier = nondigitchar normalchar*

nondigitchar = ([a-zA-Z]|p{IsHiragana}|p{IsKatakana}|p{IsCJKUnifiedIdeographs})
normalchar = nondigitchar | [0-9_]
```

stringとかはまぁ適当に(閉じ大かっこ以外とかでいいと思う)。
IsHiraganaとかは以下を参考に。 [.NET 正規表現での文字クラス - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/standard/base-types/character-classes-in-regular-expressions#SupportedNamedBlocks)

注意したい事としては

- 識別子は1文字目数字は禁止とする（パースで数字の即値と区別が難しいのでだいたいは禁止されている）
- カタカナや漢字を使いたいのでその辺は真面目に対応する

という感じで。
なおLEAPではcolumnlistはカッコでくくていたけれど、toyrelではカッコは無しで行きたいと思います。

### expressionの暫定的な仕様

expressionは中を評価するとrelationになっているようなものです。
まず考えるべきは以下の２つです。

- リレーションの名前をそのままカッコでくくったもの
- projectなどの結果

例えば、１つ目の例は以下になります。

```
project (シラバス) 専門, 学年
```

この時、１つ目のカッコの中はリレーションの名前になります。

２つ目の例としては以下のようなものが考えられます。

```
project (project (シラバス) 専門, 学年, 場所) 専門, 学年
```

以上をそのまま書くと以下のようになります。

```
expression =  '(' identifier ')'
           |'(' project_expression ')'
```

さて、これをそのまま実装しようとしてみると、pExpressionを実装する時にはpProjectExpressionが必要で、
pProjectExpressionを実装する時にはpExpressionが必要になってしまいます。

文法的は再帰で定義していくものなので、左再帰で無い限りは再帰は問題無いはずですが、
実際にどのように書けば良いでしょう？

そこで出てくるのが `createParserForwardedToRef` です。

### createParserForwardedToRefを使ってexpressionの再帰を解決する

expressionの定義にはproject_expressionが必要で、project_expressionの定義にはexpressionが必要、
という状態を解決する為の機能として、FParsecには `createParserForwardedToRef()` という関数が提供されています。

これは、実体の無い仮のパーサーを返して、とりあえずそれを使ってルールを定義したあとに、その定義したルールを使って仮のパーサーの実体をあとから定義する、
という事を可能とします。

例として、pExpressionを仮のパーサーとして生成してpProjectExpressionを定義してみましょう。

```
let pExpression, pExpressionRef = createParserForwardedToRef()
```

Refについては後述します。こうやって得た仮のパーサーを使ってpProjectExpressionを定義する。

```
let pProjectExpression = (str "project") >>. pExpression .>>. pColumnList
```

このように、まだ定義していないpExpressionを使ってpProjectExpressionが定義出来ました。

次に、このpProjectExpressionを使ってpExpressionを実際に定義します。
この実際に定義する時に重要になるのが先程 `createParserForwardedToRef()` で得た二番目の戻り、 `pExpressionRef` です。
これは変更という副作用が必要なので通常のletでは無く`:=`を使います。

```
pExpressionRef := (pstring "(") >>. pidentifier .>> (pstring ")")
               <|> (pstring "(") >>. pProjectExpression .>> (pstring ")")
```

この右辺でpProjectExpressionを使っている事に注目してください。

もちろんこのまま書くと、pidentifierとpProjectExpressionの返りがコンパチブルじゃない的なエラーが出てくると思います。
でもその解決はここまで進めた読者なら自分で出来るでしょう。

### 課題1: pExpressionとpProjectExpressionをここまでの仕様で完成させよ

ちゃんと返す型も作ってください。
パースだけでいいです。ちょっと大きめの課題なので、一気に出来ないようなら言って下さい。分割します。

### projectでdfを返す所まで実装してみよう

### rowを一意にする

## リレーションの保存

### ランダムファイル名の生成

### 保存

### 左辺を含むパース

### printの実装

## differenceの実装

### differenceの説明

### 処理の実装

### パース

### 以下を動かす

```
r2=(project (subject) class) difference (project (index) class)
print r2
```