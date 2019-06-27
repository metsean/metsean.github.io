---
layout: post
title: Building a statistical model of wind speeds and direction
---

The reference site used for this analysis is the ‘Wellington Aero’ Meteorological station at -41.322 174.804. The terrain at and around this location is flat and there are no hill obstructions for the dominant wind directions (north and south). This makes the site well suited as a reference for this analysis as the local wind effects will be minimal. There is also a long observation record for this site which is publicly accessible through the CliFlo portal.

The hourly averaged surface wind record was obtained using the for the period of June 2000 - January 2013. This long record was assumed to be generally representative of the sites climate.

This entire observational record could be used as input reference data and scaled by the regional wind model to give the site specific estimate. However it is a large dataset and it is beneficial to represent the reference data by a statistical model instead. Wind speed distributions are typically represented with a Weibull distribution. 

The wind record was divided into 16 different wind direction bins (N, NNE, NE, …) and then a Weilbull fit was performed for each of these using the scipy.stats.weibull_min function. This returns a wind speed probability distribution function v(c,loc,scale). The results of this are shown in the plot below where the bars represent the observational data and the solid line shows the distribution fit. The occurrence of each wind direction f is given on the plot as a percentage.

![distribution of wind speeds]({{ site.baseurl }}/images/station3445_fig1.png "distribution of wind speeds")

By eye, the Weibull distribution does a good job of describing the data. The obvious exception is the SSW wind speed distribution which is almost bi-modal - however, even in this case the wind speeds are well described at the upper and lower limits by the Weibull fit. This wind directions has only a 7% occurrence and so this error is tolerable

The statistical model used to generate wind speed and direction data is:
$$
\begin{align*}
  & W = \sum_{i=N}^S\left(v(c_i,loc_i,scale_i)f_iX(i-1,i+1)\right)
\end{align*}
$$

W =i=Ndirections   (v(ci,loci,scalei)f(i),random(i-1,i+1)),
where random(i-1,i+1) represents random directions generated in each wind direction bin. E.g. for the northerly bin, the direction angles are randomly distributed between -11.25 and 11.25 degrees bearing.

Computationally this summation looks as follows:
```python
for a,w in enumerate(dirn):
        # get distribution parameters
        dists= windModel[w] 
        (c, loc, scale) = dists[0:3]
        occurrence=dists[3]
        # generate windspeeds
        vel=weibull_min.rvs(c, loc, scale, size=int(occurrence*8760))
        # gernate wind directions (random in angle bin)
        theta=angbins[a]+(angbins[a+1]-angbins[a])*np.random.random(size=int(dists[3]*8760))
        
        #wrap around correction for angles near N
        theta[theta>360]-=360
        theta[theta<angbins[0]]+=360
        W=np.vstack((W,(np.vstack((theta,vel)).T)))
```
This function generates 1 years worth of wind data (8760 hours).

The quality of the statistical model can be well judged by comparing the observational ‘raw’ wind record against the model. This is shown below. The distribution of velocities and directions is very similar for the two plots. The scaling for the raw data is different because there is a 16 year record whereas the model is normalised for 1 year. The statistical model looks like a very good approximation.

![wind rose]({{ site.baseurl }}/images/station3445-Windrose.png "wind rose")
![model wind rose]({{ site.baseurl }}/images/station3445-modelWindrose.png "model wind rose")


A further comparison can be made by ignoring the direction data and comparing only the distribution of wind speeds. This is shown below for the two cases.  

![wind speed histogram]({{ site.baseurl }}/images/station3445-Windrose.png "wind speed histogram")
![model wind speed histogram]({{ site.baseurl }}/images/station3445-modelWindrose.png "model wind speed histogram")

