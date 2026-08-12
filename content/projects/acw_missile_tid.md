+++
title = "Tracking Missiles with GPS Data"
description = "Collaboration with Tyler Nighswander. A python library/software that pulls in data from global GPS observation stations which it does to monitor the ion content in the ionosphere throughout the sky. The blanket of Ion content measurements is then used to identify when a country (e.g. North Korea) has successfully tested a new potentail nuclear weapon (ICBM)."
author = "Mike Nute"
date = "2022-12-20"
tags = ["python", "bioinformatics"]
# categories = ["themes", "syntax"]
+++

**Github**: [tylerni7/missile-tid](https://github.com/tylerni7/missile-tid)	

# Overview

This software was part of a volunteer collaboration with Tyler Nighswander and other members of the Arms Control Wonk Podcast slack channel. The goal was to create an open source software package that could automatically (and reliably) monitor public GPS data for signs for a space-launch. 

On November 17, 2022 (U.S. Time), North Korea tested the Hwa-Song 17, a large ICBN on a lofted trajectory that sent it well through the Ionosphere. This software was running and was able to verify their claim of having tested it:

[See the verification post on X/Twitter →](https://x.com/i/status/1593452159365918722)

{{< x user="ArmsControlWonk" id="1593452159365918722" >}}

The work that went into this was led by Tyler, although I made substantial contributions along the way as well, and we talked with Jeffrey Lewis on his podcast about this work ([podcast link](https://www.armscontrolwonk.com/archive/1216884/detecting-missile-launches-with-ionospheric-disturbances/)).


# How it Works

When a big, heavy rocket launches, as it accelerates and goes through the ionosphere the engine leaves heavy water vapor clouds which react with the charged particles to temporarily and *locally* reduce the Ion density in the Ionosphere. For arms control advocates and government treaty verification, it would be helpful if there was a way to use this effect to surveil for rocket launches by monitoring the ionosphere's charge almost continuously and over a highly dense set of observations. 

As it turns out, there is a publicly available dataset with the sampling frequency and density and the signal to enable this kind of measurements: GPS signals recorded at continuously-observing reference stations used to calibrate the GPS constellation. In the U.S. this data is hosted on an FTP site by NOAA. Crucially, the U.S. GPS constellation operates on two frequences: L1 (1.5754GHz) and L2 (1.2276GHz), with wavelengths of approximately 19cm and 24.4cm respectively. The ion content at that altitude happens to be a refractive medium, so as these two signals pass through they are deflected *by different amounts*. Thus the difference in distance reported to the GPS satelite between the two frequences on a single transmission is a first-order proxy for the Ion content in the associated path. Subsantial data cleaning and QC must be done to rigorously measure this difference (as well as to store it and look through the resutls), but this is not terribly difficult and the software in here handles though steps.

This software package then does just this. It monitors publicly available CORS data on a regular, periodic basis, does the data crunching required to map the ion content in the sky, and then does a screen for patterns that look like a depletion event caused by a space-launch or ICBM test. Tyler has set up a [bot](https://x.com/ionplotbot) to post periodic graphics of the ion content over the Korean penninsula, although lately it seems like the data sources aren't as reliable as they used to be. 
