---
Created: 2025-01-30T17:25
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# [[Quantiles]]

![[Tags/image.png|image.png]]

  

![[Tags/image 1.png|image 1.png]]

  

# [[Box Plots]]

- Imagine the data is as below along with the quantiles.

![[Tags/image 2.png|image 2.png]]

- The box plot will be as follows. Note that there are no outliers here.

![[Tags/image 3.png|image 3.png]]

- Another example with outliers

![[Tags/image 4.png|image 4.png]]

  

## ==Things to remember:==

1. IQR = Q3 - Q1
2. The whiskers are min, max of Q +- 1.5 * IQR. They don’t extend to the most extreme data point.
3. Anything outside whiskers is outlier.

# Kernel Density function

- Imagine a histogram.

![[Tags/image 5.png|image 5.png]]

- **The goal is to convert this to a PDF plot.** ==**The way we do this using a [[KDE]].**==
- Step 1 is plotting each point along the x axis

![[Tags/image 6.png|image 6.png]]

- Step 2 is take a normal distribution around each point. ==**This is called the [[kernel]].**==

![[Tags/image 7.png|image 7.png]]

- Step three is to multiply everything by 1/n. Notice that since the area under each curve is one, the average of the curves must give us an area under the curve of one. This is what estimation looks like after cleaning up the plot a bit.

![[Tags/image 8.png|image 8.png]]

- Doesn't look so great, but that's because you've try to approximate a density from just 12 data points. But if you try from many points, then you're actually going to get a really nice smooth function that actually resembles the PDF well. This is a way to approximate the PDF based on the data.

![[Tags/image 9.png|image 9.png]]

  

  

# Violin Plots

- Violin plots are mix of KDE and Box plot.

![[Tags/image 10.png|image 10.png]]

  

# [[QQ Plot]]

- Linear and log regression along with some other frameworks expect data normality. While quickly looking at histograms is one way to check normality, another is QQ plot.
- Consider an example below of how to do it. The data here is not normally distributed.

![[Tags/image 11.png|image 11.png]]