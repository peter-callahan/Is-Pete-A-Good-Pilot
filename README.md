Is Peter a Good Pilot?

Measuring Flight Performance with GPS Coordinates

Peter Callahan
School of Information
University of Michigan
Ann Arbor, MI
pcallaha@umich.edu

Tammy Nguyen
School of Information
University of Michigan
Ann Arbor, MI
nguyents@umich.edu

ABSTRACT

> Using data collected from a personal handheld Garmin GPS device during
> Peter Callahan’s numerous helicopter flights in the winter of 2014, we
> mined for insights surrounding flight profiles with the goal of
> creating some measure for evaluating in-flight performance. After
> classifying which GPS observations were recorded while the aircraft
> was engaged in flight activity, we performed two parameter expansions:
> one to determine altitude above ground by incorporating elevation data
> (NASA) and the other to calculate airspeed (historical NOAA weather
> observations). Using the height-velocity diagram’s threshold to
> evaluate Peter’s flight, we found that his average percentage of risk
> exposure inside was 5.89% and that the biggest features that
> contribute to risky flying are altitude, airspeed, and in-flight
> visibility.

1 Introduction

Measuring the performance of any action in an uncontrolled environment
adds layers of complexity, but raw data is messy and requires a
significant amount of cleaning and processing. During the winter of
2014, Peter recorded helicopter flight data using a personal Garmin GPS
receiver.

Using this data, we performed an analysis of Peter’s flights and create
a measure for evaluating in-flight performance through comparison with
the height-velocity diagram or H/V curve. This kind of analysis is not
usually performed outside of aircraft mishaps or accidents, so we have
developed an alternative method to evaluate in-flight performance using
off-the-shelf technology, a Garmin GPS and Python.

**2 Problem Definition and Data**

According to the Federal Aviation Administration (FAA) of the United
States, the height-velocity dia­gram or H/V curve (Figure 1) is a graph
that represents the combination of airspeeds and altitudes (above ground
level) that a helicopter can occupy at any given time. A safe flight
profile is one where a pilot has a chance to respond in the case of a
cataclysmic failure of the helicopter’s power system. A helicopter could
lose all power for a variety of reasons and if the pilot makes the
proper con­trol input immediately—1 to 2 seconds in the most severe
emergency—they have a reasonable chance of surviving the emergency
landing assuming a suitable landing site can be reached.

An unsafe flight profile is one where a pilot has little time to respond
to a loss of power in a “dead man’s curve” and is unlikely to perform
the correct control inputs fast enough to keep control of the
helicopter. In this case, a complete loss of control will most likely
result in a crash. This type of accident can be fatal, therefore, as a
way of mitigating risk during a flight, pilots must minimize the time
they spend in the unsafe flight areas of the height-velocity diagram.

Our data is a time series where the observations occur sequentially.
With approximately 136,000 lines of observations, each row represents a
single second of observation. Our data includes features such as
latitudinal and longitudinal values that are accurate down to 6 decimal
points, speed in knots (nautical miles per hour), direction (degrees
magnetic), temperature degrees Celsius, altitude above mean sea level
(feet), and time. The time data records both date and time of day and is
representative of local time, which is determined by the latitude and
longitude value and computed by the GPS automatically.

Unfortunately, the data has no indication of when one flight event stops
and another begins. Additionally, GPS data spikes periodically in ways
that are impossible (i.e. a 20MPH jump or a 200ft altitude change from
one second to the next), therefore those data points must be dealt with.
Lastly, much of the data does not reflect the actual figures necessary
to conduct the analysis and must be modified to provide useful insights.
A conversion of altitudes from meters above sea level to feet above
ground level is necessary, as is an adjustment for the impact of wind
based on the nearest observable weather data.

<img src="https://github.com/peter-callahan/flight-project/blob/main/doc_images/image1.png" style="width:3.33333in;height:4.15694in" />

Figure 1: Height-velocity diagram of a AH-1W helicopter which displays
the unsafe region on the left in red as a result of insufficient
airspeed and altitude above ground level. The area in green displays the
region where an aircraft is safe to operate \[NATOPS Flight Manual, Navy
model: AH-1W Helicopter, 2010\].

**3 Related Work**

3.1 Predicting Aircraft Pilot-Training Success: A Meta-Analysis of
Published Research

David R. Hunter and Eugene F. Burke’s analysis \[3\] focuses heavily on
human attributes related to flying such as reaction time or special
awareness. Their research seeks to evaluate the importance of these
attributes to flight performance based on previous human testing.
Interestingly, the analysis is based on metrics like ‘reaction time’ and
‘spatial awareness’ and takes a different approach from the method
proposed by us, which focuses on observations of suboptimal flight
parameters.

3.2 Sustaining Helicopter Pilot Performance with Dexedrine During
Periods of Sleep Deprivation

John A. Caldwell, J. Lynn Caldwell, John S. Crowley, and Heber D. Jones
assesses the performance of pilots in simulators after long periods of
wakefulness and after administering Dexedrine. The researchers test the
ability of simulator operators to maintain flight parameters and use
that as their metric for evaluating performance. They are testing in a
controlled simulator environment, while our proposal takes data from
operational missions and proposes to mine it for insight. Their approach
has some similarity to ours in the sense they seek to measure actual
flight performance through inflight parameters. The main difference is
that they are testing the effects of sleep deprivation on performance,
while we propose a new metric that could be used to evaluate
performance.

**4 Methodology**

4.1 Flight Events

GPS observations were recorded between 1-2 seconds and in addition to
flight time, included observations when the aircraft was idle on the
ground, taxiing, taking off, and landing. To determine when the aircraft
was in flight, we used a rolling average to determine when the aircraft
speed was greater than 20 knots, indicating the transition from normal
taxi speed to actual flight. The rolling average was necessary to
provide a more accurate picture of a transition to flight, else small
jumps in GPS speed would erroneously indicate a transition to flight.

4.2 Parameter Expansion: Above Ground Level

Altitude above sea level (ASL) is the distance of the aircraft from the
mean sea level (MSL). Alternatively, altitude above ground (AGL) level
is the distance between the aircraft and the ground and therefore,
considers elevation (Figure 2). To use the H/V curve, we need the AGL
and since the GPS data record altitude ASL, we had to convert this
parameter.

<img src="https://github.com/peter-callahan/flight-project/blob/main/doc_images/image2.PNG" style="width:3.33333in;height:1.89028in" />

**Figure 2: A diagram displaying the differences between Mean Sea Level
(MSL), Elevation, Altitude Above Sea Level (ASL), and Altitude Above
Ground Level (AGL).**

Using a large database of elevation data from the National Aeronautics
and Space Administration (NASA) to correspond with our existing
latitudinal and longitudinal values. By subtracting the elevation from
ASL of each respective ASL, we were able to determine the AGL during
flight.

4.3 Parameter Expansion: Airspeed

To determine the actual speed of the aircraft going through the air, we
had to account for wind speed and direction’s effect on the aircraft. We
merged local weather measurements for the area where the flights took
place and using several trigonometric formulas, we were able to
calculate airspeed.

The raw value provided by the GPS receiver is known as ground speed.
Ground speed is the speed an aircraft travels over the surface of the
Earth. However, an aircraft is always flying through a body of air which
is also moving relative to the Earth’s surface. As a result, the ground
speed that is measured via GPS is a combination of the aircraft speed
through the air combined with the speed and direction of the body of air
through which the aircraft travels. Like a boat traveling in water with
a strong current, an aircraft subject to significant winds may move much
slower when measured via GPS. Using vector addition and trigonometry, we
were able to decompose ground speed and wind speed to reveal “indicated
airspeed” which is the speed of an aircraft through a body of air. This
value is most important because it represents the aerodynamic reality of
an aircraft in flight, and is the dimension in which the H/V diagram is
calibrated.

4.4 Classification of “safe” and “unsafe” flights

TALK ABOUT HOW YOU COMPARED THE DATA WITH THE H/V CURVE (STEP 3) AND THE
METRIC BALANCES SAFE AND UNSAFE FLIGHTS (STEP 4)

Classification of each moment of flight as either safe or unsafe was
done by plotting each airspeed and altitude combination on the H/V
curve. Values that plotted in the red area of the curve were classified
as “unsafe” while those in the green area were classified as “safe.”
Calculating the slope of the line for each portion of the curve was
necessary, and conditional logic (altitude greater than 200 feet,
between 100 and 200 feet, and below 100 feet) allowed us to identify
which equation to apply since the line is not linear. Aggregating each
plot (which represented the spatial reality of the aircraft every
second) allowed us to compute the amount of time spent in the “unsafe”
portion of the curve relative to the total amount of time spent in
flight.

4.5 Principal Component Analysis of Flight Features During Unsafe Flight
Envelopes

TALK ABOUT HOW YOU DID THE PCA ANALYSIS

After feature extraction was complete and each data point was classified
as either safe or unsafe, we normalized all numeric values and ran a
PCA. The prediction variable was the classification value (safe or
unsafe) and once the dataset was fit and the PCA variables were
determined, we created a correlation matrix that allowed us to see the
breakdown of each preexisting variable’s correlation to our new PCA
variables. The results are visible in figure 4. This provides insight
into the composition of the newly formed CPA variables, which allows for
a more direct interpretation and root cause analysis.

**5 Evaluation and Results**

5.1 Percentage of Safe and Unsafe Flight Envelopes

Using the classification method described in section 4.4, we were able
to calculate the number of seconds spent in a compromised flight
envelope—within the unsafe flight profile—and estimate the percentage of
risk associated with each individual flight. The range of compromised
flight envelopes is between 2.4% to 19% in risk depending on an
individual flight. When we aggregated all the flight data, we found that
the average percentage of risk exposure was 5.89%.

<img src="https://github.com/peter-callahan/flight-project/blob/main/doc_images/image3.png" style="width:3.33333in;height:3.14514in" />

**Figure 3: A bar chart representing the average percentage of risk of
the aggregated flight missions.**

5.2 Risky Flight Features

We applied principal component analysis to the risk exposure events in
each respective mission and we discovered two features that contributed
to compromised flight envelopes. The first PCA variable deals with
flight parameters directly under the control of the pilot and includes
values like airspeed, altitude, and ground speed. The second PCA
variable deals with environmental factors like visibility, cloud cover,
and altimeter setting. These values are described in more detail below.

<img src="https://github.com/peter-callahan/flight-project/blob/main/doc_images/image4.png" style="width:3.33333in;height:2.74931in" />

**Figure 4: After performing PCA, the values above show the correlations
of each of the features that occurred during risky flight envelopes.**

**Altitude and Airspeed.** We were able to verify the H/V curve’s
threshold for safe and unsafe flight profiles as the data revealed that
the amount of risky flying is highly correlated with the altitude (AGL)
and airspeed figures.

**In-flight Visibility.** We found that the altimeter
setting—atmospheric pressure—is positively correlated with flight risk
and that in-flight visibility is negatively correlated. High altimeter
values indicate higher atmospheric pressure and in the desert
environment where these flights took place, this can lead to blowing
dust which causes in-flight visibility to drop.

From this observation, we can conclude that a reduction in visibility is
one of the primary factors that contributed to Peter’s risky flight
patterns.

**6 Discussion**

Different stages of flight pose different hazards. A visual analysis of
takeoff and landing patterns reveal more violations occur because an
aircraft is lower to the ground and a small slip in airspeed control
results in a violation of H/V. These types of errors are much more
common when an aircraft is involved in taking off and landing, due to
proximity to the ground.

Reductions in visibility, measured by weather station observations of
flight visibility, are a factor in the number of H/V violations that
occur. This is similar to when a motor vehicle driver encounters fog and
slows down, as visual acuity decreases the natural tendency is to
decrease speed to compensate. Generally, faster speeds prevent H/V
violations so the decrease in speed that naturally occurs under poor
conditions of visibility increases the chance a H/V violation will
occur.

**7 Conclusion**

By bringing in two external data sets about elevation and weather, we
can conduct a thorough feature expansion which allowed for an in-depth
analysis of flight risk patterns and revealed new insights surrounding
risk.

Of all the flight missions that Peter completed in the winter of 2014,
he was flying within a compromised flight envelope only 5.89% of the
time. During these compromised flight envelopes, AGL and airspeed
violations were present as was low visibility due to blowing dust. Given
that Peter was flying safe for 94.11% of his flights, we feel confident
in concluding that Peter was a fairly good pilot or, in his own words,
an “okay” pilot.

Acknowledgements

The authors gratefully acknowledge the helpful discussions with and
guidance from Professor David Jurgens and Heeryung Choi.

References

\[1\] Caldwell, John A., J. Lynn Caldwell, John S. Crowley, and Heber D.
Jones (1995). “Sustaining Helicopter Pilot Performance with Dexedrine®
During Periods of Sleep Deprivation”. Aviation Space, and Environmental
Medicine, 66(10), 930-937.

\[2\] Helicopter Flying Handbook, FAA-H-8083-21A (PDF) (2012). U.S.
Dept. of Transportation, FAA, Flight Standards Service. pp. 11-8–11-12.

\[3\] Hunter, David R. and Eugene F. Burke (1994). “Predicting Aircraft
Pilot-Training Success: A Meta-Analysis of Published Research.” The
International Journal of Aviation Psychology, 4(4), 297-313.

\[4\] NATOPS Flight Manual: AH-1W Helicopter (2010). Department of the
Navy, Office of the Chief of Naval Operations, 4-4.Conference Name:ACM
Woodstock conference

Conference Short Name:WOODSTOCK’18

Conference Location:El Paso, Texas USA

ISBN:978-1-4503-0000-0/18/06

Year:2018

Date:June

Copyright Year:2018

Copyright Statement:Rights Retained

DOI:10.1145/1234567890
