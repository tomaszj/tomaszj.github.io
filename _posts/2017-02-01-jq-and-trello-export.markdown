---
layout: post
title:  "jq and Trello export"
date:   2017-02-01
categories: command-line utilities
---

In a previous [post](/command-line/utilities/2016/03/04/jq-and-github-api.html) I've presented how to use `jq` - a command-line tool for JSON processing.

#### Trello export

From time to time, I have a need to grab some Trello cards and put them in a spreadsheet for analysis/graphs. Using the free version, Trello only allows you to export the board to a JSON file (more Excel-friendly CSV is available in business tier). Let's see what we can do with the JSON, then. Follow these steps to do that:

![first step](/images/jq_trello/step 1 - more.png)

![second step](/images/jq_trello/step 2 - print and export.png)

![third step](/images/jq_trello/step 3 - json export.png)

Finally, you'll see some JSON text in your browser (at least that's the behaviour in Google Chrome) - save it as a file (e.g. as trello_export.json) using Cmd/Ctrl+S.

#### jq and Trello

The export from Trello is just a one, huge object containing all the data related to the board. Perhaps the most important are the cards - all available under the "cards" key.

I'll use some basic `jq` in raw mode (`-r`) to get clean output, ready for copy/paste. This will allow me to get the names of all the cards on the board (remember to change the name of the file to whatever you saved your export as):

```bash
jq ".cards[] | .name" trello_export.json
```

![1st example](/images/jq_trello/example 1.png)

Then I realised there are some archived cards in the export, so I got rid of them:

```bash
jq  ".cards[] | select(.closed == false) | .name " trello_export.json
```

![2nd example](/images/jq_trello/example 2.png)

In another case, I needed to filter cards based on a string in the title:

```bash
jq ".cards[] | select(.name | contains(\"$MY_TAG\")) | .name " trello_export.json
```

On another occasion, I wanted to get all cards which had a particular label:

```bash
jq  ".cards[] | select(.labels[].color != \"green\") | .name " trello_export.json
```

And finally, an example with names of all visible cards with concatenated IDs of Trello users assigned to them. These can be cross-referenced with what "members" key contains.

```bash
jq -r ".cards[] | select(.closed == false) | \"\(.name), \(.idMembers | join(\"|\"))\"" trello_export.json
```

