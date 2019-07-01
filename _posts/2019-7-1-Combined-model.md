---
layout: post
title: Part 3. Generating local wind expectancy statistics from a reference windrose and a regional CFD model
tags: [statistical modeling, CFD, wind]
categories: [regional wind model]
image: sim-41.3293_174.7262.png
excerpt_separator: <!--more-->

---

## Summary

This post describes the combination of a statiscal model of the wind distribution at reference site with a regional model of terrain effects to generate wind expectancy statistics for any site. <!--more-->

## Formulation of the model

The wind distribution at location coordinates _x,y_ is given by:
$$
\begin{align*}
  & W(x,y) = \sum_{i=N}^S\left(v(c_i,loc_i,scale_i)f_i\frac{v_i(x,y)}{v_i(x_0,y_0)},\Theta(i-1,i+1)+\theta(x,y)-\theta(x_0,y_0)\right)
\end{align*}
$$
where $$x_0$$ and $$y_0$$ are the coordinates of the reference site.

This might look complicated but it is only a small jump from the statistical model presented in post 1. Here, for each wind direction the velocity distribution from the reference site is weighted by the occurance of that wind direction $$f_i$$ and then scaled by $$v_i(x,y)/v_i(x_0,y_0)$$ - the ratio of velocites of the 2 sites. The wind direction is the second part of the equation - now the distribution of the wind angles for the wind direction bin $$\Theta(i-1,i+1)$$ is adjusted by $$\theta(x,y)-\theta(x_0,y_0)$$ to give a local correction for the site.

## Testing the model

Let's try out the combined model on some sites around Wellington. Presented here are 

| Site 1 : Wellington CBD |
|![report]({{ site.baseurl }}/images/sim-41.2935_174.7761.png "CBD wind estimate")|
| Site 2: Brooklyn wind turbine site |
|![report]({{ site.baseurl }}/images/sim-41.3111_174.7448.png "wind turbine estimate")|
| Site 3: Hawkins hill |
|![report]({{ site.baseurl }}/images/sim-41.3293_174.7262.png "wind turbine estimate")|

