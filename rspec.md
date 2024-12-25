# RSpec 

```ruby
it '一覧ページにアクセスした場合、Taskが表示されること' do
        # TODO: ローカル変数ではなく let を使用してください
        project = FactoryBot.create(:project)
        task = FactoryBot.create(:task, project_id: project.id)
        visit project_tasks_path(project)
        expect(find('.task_list')).to have_content task.title
        expect(Task.count).to eq 1
        expect(current_path).to eq project_tasks_path(project)
end
```
```ruby
#最初にletで定義することで１回だけでOK
let(:project) { FactoryBot.create(:project) }　#　プロジェクトを１件作成
let(:task) { FactoryBot.create(:task, project_id: project.id) } # プロジェクトに紐づくタスクを１件作成

it '一覧ページにアクセスした場合、Taskが表示されること' do
        # TODO: ローカル変数ではなく let を使用してください
        visit project_tasks_path(project)

　　　/ .task_listの要素の中にtast.titleがあればOK
        expect(find('.task_list')).to have_content task.title

　　　/ データベースに存在するタスクが１件であればOk
        expect(Task.count).to eq 1

　　　/ current_pathは現在表示されているページにパス
　　　/ to eq は　等しい
　　　/ project_tasks_path(project)は project.idが1であれば　projects/1/tasks　を返す
        expect(current_path).to eq project_tasks_path(project)
end
```
