---
title: Timeseries Analysis Reporting and Graphing Toolkit
summary: TARGT is a MATLAB based application that enables the temporal segmentation of event-related wearable datastreams. In addition to parsing the data, the application also provides high level summary statistics and heart rate variability metrics.
tags:
  - Data Analysis
  - Wearable Technology
  - Heart Rate Variability
  - Physiological Monitoring

date: '2020-05-31T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: Graphical Abstract of TARGT
  focal_point: Smart

url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---
Collecting, analyzing, and acting on wearable biomonitoring data often presents as a difficult and time intensive challenge across clinical, military, athletic, and general populations. The Timeseries Analysis Reporting and Graphing Toolkit (TARGT) is a high throughput and easy to use software application for digesting, interpreting, and actioning biomonitoring data to better understand systemic responses and resource allocation dynamics within the human operating system. To gain insights related to a specific event, condition, or stimuli, time-series data garnered from wearable devices, can be temporally parsed to interpret the underlying context with respect to health or performance outcomes. The TARGT algorithm parses data into anticipatory (pre-), execution (intra-), and recovery (post-) even and sub-event windows, across any number of participants and events, according to user specified timepoints and durations. Summary statistics are calculated for each variable the device purports to measure. In the presence of cardiovascular inter-beat-interval data, the software incorporates the calculation of popular heart-rate-variability time-domain, frequency-domain, and nonlinear parameters. Data outputs are stored locally, presented in a graphical user interface, and are available to push into cloud-based data management platforms. By examining human behavior through a temporal and contextual lens, TARGT helps end users, practitioners, and decision makers leverage wearable devices to monitor, characterize, predict, and optimize the body’s responses to readiness, workload, and recovery state demands for perception, cognition, and action events. This system provides a multidisciplinary, innovative, and integrated solution for leveraging wearable technology deployment across a multitude of real-world applications in healthcare, defense, and sport. 

This application was built in MATLAB.