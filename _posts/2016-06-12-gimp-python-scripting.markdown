---
layout: post
title:  "Generate name cards in GIMP using Python"
date:   2016-07-16
categories: graphics utilities
---

Creating custom name cards can be a major pain in the neck. I faced this challenge again, when I was about to prepare them for my very own wedding. I made this process much easier for myself thanks to Python-Fu scripting available in GIMP.

#### Pre-requisites

It helps to have GIMP installed (grab it 'ere: [www.gimp.org](https://www.gimp.org/)).

#### Drawing the template

First, I created a printing-grade image in GIMP (at least 300 ppi). I've decided to use dimensions which would be around one-third of A4 format (roughly 6 cm x 8 cm unfolded), so I set the canvas size to 709 x 945 px.

![Screen showing new image setup with details above](/images/gimp/gimp_python_new_image.png)

Then I create all the elements of the image and add a text layer - I explicitly name the layer for later reference.

![Screen showing a sample name card](/images/gimp/gimp_python_card_prototype.png)

#### Scripting

Here comes the fun part. You can script most of GIMP's actions, what is a fab feature. The downside is that most functions are stored as simple procedures in PDB (Procedure Database I'm guessing) and the code looks a bit tedious. Anyway, let's see what we've got available. Let's start with finding where to run the scripts:

![Screen showing where Python-Fu console can be found in GIMP](/images/gimp/python_fu_location.png)

Now, we can start typing Python!

```python
gimp.image_list()                       # Gets currently open images in GIMP.
original_image = gimp.image_list()[0]   # Gets the currently open image if we're working one one.

image = pdb.gimp_image_duplicate(original_image) # Gives a copy of the image to work with
```

To work on a layer, we need to get it first:

```python
text_layer = pdb.gimp_image_get_layer_by_name(image, "Your text layer name")
```

Now we can use classic operations, such as setting text, font, etc. One annoying bit is that setting a new text, for some reason changes the font to default. Hence the consecutive font style settings:

```python
pdb.gimp_text_layer_set_text(text_layer, "Here goes the text!")
pdb.gimp_text_layer_set_font_size(text_layer, 72, 0)
pdb.gimp_text_layer_set_font(text_layer, "Helvetica Neue Light")
```

Now, to properly save the file as, say, PNG, we need to merge everything into one visible layer.

```python
merged_layer = pdb.gimp_image_merge_visible_layers(image, 1)
```

Export to PDF has many different options, but if the default are sensible enough for you (they were for me), then we can use a special function to do so:

```python
# $OUTPUT_FOLDER_PATH could be /Users/johnsmith/outputfolder/
pdb.file_png_save_defaults(image, merged_layer, "$OUTPUT_FOLDER_PATH" + filename, filename)
```

Finally, we need to cleanup the image we were working on:

```python
pdb.gimp_image_delete(image)
```

#### Printing fun

On Mac, I just opened all the images in Preview and used standard printing to print everything on one sheet. Feel free to use anything that works for you, however.

#### Putting this all together

Here's a gist that contains whole script together with sample names and added filename handling, feel free to use it: [https://gist.github.com/tomaszj/fe25e368d860fb399987a697c70369e4](https://gist.github.com/tomaszj/fe25e368d860fb399987a697c70369e4)

Here's a GIMP file I produced: [Link to the GIMP file prepared in the article](/images/gimp/party_parrot_name_card.xcf)

Here's the Python-Fu window with the script already pasted in:

![Screen showing GIMP with already pasted script](/images/gimp/pasted_script.png)

And here's the result:

![Screen showing GIMP with already pasted script](/images/gimp/generated_files.png)

#### Further info

PDB contains a lot of utility functions - whole list can be found in GIMP:

![Screen showing where PDB can be found in GIMP](/images/gimp/pdb_location.png)

