# Learning by Doing 

![Ubuntu version](https://img.shields.io/badge/Ubuntu-16.04%20LTS-orange.svg)
![Rails version](https://img.shields.io/badge/Rails-v5.0.0-blue.svg)
![Ruby version](https://img.shields.io/badge/Ruby-v2.3.1p112-red.svg)

Mackenzie Child's video really inspired me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# Project 7: How To Build A Jobs Board With Rails       
This time we build a Jobs Board Applicatoin. We're able to add multiple job postings and categories. We have four different categories like full-time, part-time, freelance, and consultant. We have the ability to Add, Edit, Destroy jobs postings and each job post is assigned to a category, and we are able to filter between the various categories to see only jobs within that category.


https://mackenziechild.me/12-in-12/7/         




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

# Delete And Edit(19:08)
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

Next thing we want to do is to add categories for our jobs. So to 

To be continued...