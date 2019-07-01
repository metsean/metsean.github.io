---
layout: post
title: Part 3. Generating local wind expectancy statistics from a reference windrose and a regional CFD model
tags: [statistical modeling, CFD, wind]
categories: [regional wind model]
image: sim-41.3293_174.7262.png
excerpt_separator: <!--more-->

---

## Summary

This post describes the combination of a statiscal model of the wind distribution at reference site with a regional model of terrain effects to generate wind expectancy statistics for any site. 

## Formulation of the model

The wind distribution at location coordinates _x,y_ is given by:
$$
\begin{align*}
  & W(x,y) = \sum_{i=N}^S\left(v(c_i,loc_i,scale_i)f_i\frac{v_i(x,y)}{v_i(x_0,y_0)},\Theta(i-1,i+1)+\theta(x,y)-\theta(x_0,y_0)\right)
\end{align*}
$$
where $$x_0$$ and $$y_0$$ are the coordinates of the reference site.

This might look complicated but it is only a small jump from the statistical model presented in post 1. Here, for each wind direction the velocity distribution from the reference site is weighted by the occurance of that wind direction $$f_i$$ and then scaled by $$frac{v_i(x,y)}{v_i(x_0,y_0)}$$ - the ratio of velocites of the 2 sites. The wind direction is the second part of the equation - now the distribution of the wind angles for the wind direction bin $$Theta(i-1,i+1)$$ is adjusted by $$\theta(x,y)-\theta(x_0,y_0)$$ to give a local correction for the site.