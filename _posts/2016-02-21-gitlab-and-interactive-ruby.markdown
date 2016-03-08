---
layout: post
title:  "Interactive Ruby used for Gitlab API"
date:   2016-02-21
categories: ruby
---

TL;DR: Gitlab has a simple API which has a few caveats (paging). This leads to a need to use some scripting language, rather than simple combination of `curl` and `jq`. This post shows off how inline evaluation makes development of scripts easier and faster.

#### Gitlab API

I'm interested in getting a list of projects and their git SSH paths. To do that I'm going to use the `/api/v3/projects` endpoint ([API here](http://doc.gitlab.com/ce/api/projects.html#list-projects)). To use the API, you have to obtain a "private token", which can be found on your Gitlab account.

During a short test of the API, it quickly turned out that the response did not contain all the entries. The responses are paged (silently), so I had to collect all the pages before going any further. Paging is controlled via `page` and `per_page` query params, see [here](http://doc.gitlab.com/ce/api/#pagination):

    /api/v3/projects?page=1&per_page=100

#### Interactive Ruby

So what's the interactive fuss all about? I've set up Vim using instructions from [Jack Franklin's](https://github.com/jackfranklin) [tilvim.com article](http://tilvim.com/2013/06/24/live-ruby-execution.html). Then I fire up a new Ruby file and stick in the following code to load credentials and URL using .env file:

```ruby
require 'dotenv'

# Dotenv loads .env file from the folder and sets values of ENV hash to these.
# This way you can easily separate tokens, usernames, etc. from your code.
Dotenv.load

gitlab_token = ENV["GITLAB_TOKEN"]
gitlab_url = ENV["GITLAB_URL"] + "/api/v3"
```

Now I can mark the two last lines to show output using <F3> and then press <F4> to execute script. That's what I get:

![Inline Ruby execution 1st example](/images/gitlab-ruby/gitlab-ruby1.png)

Let's now create a Faraday connection and try to get the response from `/api/v3/projects` endpoint:

```ruby
gitlab_conn = Faraday.new(url: gitlab_url)
response = gitlab_conn.get "projects", page: 1, per_page: 100 # =>
```

*Mark the last line* and run Ruby inline. This will quickly lead us to discover that we forgot to include the private token, due to HTTP 401 error:

![Inline Ruby execution 2nd example](/images/gitlab-ruby/gitlab-ruby2.png)

Update previous snippet to include `PRIVATE-TOKEN` header in Faraday's connection constructor and run it again:

```ruby
gitlab_conn = Faraday.new(url: gitlab_url, headers: {"PRIVATE-TOKEN": gitlab_token})
response = gitlab_conn.get "projects", page: 1, per_page: 100 # =>
```

![Inline Ruby execution 3rd example](/images/gitlab-ruby/gitlab-ruby3.png)

#### The Loop

We're now sure that the connection to the API works and we've examined the structure. Now we can write simple loop code to get all pages together and run it:

```ruby
all_projects = []
page_index = 1

loop do
  response = gitlab_conn.get "projects", page: page_index, per_page: 100
  projects = JSON.parse(response.body)
  break if projects.empty?
  all_projects.concat(projects)
  page_index += 1
end

page_index # =>
all_projects.size  # =>
```

And here's the result:

![Inline Ruby execution 4th example](/images/gitlab-ruby/gitlab-ruby4.png)

#### Wrap-up

Inline code execution is a great way to explore while you prototype and feels a bit more natural than going to REPL. If you happen to use Sublime Text or emacs rather than vim, take a look here on ideas how to set it up for you: [http://dgmstuart.github.io/blog/2015/03/27/execute-ruby-code-inline-in-the-editor](http://dgmstuart.github.io/blog/2015/03/27/execute-ruby-code-inline-in-the-editor/).

