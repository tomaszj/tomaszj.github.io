---
layout: post
title:  "jq for JSON processing with GitHub API"
date:   2016-03-04
categories: command-line utilities
---

In previous [post](/ruby/2016/02/21/gitlab-and-interactive-ruby.html) I've presented a case when scripting language is useful. More often that not, a command-line JSON processor would be enough. Ladies and Gentlemen, please welcome `jq`!

#### Installation

Just run `brew install jq` and you'll be sorted.

#### GitHub API

Recently, I needed to pull down a list of repositories from Gitlab (no, that's not a typo) for some particular task. Let's see how we could do that based on public GitHub API. The public API exposes `/users/:username/repos` endpoint. It can be called with `curl` like this:

    curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/tomaszj/repos

I'll store the response in a file for further activitites:

    curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/tomaszj/repos > github_response.json

#### jq

jq is a command-line tool which takes a "program" and the JSON as inputs. The program dictates what the output is going to be. The simplest program is `'.'`, which just outputs nicely formatted input.

```bash
< github_response.json | jq '.'
# or
< github_response.json | jq
```

![jq 1st example](/images/jq/jq1.png)

`.[]` allows to iterate over the elements of the array. Together with `-c` or `--compact-output`, we can count the number of repos the user has:

```bash
< github_response.json | jq '.[]' --compact-output | wc -l
```

![jq 2st example](/images/jq/jq2.png)

`.KEY` allows to extract a value from a JSON object. Note how I append the extraction to array operator. Let's say we're interested in names of repos:

```bash
< github_response.json | jq '.[].name'
```

![jq 3st example](/images/jq/jq3.png)

`{KEY}` allows us to continue working with entries as JSON object. Note how I use the pipe to join two filters together:

```bash
< github_response.json | jq -c '.[] | {name}'
```
![jq 4st example](/images/jq/jq4.png)

More complicated example shows names and SSH URLs joined together using a string, so the output can be easily copied to e.g. Excel:

```bash
< github_response.json | jq '.[] | {name, ssh_url} | join(",")'
# or with pbcopy for easy paste
< github_response.json | jq '.[] | {name, ssh_url} | join(",")' | pbcopy
```

![jq 5st example](/images/jq/jq5.png)

#### And much moar

`jq` offers a lot of functionality, just take a look at documentation (`man jq`) ant take your pick. The last thing I'm going to show is how to use `select` function for filtering the objects. This little monster selects name and (update) date of projects which were updated within past year:

```bash
REF_DATE=`date -v-12m -u +%Y-%m-%dT%H:%M:%SZ` && \
  < github_response.json | jq ".[] | select(.updated_at > \"$REF_DATE\") | {name, updated_at}"
```

![jq 6st example](/images/jq/jq6.png)

