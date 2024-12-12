# 掲示板/ユーザのCRUD機能の作成

## 概要
* ユーザーのCRUD・検索
  * 検索項目：
    * 名前
    * 権限（一般 or 管理者 のプルダウン形式）
* 掲示板のCRUD・検索
  * 検索項目：
    * フリーテキスト（タイトル or 本文に含まれているかどうかを判定）
    * 作成日の範囲指定

### メニューのアクティブ・非アクティブ化
```ruby
- app/helpers/application_helper.rb

module ApplicationHelper
  ... 省略 ...

  def active_if(path)
    path == controller_path ? 'active' : ''
  end
end
```
active_ifメソッドは、指定されたパスが現在のコントローラのパスと一致する場合にCSSクラス'active'を返し、一致しない場合には空文字列を返します。これはナビゲーションメニューの現在のページを視覚的に強調するためによく使用されます。例えば、特定のリンクが現在のページを指している場合、そのリンクに'active'クラスを追加することで、そのリンクが現在選択されていることを示します。

例：<%= link_to 'Home', root_path, class: active_if('home') %>

上記の例では、コントローラのパスが'home'である場合、リンクに'active'クラスが追加され、スタイルが適用されます。
