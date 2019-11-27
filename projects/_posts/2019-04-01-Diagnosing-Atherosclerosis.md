---
layout: post
category: proj
title: Diagnosing Atherosclerosis
github: https://github.com/kaisubr/Photoplethysmogram_API_22
---

In this independent research project, I aimed to develop a method for determining a severity for atherosclerosis through analysis of waveforms representing light absorption by the blood when a finger is held up to an optical camera. This was done through (1) a series of preprocessing filters, initially through a basic dark-pixel removal algorithm, then later adapted into a convolutional neural network for shadow detection; (2) peak-detection and heart rate variability calculations; (3) blood-pressure inferences through user-input data, such as height/weight used to determine arm length; (4) EKG inferencing through the available data; and (5) output a severity for atherosclerosis.

## ABSTRACT

##### A cost-efficient and reliable form for obtaining volumetric measurements of the heart through optical techniques

The leading cause of death is heart disease, especially atherosclerosis, the accumulation of fats and cholesterol on the linings of artery walls. The narrowing of the artery can limit the flow of oxygen-rich blood to vital organs in the body, such as the heart. If not diagnosed beforehand, blood flow can gradually decrease to the point where the victim may have a heart attack or stroke. Typically, doctors use electrocardiograms and exercise stress tests to diagnose atherosclerosis. However, the cost of diagnosis, let alone treatment, makes patients more willing to ignore the problem. The project aims to develop a more cost-effective form of diagnosis accessible to more people, preventing thousands of deaths due to heart attack or stroke. Using measurements of light absorption by the blood, one can identify patterns common to patients with atherosclerosis.

When a finger is held up to a camera, faint red pulses are noticeable, indicating variations of light absorption through the blood. Quantifying these pulses can help to generate a waveform whose features provide data that can be used to identify abnormalities. Through peak detection, waveform manipulation, and age index calculations, it is possible to determine the severity of atherosclerosis in a patient. By developing a smartphone application that can recognize these abnormalities in light absorption by the blood of a finger, the study aims to determine whether a patient may have atherosclerosis in a more cost-effective and accessible manner. Research in optically obtained volumetric measurements of the heart and its application in diagnosing health disorders will not only create a scientific impact, but also a societal impact, since a more cost-effective form of diagnosis gives more people access to medical technology and prevents deaths caused by cardiovascular disease.

Developing a smartphone application to accurately diagnose atherosclerosis based on optical measurements can prevent heart attacks and strokes and open doors to cost-effective, accessible medical technology.

