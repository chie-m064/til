# ransackを使った検索

実装したいこと
* 他のモデルの検索

## モデルの設定
```Post```モデル
```ruby
class Post < ApplicationRecord
  belongs_to :user
  has_many :comments, dependent: :destroy
  has_many :post_tags, dependent: :destroy
  has_many :tags, through: :post_tags

  attr_accessor :tag_names

  after_save :assign_tags

  validates :title, presence: true
  validates :body, presence: true

  ransacker :profile_name do
    Arel.sql("profiles.name")　#Arel.sqlは生のSQL分をRansackに渡すためのメソッド　”profiles.name”でprofilesテーブルのnameカラムを直接参照する
  end
  ransack_alias :comment_body, 'comments.body'　# comments.bodyをけんさくする際に、comment_bodyというエイリアス（別名）をつかえるようにする

  def assign_tags
    if tag_names.present?
      self.tags = tag_names.split(',').map(&:strip).uniq.map do |name|
        Tag.find_or_create_by(name: name)
      end
    end
  end
end
```

## コントローラーの設定
```ruby
# post_controller.rb

def index

    # SQL の INNER JOIN を生成し、Post と関連する User モデル、そのさらに関連する Profile モデルを結合
    # params[:q] の中身（検索フォームから送信されたデータ）を使って、検索条件を構築
    @q = Post.joins(user: :profile).ransack(params[:q])

   # @q.result(distinct: true) 検索条件に一致する Post モデルのデータを取得、stinct: true を指定することで、重複するデータを排除
   # includes(:user, :comments, :tags): 関連データ（User、Comment、Tag）を事前に取得し、N+1 問題を回避
    @posts = @q.result(distinct: true).includes(:user, :comments, :tags).order(created_at: :desc).page(params[:page])
end
```

## ビューの設定
```ruby
# _header.html.erb

<%= search_form_for @q, url: posts_path, method: :get, class: 'd-flex' do |f| %>
  # Post モデルの title または body に対する部分一致検索。
  <%= f.search_field :title_or_body_cont, id: 'search_field_of_post', placeholder: '投稿', class: 'form-control me-2' %>

  # 関連する Comment モデルの body に対する部分一致検索
  <%= f.search_field :comments_body_cont, id: 'search_field_of_comment', placeholder: 'コメント', class: 'form-control me-2' %>

  # 関連する Profile モデルの name に対する部分一致検索
  <%= f.search_field :profile_name_cont, id: 'search_field_of_user', placeholder: 'ユーザー', class: 'form-control me-2' %>
  <%= f.submit 'Search', class: 'btn btn-outline-success' %>
<% end %>
```

```eq``` ：等しい条件にマッチ（全部一致）
```not_eq``` ：等しくない条件にマッチ　
```cont``` ：部分一致する条件にマッチ
```not_cont``` ：部分一致しない条件にマッチ
```start``` ：前方一致する条件にマッチ
```end``` ：後方一致する条件にマッチ
```gt``` ：より大きい条件にマッチ
```lt``` ：より小さい条件にマッチ












































