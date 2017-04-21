---
title: Glossary for Video API in Microsoft Cognitive Services | Microsoft Docs
description: Get definitions of key terms used in the Video API in Cognitive Services.
services: cognitive-services
author: CYokel
manager: ytkuo
ms.service: cognitive-services
ms.technology: video
ms.topic: article
ms.date: 05/23/2016
ms.author: chbryant
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e3e3aa16cf4557d94411e3c85783008aed022d0c
ms.lasthandoff: 04/12/2017

---

# <a name="glossary"></a>Glossary

## <a name="a"></a>A

## <a name="b"></a>B

## <a name="c"></a>C

## <a name="d"></a>D

### <a name="duration"></a>Duration

The length of the fragment, in ticks (within the JSON result).

## <a name="e"></a>E

### <a name="events"></a>Events

An array of events (within the JSON result). The outer array represents one interval of time. The inner array consists of 0 or more events that happened at that point in time. 

## <a name="f"></a>F

### <a name="face-detectiontracking"></a>Face Detection/Tracking

This is derived from the video face detection and tracking results (this is different from the Face API’s Face ID). This is the index of a face. A given individual should have the same ID throughout the overall video. Due to limitations in the detection algorithm (e.g. occlusion) this cannot be guaranteed.

### <a name="fragments"></a>Fragments

The JSON metadata is chunked up into different segments called fragments. Each fragment contains a start, duration, interval number, and event(s). 

### <a name="framerate"></a>Framerate

Frames per second of the video.

## <a name="g"></a>G

## <a name="h"></a>H


## <a name="i"></a>I

### <a name="interval"></a>Interval

The length of each event within the fragment, in ticks (within the JSON result).


## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

## <a name="m"></a>M

### <a name="motion-detection"></a>Motion Detection

Motion detection is the action of detecting motion in a video.

## <a name="n"></a>N


## <a name="o"></a>O

### <a name="offset"></a>Offset

This is the time offset for timestamps (within the JSON result). In version 1.0 of Video APIs, this will always be 0. In future scenarios we support, this value may change.


## <a name="p"></a>P


## <a name="q"></a>Q

## <a name="r"></a>R

### <a name="region-of-interest"></a>Region of Interest

This is derived from the motion detection JSON results. Regions refers to the area in your video where you care about motion.  

## <a name="s"></a>S

### <a name="sensitivity"></a>Sensitivity 

User can specify ‘sensitivity level’ for motion detection, which are high, medium, and low, default is medium. Higher sensitivity means more motions will be detected at a cost that more false alarms will be reported.

### <a name="stabilization"></a>Stabilization

Stabilization takes a shaky video and smooths and stabilizes it.

### <a name="start"></a>Start

The start time of the first event, in ticks (within the JSON result).

### <a name="subscription-key"></a>Subscription key

Subscription key is a string that you need to specify as a query string parameter in order to invoke any Face API. The subscription key can be found in My Subscriptions page after you sign in the Microsoft Cognitive Services portal. There will be two keys associated with each subscription: one primary key and one secondary key. Both can be used to invoke API identically. You need to keep the subscription keys secure, and you can regenerate subscription keys at any time from My Subscriptions page as well. 

## <a name="t"></a>T

### <a name="timescale"></a>Timescale

The “ticks” per second of the video (within the JSON result).

### <a name="type"></a>Type

This is derived from the motion detection JSON results.  2 refers to motion, 4 refers to light change. 

## <a name="u"></a>U

## <a name="v"></a>V

### <a name="video-api"></a>Video API

Video API is a cloud-based API that provides the most advanced algorithms for stabilization, face detection and tracking, and motion detection in video. 

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Y

## <a name="z"></a>Z

 * [Link back to Overview](Home.md)
 * [Link back to Get Started with Video API](GetStarted.md)
 * [Link back to How to Call Video API](./How-To/HowtoCallVideoAPIs.md)

