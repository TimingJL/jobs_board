# Learning by Doing 

![Ubuntu version](https://img.shields.io/badge/Ubuntu-16.04%20LTS-orange.svg)
![Rails version](https://img.shields.io/badge/Rails-v5.0.0-blue.svg)
![Ruby version](https://img.shields.io/badge/Ruby-v2.3.1p112-red.svg)

Mackenzie Child's video really inspired me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.             

![image](https://github.com/TimingJL/jobs_board/blob/master/pic/machenziechild.jpeg)

# Project 7: How To Build A Jobs Board With Rails       
This time we build a Jobs Board Applicatoin. We're able to add multiple job postings and categories. We have four different categories like full-time, part-time, freelance, and consultant. We have the ability to Add, Edit, Destroy jobs postings and each job post is assigned to a category, and we are able to filter between the various categories to see only jobs within that category.


https://mackenziechild.me/12-in-12/7/         


![image](https://github.com/TimingJL/jobs_board/blob/master/pic/jobs_board.jpeg)

### Highlights of this course
1. Posts (Jobs)
2. Categories
3. Filtering
4. Bootstrap
5. HAML


# Create A Jobs Board
```console
$ rails new jobs_board
```

Chage directory to the pin_board. Under `Gemfile`, add `gem 'therubyracer'`, save and run `bundle install`.      

Note: 
Because there is no Javascript interpreter for Rails on Ubuntu Operation System, we have to install `Node.js` or `therubyracer` to get the Javascript interpreter.

```console
$ bundle install
```

Then run the `rails server` and go to `http://localhost:3000` to make sure everything is correct.


# Add the Ability to Create a Job
First thing we wanna do is add the ability to create a job.  We need to first create a model. And then a controller as well as some routes.

### Create a Model
```console
$ rails g model job title:string description:text company:string url:string
$ rake db:migrate
```

### Add a Controller
```console
$ rails g controller jobs
```

We know we're gonna list out all of the jobs. So first we have to create a index action.     
In `app/controllers/jobs_controller.rb`
```ruby
class JobsController < ApplicationController
	def index
	end
end
```

In `config/routes.rb`
```ruby
Rails.application.routes.draw do
  resources :jobs
  root 'jobs#index'
end
```


### Some Rubygems Install
From here, we're gonna to use `simple_form` rubygems for our forms.       
And we also want to use `HAML` gems and `bootstrap` gems. So let's add these gems in our `Gemfile` and install:
```
gem 'simple_form', github: 'kesha-antonov/simple_form', branch: 'rails-5-0'
gem 'haml', '~> 4.0.5'
gem 'bootstrap-sass', '~> 3.2.0.2'
```
Then we go to our terminal and run `bundle install`, and restart our server.

#### Bootstrap
https://github.com/twbs/bootstrap-sass          

To use bootstrap-sass, first we need to rename `application.css` to `application.css.scss` in `app/assets/stylesheets/`.       
And then import Bootstrap in `app/assets/stylesheets/application.css.scss`:
```scss
@import "bootstrap-mincer";
@import "bootstrap";
```
Note:        
File to import not found or unreadable: 
bootstrap-sprockets Only for Twitter Bootstrap 3, bootstrap-sprockets is used.


Then in `app/assets/javascripts/application.js`, we import `//= require bootstrap-sprockets` under `//= require jquery` like:
```js
//= require jquery
//= require jquery_ujs
//= require bootstrap-sprockets
//= require turbolinks
//= require_tree .
```

#### Simple Form
https://github.com/plataformatec/simple_form        

In our terminal, we need to run the generator.     
Simple Form can be easily integrated to the Bootstrap. To do that you have to use the bootstrap option in the install generator, like this:
```console
rails generate simple_form:install --bootstrap
```


```
=========================================================
  Be sure to have a copy of the Bootstrap stylesheet available on your
  application, you can get it on http://getbootstrap.com/.

  Inside your views, use the 'simple_form_for' with one of the Bootstrap form
  classes, '.form-horizontal' or '.form-inline', as the following:

    = simple_form_for(@user, html: { class: 'form-horizontal' }) do |form|
=========================================================
```



In `app/views/jobs`, we create a new file named `index.html.haml`
```haml
%h1 This is the index page
```


Then we're going to define all of the actions that we're going to use for the jobs board.
In `app/controllers/jobs_controller.rb`
```ruby
class JobsController < ApplicationController
	before_action :find_job, only: [:show, :edit, :update, :destroy]
	
	def index
	end

	def show
	end

	def new
		@job = Job.new
	end

	def create
		@job = Job.new(jobs_params)

		if @job.save
			redirect_to @job
		else
			render "New"
		end
	end

	def edit
	end

	def update
	end

	def destroy
	end

	private

	def jobs_params
		params.require(:job).permit(:title, :description, :company, :url)
	end

	def find_job
		@job = Job.find(params[:id])
	end
end
```

# Create A Job
Next thing we need to do is add some views. So under `app/views/jobs`, let's create a new file called `new.html.haml`, and also another one called `_form.html.haml`.

In `app/views/jobs/_form.html.haml`
```haml
= simple_form_for(@job, html: { class: 'form-horizontal'}) do |f|
	= f.input :title, label: "Job Title", input_html: { class: "form-control" }
	= f.input :description, label: "Job Description", input_html: { class: "form-control" }
	= f.input :company, label: "Your Company", input_html: { class: "form-control" }
	= f.input :url, label: "Link to Job", input_html: { class: "form-control" }
	%br/
	= f.button :submit
```

And go to `app/views/jobs/new.html.haml`
```haml
.col-md-8.col-md-offset-2
	.row
		.panel.panel-default
			.panel-heading
				%h1 New Job
			.panel-body
				= render 'form'
				= link_to "Back", root_path
```

So let's go to `http://localhost:3000/jobs/new`
![image](https://github.com/TimingJL/jobs_board/blob/master/pic/new_job.jpeg)

Then under `app/views/jobs`, let's create a new file called `show.html.haml` to show up our job.
```haml
#jobs
	.job
		%h2= @job.title
		%p= simple_format @job.description
		%p= @job.company

#links
	= link_to "Home", root_path, class: "btn btn-sm btn-default"
```
![image](https://github.com/TimingJL/jobs_board/blob/master/pic/basic_show.jpeg)


So new on the index page, let's list out all of the jobs we have.     
In our controller `app/controllers/jobs_controller.rb`
```ruby
def index
	@jobs = Job.all.order("created_at DESC")
end
```


In `app/views/jobs/index.html.haml`
```haml
#jobs
	- @jobs.each do |job|
		.job
			%h2= link_to job.title, job
			%p= job.company
= link_to "New Job", new_job_path
```
![image](https://github.com/TimingJL/jobs_board/blob/master/pic/jobs_list_out.jpeg)

# Delete And Edit
In our controller `app/controllers/jobs_controller.rb`
```ruby
def update
	if @job.update(jobs_params)
		redirect_to @job
	else
		render "Edit"
	end
end

def destroy
	@job.destroy
	redirect_to root_path
end
```

Then, in our show page `app/views/jobs/show.html.haml`, let's add more links
```haml
#jobs
	.job
		%h2= @job.title
		%p= simple_format @job.description
		%p= @job.company

#links
	= link_to "Home", root_path, class: "btn btn-sm btn-default"
	= link_to "Edit", edit_job_path(@job), class: "btn btn-sm btn-default"
	= link_to "Delete", job_path(@job), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-sm btn-default"
```

Let's create a new file under `app/views/jobs/`,  then save it as `edit.html.haml`
```haml
.col-md-8.col-md-offset-2
	.row
		.panel.panel-default
			.panel-heading
				%h1 Edit Job
			.panel-body
				= render 'form'
				= link_to "Back", root_path
```

# Categories 
Next thing we want to do is to add categories for our jobs.    
First thing we need to create a model.
```console
$ rails g model category name:string
$ rake db:migrate
```

Next, what we wanna do is add the category ID  column to our job's table.
```console
$ rails g migration add_category_id_to_jobs category_id:integer
$ rake db:migrate
```

Then we need to add association between our categories and our job's model.       
In `app/models/category.rb`
```ruby
class Category < ApplicationRecord
	has_many :jobs
end
```


In `app/models/job.rb`
```ruby
class Job < ApplicationRecord
	belongs_to :category
end
```

So let's go into our rails console:
```console
$ rails c

> Category.connection
> Category
> Category.create(name: "Full Time")
> Category.create(name: "Part Time")
> Category.create(name: "Freelance")
> Category.create(name: "Consulting")

> Category.all
```

Next, what we want to do is for each new job that is created we want to add the category ID to that specific job.       
In `app/views/jobs/_form.html.haml`
```haml
= simple_form_for(@job, html: { class: 'form-horizontal'}) do |f|
	= f.collection_select :category_id, Category.all, :id, :name, {promt: "Choose a category" }, input_html: { class: "dropdown-toggle" }
	= f.input :title, label: "Job Title", input_html: { class: "form-control" }
	= f.input :description, label: "Job Description", input_html: { class: "form-control" }
	= f.input :company, label: "Your Company", input_html: { class: "form-control" }
	= f.input :url, label: "Link to Job", input_html: { class: "form-control" }
	%br/
	= f.button :submit
```
![image](https://github.com/TimingJL/jobs_board/blob/master/pic/category.jpeg)


Next, let's go to the `app/controllers/jobs_controller.rb`, we need to add the `category_id` in private action `jobs_params`.
```ruby
def jobs_params
	params.require(:job).permit(:title, :description, :company, :url, :category_id)
end
```

### Filter
So, to filter those, we need to first list out all the categories. And then we need to change the index method inside of our jobs_controller to add a params to filter by.       

First, we rename `application.html.erb` to `application.html.haml` under `app/views/layouts/`.
```haml
!!!
%html
%head
	%title Ruby on Rails Jobs
	= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true
	= javascript_include_tag 'application', 'data-turbolinks-track' => true
	= csrf_meta_tags

%body
	%nav.navbar.navbar-default
		.container
			.navbar-brand Rails Jobs
			%ul.nav.navbar-nav
				%li= link_to "All Jobs", root_path
				- Category.all.each do |category|
					%li= link_to category.name, jobs_path(category: category.name)
			= link_to "New Job", new_job_path, class: "navbar-text navbar-right navbar-link"
	.container
		.col-md-6.col-md-offset-3
			= yield
```

Next, inside of our controller `app/controllers/jobs_controller.rb`
```ruby
def index
	if params[:category].blank?
		@jobs = Job.all.order("created_at DESC")
	else
		@category_id = Category.find_by(name: params[:category]).id
		@jobs = Job.where(category_id: @category_id).order("created_at DESC")
	end
end
```    
![image](https://github.com/TimingJL/jobs_board/blob/master/pic/filter.jpeg)


# Basic Styling
What we need to do next is styling.
In `app/assets/stylesheets/application.css.scss`
```scss
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or vendor/assets/stylesheets of plugins, if any, can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any styles
 * defined in the other CSS/SCSS files in this directory. It is generally better to create a new
 * file per style scope.
 *
 *= require_tree .
 *= require_self
 */

@import "bootstrap-sprockets";
@import "bootstrap";

* {
	box-sizing: border-box;
}

html {
	height: 100%;
}

body {
	height: 100%;
  background: -webkit-linear-gradient(90deg, #1D976C 10%, #93F9B9 90%);
  background:    -moz-linear-gradient(90deg, #1D976C 10%, #93F9B9 90%);
  background:     -ms-linear-gradient(90deg, #1D976C 10%, #93F9B9 90%);
  background:      -o-linear-gradient(90deg, #1D976C 10%, #93F9B9 90%);
  background:         linear-gradient(90deg, #1D976C 10%, #93F9B9 90%);
  font-family: 'Lato', sans-serif;
}

.clearfix:before,
.clearfix:after {
  content: " ";
  display: table;
}

.clearfix:after {
  clear: both;
}

.navbar-default {
	background-color: white;
	border-radius: 0;
	height: 80px;
	padding: 15px 0;
	border: none;
}

.navbar-brand {
	text-transform: uppercase;
	letter-spacing: -1px;
	font-size: 2em;
	font-weight: 300;
	color: #1D976C !important;
}

#jobs {
	.job {
		padding: 1em 0;
		border-bottom: 1px solid rgba(250,250,250, 0.5);
		h2 {
			font-size: 2.5em;
			font-weight: 300;
			margin-bottom: 0;
			color: white;
			a {
				color: white;
			}
		}
		p {
			color: rgba(250,250,250, 0.5);
		}
	}
}

#links {
	margin-top: 2em;
}
```

In `app/views/jobs/show.html.haml`
```haml
#jobs
	.job
		%h2= @job.title
		%p= @job.description
		%p= @job.company
		%button.btn.btn-default= link_to "Apply for Job", @job.url

#links
	= link_to "Back", root_path, class: "btn btn-sm btn-default"
	= link_to "Edit", edit_job_path(@job), class: "btn btn-sm btn-default"
	= link_to "Delete", job_path(@job), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-sm btn-default"
```

Finished!
