# 参考
- [一言で理解するReact Server Components](https://zenn.dev/uhyo/articles/react-server-components-multi-stage)
- [ざっくりApp Router入門【Next.js】](https://zenn.dev/yamadadayo123/articles/6cb4f586de0183)

# React Server Components(RSC)とは
> 一言で言うと、React Server Componentsは**多段階計算**です。

- 多段階計算
  - 「プログラムの評価を多段階に分けて処理する機構」
  - 「動的にコードを生成してそれを走らせる機構を備えた，計算が複数のステージからなる意味論を備えた体系（＋それを安全に行うための型システム）」
  - stage 0, stage 1と表現する
- Reactアプリケーションはサーバーで実行される部分とクライアントで実行される部分がある
  - stage 0: サーバー
  - stage 1: クライント
- サーバーサイド・クライアントサイドのビルドの違い
  - サーバーサイド  
    - クライント側でのビルドがないので表示速度が高速
    - 先にサーバーでビルドするので、SEOの観点からもGOOD
  - クライアントサイド
    - 表示は低速になる恐れがある
    - ユーザーの操作に応じたフィードバックが可能
    - リクエスト情報・クッキー等を用いた動的処理も同様
# Next.jsにおける扱い
- App Routerからはすべてのコンポーネントがデフォルトでサーバーコンポーネントになった
- App Router / Page Router
  - App Router
    - Next.js 13.4〜
    - appディレクトリ直下のpage.tsxのみがルーティング対象となる
  - Page Router
    - pages直下に作ったファイルがすべてルーティング対象だった
- クライアントコンポーネントで使いたい場合はファイル先頭に`"use client"`を記載する
- 