---
title: Converting Images in macOS using sips 
description: Overview of Content Delivery Networks, or CDNs.
date: 2025-03-22 21:19:00 -0700 # date affects if it shows up or not
authors: [jsantos]
image: "/personal-blog/images/macos-sips-command.jpg"
tags: [images, macos, photos, terminal]
tags_color: '#b77be3'
featured: false
toc: true
---

## **The macOS `sips` Command: A Handy Tool You Didn’t Know You Needed**

### **Introduction to the `sips` Command**

Ever stumbled across a folder of massive images that need resizing, or wanted to convert a batch of HEICs or PNGs into JPEGs without firing up heavy software? If you're on macOS, there’s a lightweight hero ready to help—meet `sips`.


`sips`, short for **Scriptable Image Processing System**, is a built-in macOS command-line utility designed to make simple image manipulation tasks a breeze. Whether you’re resizing, converting, or tweaking images, `sips` delivers no-nonsense results right from the terminal.

### **Basic Usage of `sips`**

#### **Viewing Image Metadata**

Before making changes, it’s often handy to know what you’re working with. The `sips` command lets you check an image's metadata without breaking a sweat. Just run:

```bash

sips -g all myimage.jpg

```

This spits out details like pixel height, width, DPI, color model, and more. Think of it as a quick snapshot of your file’s vital signs.

#### **Converting Image Formats**

Need to switch a file format? `sips` makes it ridiculously simple. Here's how you can convert a PNG to a JPEG:

```bash
sips -s format jpeg myimage.png --out myimage_converted.jpg
```

No need for Photoshop or Preview. 

*NOTE: This is the command I use to convert my iPhone's HEIC images to JPEG.*

#### **Resizing Images**

For those moments when you need your image to shrink (or grow), `sips` can resize by pixel dimensions:

```bash
sips -z 600 800 myimage.jpg --out small_image.jpg
```

The example above resizes the image to 600 x 800 pixels and outputs the result to a file. Big image files can slow down your site, but quickly shrinking them to a web-friendly size helps your pages load faster. It’s simple, fast, and you don’t need to mess with any fancy apps.

### **Advanced `sips` Operations**

#### **Batch Processing Images**

What if you’ve got an entire folder of images that need resizing? Pair `sips` with a simple loop in your terminal:

```bash
for img in *.jpg; do
  sips -z 400 600 "$img" --out "resized_$img"
done
```
In seconds, you’ve got a batch of resized images, each neatly prefixed.

#### **Rotating and Flipping Images**
Flipping an image vertically (because why not?) is as easy as:

```bash
sips -f v myimage.jpg
```

Rotate an image 90 degrees clockwise? Yep, you can do that too:

```bash
sips -r 90 myimage.jpg
```

#### **Converting a Folder of PNGs to JPEGs**

Save bandwidth by converting PNGs to JPEGs in bulk:

```bash
for img in *.png; do
  sips -s format jpeg "$img" --out "${img%.*}.jpg"
done
```

### **Conclusion**

For macOS users, `sips` is a hidden gem. It may not replace Photoshop or Affinity Photo, but it sure makes quick and dirty image manipulation easy without leaving the terminal. Whether you're a developer, blogger, or just someone looking to resize some vacation photos, `sips` is a great option to have in your toolbelt.

##### Hero Image Prompt

*Generate a photo of an aesthetic snowy outdoors scene showcasing a beautiful background and an individual taking a photo of that scene. Make the resolution big enough for a blog post hero image.*