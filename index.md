---
title: "トップページ"
layout: page
---

karino2の暇つぶしプログラム教室のF#編のページです。

理論上がりの人が機械学習屋になってPythonでとりあえずコードは書けるようになった、くらいの人で、
そろそろプログラムって奴を本格的にやってみるか、と思った人を対象に、karino2が暇つぶし程度にプログラムを教える、という事をやるサイトです。

機械学習屋になりたい人向けではなくて、すでになっている人に対してのプログラムの教室です。機械学習は扱いません。

[姉妹サイト: karino2の暇つぶしプログラム教室 C言語編(通称c-lesson)](https://karino2.github.io/c-lesson)

## モチベーションや背景など

[モチベーションとか背景とか](motivation.md)

## 進め方

適当に課題をやってもらいつつ[gitter](https://gitter.im/karino2_program_lesson/fsharp-lesson)で質問に答えたり添削したりして、
たまに補足したり説明した方がいいと思う事は本を紹介したり説明をしたり動画を作ったりします。

とりあえず始める人はgitterで自己紹介くらい簡単にしてくれると嬉しいです。

課題は https://github.com/karino2/fsharp-lesson.git をフォークして、このサイトの指示に従ってやってもらいます。

基本的には相手の反応見てから進め方を考えます。

## やる事

- csvに対してリレーショナルアルジブラを実行する[LEAP](https://karino2.github.io/RandomThoughts/LEAP)みたいなシステムを作る
- 簡単なKVS（キーはファイルパスくらいの長さの文字列、値はint64のみ、くらいの制約のハッシュのKVS）
- バイグラムのインデクサとそれを使った全文検索（DocIDに上記KVMを使い、インデックスはファイルシステムをgitみたいに使う）

c-lessonと同様、あくまで作るのは学習目的で実用性は無いものにする。
その代わり題材的な面白さと、その理解が実務の理解を深めるようなものを選びたい。

KVSはちょっと低レベルすぎる気もするので、最初は[ztittle/Qdbm.Net: .NET port of the QDBM Embedded Database](https://github.com/ztittle/Qdbm.Net)この辺を使ってインデクサを作り、
そこまでの分量を見て物足りなかったら自作して置き換えるでもいいかもしれない。

## 事前準備

- 簡単なgitの使い方
  - [https://git-scm.com/book/ja/v2/](https://git-scm.com/book/ja/v2/)の3章くらいまでやっておく
- F#の入門（概要くらいは）
  - [F# syntax in 60 seconds - F# for fun and profit](https://fsharpforfunandprofit.com/posts/fsharp-in-60-seconds/)
  - [F# のツアー - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/fsharp/tour)

F# for fun and profitはそのほかの記事もいろいろ読む事をおすすめするが、
あまり前提にはしないつもり。

## 第一回 まずはライブラリをつついてみよう

初回は進め方とかのチュートリアル的に、簡単な事をやってもらう。DeedleとArguとFParsecを触ってもらう感じ。環境設定とかそういう話。

[第一回 まずはライブラリをつついてみよう](play_library.md)

