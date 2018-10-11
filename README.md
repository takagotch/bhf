### bhf
---

https://github.com/antpaw/bhf

http://antpaw.github.io/bhf/

```
gem 'bhf'

```

```ruby
# config/initializers
Bhf.configure do |config|
  config.on_login_fail = :login_url
end
mount Bhf::Engine, at: 'bhf'

get 'admin', to: 'admin#login', as: :admin
class AdminController < ApplicationController
  http_basic_authenticate_with name: 'admin', password: 'bhf'
  def login
    session[:is_admin] = true
    redirect_to bhf.root_url
  end
end
class Bhf::ApplicationController < ActionController::Base
  protect_from_forgery
  before_filter :authenticate_user!
  include Bhf::Extension::ApplicationController
  def check_admin_account
    current_user.is_admin?
  end
end

Bhf.configure do |config|
end

# schema.rb
create_table "", force: true do |t|
  t.string "identifier"
  t.string "name"
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
end
# user.rb
create_table "roles_users", id: false, force: true do |t|
  t.integer "role_id"
  t.integer "user_id"
end

# config/initializers/bhf.rb
config.css << 'my_custom_bhf'

# routes.rb
Bhf::Engine.routes.draw do
  patch 'stadium/entries/:id', to: 'tournaments#stadium_update', defaults: {platform: 'stadium'}
  get 'tournamnts/entries/new', to: 'tournaments#new', defaults: {platform: 'tournaments'}
end

# app/controllers/bhf/tournament_controller.rb
class Bhf::TournamentsController < Bhf::EntriesController
  def new
    @object.save
    redirect_to edit_entry_path(@platform.name, @object)
  end
  def stadium_update
  end
end

# application.rb
config.i18n.default_locale = :de
config.i18n.fallbacks = [:en]

config.time_zone = 'Berlin'


#routes.rb
mount Bhf::Engine, at: 'bhf/:bhf_area', as: :bhf, defaults: { bhf_area: 'main' }
get 'bhf', to: 'application#redirect_to_users_bhf_area', as: :bhf_help
#schma.rb
create_table "areas", force: true do |t|
  t.string "identifier"
  t.string "name"
  t.string "link"
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
end
create_table "areas_users", id: false, true do |t|
  t.integer "area_id"
  t.integer "user_id"
end

#user.rb
class User < ActiveRecord::Base
  has_and_belongs_to_many :areas
  def bhf_area
    areas
  end
end

#application_controller.rb
class ApplicationController < ActionController::Base
  def redirect_to_users_bhf_area
    r = if current_user
      current_user.bhf_areas.first.identifier
    else
      'main'
    end
    redirect_to bhf.root_url(bhf_area: r)
  end
end

```

```
pages:
- my_page:
  - my_model:
  this project (lowcase)
  
pages:
- statics:
  - statics:
tatic
-posts:
  - posts:
    table:
      source: all_posts
      display: [id, headline, subheadline, content, category, published, published_at]
    form:
      display: [id, category, author, headline, subheadline, content, published, published_at]
      types:
        content: markdown
- settings:
  - category:
  -authors:
    table:
      sortable: position
      hide_delete: true
    from:
      display: [id, name, job_title, email]
      
pages:
- page:
  - authors:
    cutom_link: entry_path

pages:
- page:
  - best_authors:
    model: authors

pages:
- posts:
  - posts:
    form:
      display: [id, name, demo_checkbox]
   
= node f, field do
  = f.hidden_field :test
  = field.name
  
window.addEvent('bhfDomChunkReady', function(scope){
  console.log(scope);
});

pages:
- settings:
  - author:
    form:
      display: [_id, name, job_title, email, categories]
      links:
        categories: best_categories
  - best_categories:
    model: Category
    
.bhf_edit
  display: block
  float: right
  width: 40px
  height: 40px
  text-indent: 100%
  white-space: nowrap
  overflow: hidden
  background: image-url('bhf_pen.svg') no-repeat center center
  background-size: 80%
  border: 2px solid transparent
  border-radius: 5px
  padding: 5px
  &:hover
    border-color: #38A5E9
```


