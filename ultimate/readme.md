## Patterns in Login Timestamps

The login timestamps appear to be of reasonably good data quality, except that all years are given from the start of the Unix epoch, i.e., 1970. Measuring the timestamps in seconds and converting them to datetime format does not resolve this issue.

As shown in the images below, there are two important patterns in the login data. First, there are spikes in the data at weekly intervals, presumably on Saturday nights. It is difficult to tell which day of the week features the spikes given the uncertainty about what year the data have been drawn from, but most rideshare companies have peak usage on Saturday nights. This shows up on the dates labelled as 1/1/1970, 1/8/1970, 1/15/1970, and so on.
![](https://github.com/JGreenLowe/Springboard-Exercises/ultimate/weekly_usage_patterns.png)

Second, there is a rough approximation of a sine wave, or daily cycle, in which logins peak around midnight on each day, i.e., as each day changes into the next, and then decline toward a minimum around noon. There also appear to be secondary peaks within each sine wave, perhaps representing varying usage during, e.g., weekday rush hours, but these peaks are less clear and less stable.
![](https://github.com/JGreenLowe/Springboard-Exercises/ultimate/daily_usage_patterns.png)
