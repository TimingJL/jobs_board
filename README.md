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


To be continued...