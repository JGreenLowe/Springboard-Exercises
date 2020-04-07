## Patterns in Login Timestamps

The login timestamps appear to be of reasonably good data quality, except that all years are given from the start of the Unix epoch, i.e., 1970. Measuring the timestamps in seconds and converting them to datetime format does not resolve this issue.

As shown in the images below, there are two important patterns in the login data. First, there are spikes in the data at weekly intervals, presumably on Saturday nights. It is difficult to tell which day of the week features the spikes given the uncertainty about what year the data have been drawn from, but most rideshare companies have peak usage on Saturday nights. This shows up on the dates labelled as 1/1/1970, 1/8/1970, 1/15/1970, and so on.
![](https://github.com/JGreenLowe/Springboard-Exercises/blob/master/ultimate/weekly_usage_patterns.png)

Second, there is a rough approximation of a sine wave, or daily cycle, in which logins peak around midnight on each day, i.e., as each day changes into the next, and then decline toward a minimum around noon. There also appear to be secondary peaks within each sine wave, perhaps representing varying usage during, e.g., weekday rush hours, but these peaks are less clear and less stable.
![](https://github.com/JGreenLowe/Springboard-Exercises/blob/master/ultimate/daily_usage_patterns.png)

## Experiment and Metrics Design

Ultimate's city managers have proposed an experiment to encourage drivers to be available in both cities by reimbursing all toll costs across the bridge that connects the two cities. How will we know if this experiment was successful?

Taking the managers literally, the experiment would be successful to the extent that each driver's share of rides from Gotham is nearly identical to that driver's share of rides from Metropolis. If most drivers complete about 50% of their rides in Gotham, then we know that drivers are no longer exclusive to one city and the experiment has been a strong success. If drivers continue to disproportionately focus on completing rides in one city, we could measure the new proportions, compare them to the old proportions, and see if the change in proportions is likely to have arisen through chance alone. For example, if the proportion of rides from Gotham-based drivers was 90% with a standard error of 5% in any given week, and then the first week of the experiment, the share of rides in Gotham for Gotham-based drivers drops to only 75%, that is fairly convincing evidence that the experiment has achieved at least some of its intended effects.

However, we probably should not take the managers literally. The business case for making drivers available in all areas is probably based on a desire to avoid shortages and/or surge pricing. Becuase Gotham is most active at night and Metropolis is most active by day, we probably want to encourage some Gotham drivers to head over to Metropolis during the daytime, and to encourage some Metropolis drivers to head over to Gotham during the nighttime. There is no obvious business reason for wanting *each* driver to split his or her time equally between cities, and in fact such a split might be counterproductive in that it would eat up driver time and toll fees by incentivizing overly-frequent crossings of the bridge. Instead, we want to make sure that *enough* drivers will be willing to cross over to equalize supply and demand in each city. In other words, the goal of the experiment is most likely to encourage enough Gotham drivers to travel to Metropolis during the day so that Metropolis riders will be able to easily find a driver using normal pricing (and vice versa).

To determine whether the experiment is effective based on this second vision of the experiment's goals, I would look at the *avg_surge*, the average surge multiplier during the time before the experiment and the time during the experiment. If *avg_surge* is not available on a per-day basis, I would identify a cohort of users whose *last_trip_date* was prior to the start of the experiment (to serve as the control group) and then identify a second cohort of users whose *signup_date* was **after** the start of the experiment (to serve as the experimental group). If the average surge rate in the experimental group is less than the average surge rate in the control group, then the experiment has been a success -- especially if the margin of decrease is larger than twice the standard deviation of the surge rate from day to day in the control group. For example, if the surge rate varies between 1.3 and 1.9 in the control group, and the average surge rate in the experimental group is 1.35, that is reasonably strong evidence that the experiment was successful.

One important caveat is that there may be other factors that would affect the surge rate beyond just the experimental condition of free bridge tolls. If new users are somewhat more likely (or less likely) to accept surge pricing, or if surge pricing is more common during holiday weekends, and there is a holiday weekend during the experimental period but not the control period, then that would bias the results. It will be important to compare the users in each cohort of the study and make sure that they have roughly comparable demographics, or, that if they have different demographics, any differences are not associated with major differences in surge rates. Another way of validating the data is to run a multivariable regression using all available demographics, look at the Aikaike Information Criterion (AIC) for the complete model, and then look at the AIC for a simpler model that focuses on the experimental condition. If the AIC of the simpler model is less than or equal to the AIC of the complete model, that suggests that adding the additional demographics is not adding additional explanatory power, and that the experiment has successfully modelled the most important effect that was driving the change in surge prices.

My final recommendation to the city managers would depend on the expected profits from reducing surge pricing, the expected customer retention benefits from reducing surge pricing, the expected lost revenue from reducing surge pricing, and the expected costs of subsidizing bridge tolls. Presumably, surge pricing lowers Ultimate's profits by suppressing short-term demand for Ultimate's ride-sharing services. As prices rise, some consumers will decide that they would rather postpone their trips or use alternate methods of transportation; this leads to short-term lost revenue and lost profits. In addition, some consumers will presumably switch to other brands or make alternate long-term plans for their transportation if they are repeatedly disssatisfied with the frequency of surge pricing. Most customers will tolerate an occasional surge, but if prices always seem to be at surge prices whenever the customer wants to travel, then the customer may decide that Ultimate is not offering a good value. Therefore, I would anticipate that customer retention is negatively correlated with average surge pricing. 

On the other hand, reducing surge pricing could also reduce short-term profits, because Ultimate presumably earns a percentage commission on each transaction, so if the price per transaction goes down (because surges have been made unnecessary) then Ultimate's revenue per transaction will also go down. If the bridge subsidy experiment works, then Ultimate would be left with a larger volume of lower-priced transactions, which could actually cause a net loss in Ultimate's profits, especially if Ultimate has a degree of monopoly power in the ride-sharing industry. In addition, there is the cost of the bridge subsidy which must be paid -- unless the experiment generates more in new profits and retained customers than it costs in bridge tolls, then the new policy is not financially viable. Therefore, I would recommend making the subsidy permanent if and only if the expected profits from increased transactions and increased customer retention are likely to outweigh the expected lost revenue from reducing pricing and the expected costs of paying for bridge tolls.

## Predictive Modeling

Blah.

                           Logit Regression Results                           
==============================================================================
Dep. Variable:                      y   No. Observations:                40000
Model:                          Logit   Df Residuals:                    39986
Method:                           MLE   Df Model:                           13
Date:                Wed, 01 Apr 2020   Pseudo R-squ.:                  0.8642
Time:                        22:04:54   Log-Likelihood:                -3572.1
converged:                       True   LL-Null:                       -26302.
Covariance Type:            nonrobust   LLR p-value:                     0.000
=======================================================================================
                          coef    std err          z      P>|z|      [0.025      0.975]
---------------------------------------------------------------------------------------
early_trips             0.0615      0.009      6.738      0.000       0.044       0.079
signup              -2.508e-06   4.19e-08    -59.832      0.000   -2.59e-06   -2.43e-06
driver_rtg             -0.0328      0.055     -0.594      0.553      -0.141       0.075
surge                  -0.1412      0.320     -0.441      0.659      -0.769       0.487
last_trip            2.487e-06   4.16e-08     59.841      0.000    2.41e-06    2.57e-06
surge_pct               0.0017      0.003      0.562      0.574      -0.004       0.008
weekday                 0.0061      0.001      6.433      0.000       0.004       0.008
dist                   -0.0022      0.006     -0.375      0.708      -0.014       0.009
user_rtg               -0.2126      0.081     -2.610      0.009      -0.372      -0.053
city_King's Landing     1.9498      0.090     21.752      0.000       1.774       2.125
city_Winterfell         0.5182      0.071      7.309      0.000       0.379       0.657
phone_Android          -1.1672      0.437     -2.672      0.008      -2.023      -0.311
phone_iPhone           -0.0595      0.434     -0.137      0.891      -0.910       0.791
black_True              0.9089      0.065     14.004      0.000       0.782       1.036
=======================================================================================
