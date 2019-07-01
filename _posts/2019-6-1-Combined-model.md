---
layout: post
title: Part 3. Generating local wind expectancy statistics from a reference windrose and a regional CFD model
tags: [statistical modeling, CFD, wind]
categories: [regional wind model]
image: 
excerpt_separator: <!--more-->

---

## Summary

This post describes the combination of a statiscal model of the wind distribution at reference site with a regional model of terrain effects to generate wind expectancy statistics for any site.

## Formulation of the model

$$
\begin{align*}
  & W(x,y) = \sum_{i=N}^S\left(v(c_i,loc_i,scale_i)f_i\frac{v_i(x,y)}{v_i(x0,y0)},\Theta(i-1,i+1)+\theta(x,y)-\theta(x0,y0)\right)
\end{align*}
$$
