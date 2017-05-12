---
title: Overview of Custom Vision Service machine learning | Microsoft Docs
description: Use this to bring the power of machine learning to your applications.
services: cognitive-services
author: v-royhar
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 05/03/2017
ms.author: v-royhar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51a4db987a6125daab7bb74affc04a5427ea3437
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="overview"></a>Overview

## <a name="custom-vision-service-brings-the-power-of-machine-learning-to-your-apps"></a>Custom Vision Service brings the power of machine learning to your apps

Custom Vision Service is a tool for building custom image classifiers. It makes it easy and fast to build, deploy, and improve an image classifier. We provide a REST API and a web interface to upload your images and train.

## <a name="what-can-custom-vision-service-do-well-what-cant-it-do"></a>What can Custom Vision Service do well? What can't it do? 

Custom Vision Service is a tool for building custom image classifiers, and for making them better over time. For example, if you want a tool that could identify images of "Daisies", "Daffodils", and "Dahlias", you could train a classifier to do that. You do so by providing Custom Vision Service with images for each tag you want to recognize.

Custom Vision Service works best when the item you are trying to classify is prominent in your image. Custom Vision Service does "image classification" but not yet "object detection." This means that Custom Vision Service identifies whether an image is of a particular object, but not where that object is within the image.

Very few images are required to create a classifier -- 30 images per class is enough to start your prototype. The methods Custom Vision Service uses are robust to differences, which allows you to start prototyping with so little data. However, this means Custom Vision Service is not well suited to scenarios where you want to detect very subtle differences (for example, minor cracks or dents in quality assurance scenarios.)

Custom Vision Service is designed to make it easy to start building your classifier, and to help you improve the quality of your classifier over time.

## <a name="next-steps"></a>Next steps

[Build a Classifier](getting-started-build-a-classifier.md)

