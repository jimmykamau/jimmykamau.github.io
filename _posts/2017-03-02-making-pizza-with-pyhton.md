---
layout: post
title: "Making pizza with Python"
description: "Using NumPy to format data for Google Hash Code's pizza problem."
tags: [python]
---

There comes a time in a person's life that they have to face the knife: cut a tomato-and-mushroom pizza into slices with just the right amount of ingredients on each slice. This can be a really daunting task, especially if Chef Gusteau was having a bad day and just threw the ingredients onto the dough. Worry not though, technology is here to save the day!

The good peeps at Google decided to turn this scenario into a practice problem for Hash Code 2017. In their own words:

> The pizza is represented as a rectangular, 2-dimensional grid of R rows and C columns. The cells within the grid are referenced using a pair of 0-based coordinates [r, c] , denoting respectively the row and the column of the cell.

They went on to explain that each cell of the pizza contains either:
* mushroom, represented in the input file as M ; or
* represented in the input file as T

The input was provided as a data file - a plain text file containing exclusively ASCII characters with lines terminated with a single ‘\n’ character at the end of each line (UNIX- style line endings). The file consisted of:

1. one line containing the following natural numbers separated by single spaces:
* R (1 ≤ R ≤ 1000) i s t he n umber o f r ows,
* C (1 ≤ C ≤ 1000) i s t he n umber o f c olumns,
* L (1 ≤ L ≤ 1000) i s t he m inimum n umber o f e ach i ngredient cells i n a slice,
* H (1 ≤ H ≤ 1000) i s t he m aximum t otal n umber o f c ells o f a slice
2. **R** lines describing the rows of the pizza (one after another). Each of these lines contains **C** characters describing the ingredients in the cells of the row (one cell after another). Each character is either ‘M’ (for mushroom) or ‘T’ (for tomato).

An example file (small.in) looked like this:
{% highlight text %}
6 7 1 5
TMMMTTT
MMMMTMM
TTMTTMT
TMMTMMM
TTTTTTM
TTTTTTM
{% endhighlight %}

The first line gives us information about the pizza:
1. The pizza has 6 rows and 7 columns
2. There should be a minimum of 1 of each ingredient in each slice
3. There should be a maximum of 5 cells (ingredients) per slice

Getting a mental image of the pizza from the file above isn't very easy, so we have to find a way to reformat it into something more understandable. I decided to make use of NumPy for this.

If you haven't already, install NumPy as outlined in their [official documentation](http://www.numpy.org/). Let's get crackn'..

I created a `pizza.py` file to hold the Pizza class with all the functionality:

{% highlight python %}
import numpy as np


class Pizza(object):
    def __init__(self, file_path):
        self.file_path = file_path
        self.number_of_rows = 0
        self.number_of_columns = 0
        self.min_ingredients_per_slice = 0
        self.max_cells_per_slice = 0
        self.ingredients = []
        self.data_array = [[]]
        self.parse_data()

    def parse_data(self):
        with open(self.file_path, "r") as pizza_values:
            first_line = pizza_values.readline().split()
            data = pizza_values.readlines()
        self.number_of_rows = int(first_line[0])
        self.number_of_columns = int(first_line[1])
        self.min_ingredients_per_slice = int(first_line[2])
        self.max_cells_per_slice = int(first_line[3])
        self.ingredients = [item.replace("\n", "") for item in data]
        self.data_array = np.array(
            self.ingredients, dtype=str).view('S1').reshape(
                (self.number_of_rows, self.number_of_columns))
{% endhighlight %}

The meat of the application is in the `parse_data` method. The method first opens the file provided in `read` mode and assigns the first line of the file to the `first_line` variable. It then reads the rest of the data and stores it in the `data` variable. The method then assigns the data from the first line to various variables.
If we print the `data` variable after passing a file with the example data above, we see that it contains a list of the ingredients:
`['TMMMTTT\n', 'MMMMTMM\n', 'TTMTTMT\n', 'TMMTMMM\n', 'TTTTTTM\n', 'TTTTTTM\n']`
The line `self.ingredients = [item.replace("\n", "") for item in data]` is a short way of writing:

{% highlight python %}
for item in data:
    item.replace("\n", "")
    self.ingredients.append(item)
{% endhighlight %}

This line iterates through `data` and strips out every newline (\n) character from the individual items. It then appends each item to the `self.ingredients` list.

We then create a matrix of ingredients by using the power of NumPy.
{% highlight python %}
self.data_array = np.array(
        self.ingredients, dtype=str).view('S1').reshape(
            (self.number_of_rows, self.number_of_columns))
{% endhighlight %}

We first tell NumPy to create a matrix of strings from the `self.ingredients` list: `np.array(self.ingredients, dtype=str)
`view(S1)` ensures that each cell in the matrix contains one character. `reshape(self.number_of_rows, self.number_of_columns)` ensures the matrix created is the shape specified by the data received.

We can create a `pizza_problem.py` file that will create an instance of the Pizza class and supply it with data:

{% highlight python %}
from pizza import Pizza

pizza = Pizza("small.in")
print(pizza.data_array)
{% endhighlight %}

Running this file, `python pizza_problem.py` gives the following output:
{% highlight text %}
[['T' 'M' 'M' 'M' 'T' 'T' 'T']
 ['M' 'M' 'M' 'M' 'T' 'M' 'M']
 ['T' 'T' 'M' 'T' 'T' 'M' 'T']
 ['T' 'M' 'M' 'T' 'M' 'M' 'M']
 ['T' 'T' 'T' 'T' 'T' 'T' 'M']
 ['T' 'T' 'T' 'T' 'T' 'T' 'M']]
{% endhighlight %}

A :pizza: matrix, :yum:. Part two of this post will cover solving the slice problem with the formatted data.
