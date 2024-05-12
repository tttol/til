# React.jsにおけるテスト
- Unit testing
  - Component testing
    - Reactコンポーネント単位でテストする
    - コンポーネントがどのようにレンダリングされるか、propsによってレスポンスがどう変わるか
  - Integration testing
    - コンポーネント同士の結合テスト
- E2E testing
  - ユーザーのシナリオに沿ってテストする
  - 例：サインアップフロー等
- Snapshot testing
  - あるバージョンにおけるレンダリングされたコンポーネントの結果をキャプチャしファイルに保存する
  - 次回テスト実行時のスナップショットと比較して予期せぬ変更が生じてないかをチェックする


引用：https://nextjs.org/docs/app/building-your-application/testing
> - Unit testing involves testing individual units (or blocks of code) in isolation. In React, a unit can be a single function, hook, or component.
  > - Component testing is a more focused version of unit testing where the primary subject of the tests is React components. This may involve testing how components are rendered, their interaction with props, and their behavior in response to user events.
  > - Integration testing involves testing how multiple units work together. This can be a combination of components, hooks, and functions.
> - End-to-End (E2E) Testing involves testing user flows in an environment that simulates real user scenarios, like the browser. This means testing specific tasks (e.g. signup flow) in a production-like environment.
> - Snapshot testing involves capturing the rendered output of a component and saving it to a snapshot file. When tests run, the current rendered output of the component is compared against the saved snapshot. Changes in the snapshot are used to indicate unexpected changes in behavior.


# zenigameでvitestが実行できない
vercelのGetting Started -> https://nextjs.org/docs/app/building-your-application/testing/vitest
### ReferenceError: React is not defined
```bash
 FAIL  __tests__/page.test.tsx > Home
ReferenceError: React is not defined
 ❯ __tests__/page.test.tsx:5:12
      3| import Home from '../app/page'
      4| test('Home', () => {
      5|     render(<Home />)
       |            ^
      6|     expect(screen.getByRole('heading', { level: 1, name: 'ZENIGAME' })).toBeDefined()
      7| })
```
- tsconfig.jsonでcompilerOptions.jsxに`react-jsx`を指定する
  - 公式DOC：https://www.typescriptlang.org/tsconfig/#jsx
- Copilotの回答
```
compilerOptionsのjsxプロパティは、TypeScriptがJSX構文をどのように扱うかを指定します。JSXは、Reactコンポーネントを記述するための構文で、HTMLに似た形式を持っています。

jsxプロパティには以下の3つの値を設定することができます：

"preserve": JSX部分をそのまま保持します。出力ファイルの拡張子は.jsxになります。このオプションは、後続の変換ステップ（例えばBabel）でJSXを扱う場合に使用します。

"react": JSXをReact.createElement呼び出しに変換します。これはReact 16以前で使用されていた変換方法です。

"react-jsx": JSXをReact.jsx（またはReact.jsxs）呼び出しに変換します。これはReact 17以降で導入された新しいJSX Transformです。このオプションを使用すると、JSXを使用するファイルでReactをインポートする必要がなくなります。
```
- もともと`preserve`になっており、これだとReactコンポーネントで`import React from 'react';`が不要になる。
- 実際、Sum.tsxで`import React from 'react';`を削除しても問題なくアプリが動作した

### ReferenceError: document is not defined
```bash
 FAIL  __tests__/page2.test.tsx > Page2
ReferenceError: document is not defined
 ❯ Proxy.render node_modules/@testing-library/react/dist/pure.js:239:5
 ❯ __tests__/page2.test.tsx:6:3
      4|  
      5| test('Page2', () => {
      6|   render(<Page2 />)
       |   ^
      7|   expect(screen.getByRole('heading', { level: 1, name: 'Home' })).toBeDefined()
      8| })
```
原因：vitest.config.tsのファイル名をtypoしていただけだった・・・