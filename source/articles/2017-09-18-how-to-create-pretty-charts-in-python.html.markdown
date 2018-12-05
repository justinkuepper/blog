---

title: How to Create Pretty Charts in Python
date: 2017-09-18 22:14 UTC
tags:

---

There's no doubt that python is a great language for analyzing data, but it can be a challenge to translate that data into understandable formats. As a financial writer (as well as a software developer), I have a deep appreciation for charts that can quickly tell a story about an underlying metric, such as showing revenue trends or highlighting where revenue is coming from in a firm. The good news is that Python has an easy way to generate charts that can be used in articles.

In this post, we will look at how to create a simple chart in Python that's both visually appealing and branded with your company's information.

# Setting the Stage

The first step is installing Python and some standard libraries that we'll be using to modify the data and generate a chart. Assuming that you have Python already installed, you'll need the pandas and matplotlib libraries installed to generate these charts.

You can install these libraries using pip:

```pip install pandas
pip install matplotlib
```

These are the only two libraries that we'll use in this tutorial, but there are many other libraries that can be used to generate different types of charts. For example, you might want to consider more advanced charting libraries to generate unique charts like those found in D3.

Next, we need to clean and import that data that we'll need to generate the chart. A key benefit of using Python is that you can conduct fairly complex data manipulation without much effort compared to using Microsoft Excel or more hands-on applications to generate charts.

In this case, we'll just use a simple CSV data set from the American Pet Products Association that simply plots American spending on pet products over time.

```Year,Dollars
2017,69.36
2016,66.75
2015,60.28
2014,58.04
2013,55.72
2012,53.33
2011,50.96
2010,48.35
2009,45.53
2008,43.2
2007,41.2
2006,38.5
2005,36.3
2004,34.4
2003,32.4
2002,29.6
2001,28.5
```

If you're following the tutorial on your machine, simply copy and paste these values into a file and save it with a CSV extension.

# Setting up the Chart

We will start by creating a new Python file called appa.py that will contain the code necessary to generate the chart.

First, let's import the pandas and matplotlib libraries that we need and namespace parts of the library for easy access within our application:

```import pandas
import matplotlib.pyplot as plt
import matplotlib.style as style
```

We can then define the base style that we'll use for the chart:

```style.use('fivethirtyeight')
```

Note that there are many different built-in matplotlib styles that you can use and several more that you can import. You can also create your own style to easily reproduce charts with the same appearance. In this example, we will use the built-in fivethirtyeight style.

Second, let's import the CSV file we've created and define the columns and chart size:

```data = pd.read_csv('data.csv')
graph = data.plot(x = 'Year', y = 'Dollars', figsize = (12,8))
```

At this point, we would be able to generate a basic chart, but let's add some custom styling to make things a little more interesting and brandable.

# Styling the Chart

Matplotlib makes it easy to add style to the chart. In this case, we will hiding the X-axis label, add some description text, and brand the chart with your company's information. But, there's a lot more you can do that's outlined in the library's documentation.

Here's what the styling might look like in our case:

```graph.xaxis.label.set_visible(False)
graph.axhline(y = 0, color = 'black', linewidth = 1.3, alpha = .7)
graph.text(x = 2000, y = 79, s = "U.S. Pet Spending", fontsize = 26, weight = 'bold', alpha = .75)
graph.text(x = 2000, y = 75, s = "American spending on pet products and services in billions of dollars", fontsize = 16, alpha = .85)
graph.text(x = 2000, y = -9, s = "(C) Your Company Inc.                                                                                    Source: American Pet Products Association", fontsize = 14, color = '#f0f0f0', backgroundcolor = 'grey')
```

Note that the x and y coordinates for the text location are based on the actual coordinates of the chart. Since the x-axis represents the year, we are using 2000 as a starting point, for example. Also, note the space between the company name and the source of the data, which you may need to adjust.

# Displaying the Chart

The final step is displaying the chart.

We will add the following code to show the chart in the Python file:

```plt.gca().invert_xaxis()
plt.show()
```

Then, we can view the chart by simply running the file in the command line:

```python appa.py
```

# What's Next?

This brief article just scratches the surface of what's possible when analyzing and generating charts in Python. Try experimenting with different chart types from the documentation and see how it works for you. I have created custom styles for clients that they can easily re-use between charts, which makes the process of branding important data images extremely simple.
