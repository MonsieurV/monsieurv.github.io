---
layout: post
author: yoan
title: "Home item recognition using neural networks"
categories: [Neural networks]
tags: [Neural networks, Machine learning]
brief: "Computer, is that a chair or a carpet?"
draft: true
---

My API Key `a8ea58d79241380fd193d35286059b72e5e9e3f7`

A very long time ago, someone asked me how hard it would be to classify home
item to enrich a product database with attributes like furniture family, color

Not so long ago, a sect member give me a link to IBM Watson documentation

Judge how precisely it classify (exact category, color)

Pretty neat

Training here
https://visual-recognition-demo.mybluemix.net/train

Try it here
https://visual-recognition-demo.mybluemix.net/

How it works
http://www.ibm.com/watson/developercloud/doc/visual-recognition/
https://www.ibm.com/watson/developercloud/visual-recognition.html

Doc
https://www.ibm.com/watson/developercloud/visual-recognition/api/v3/#classify_an_image
https://watson-api-explorer.mybluemix.net/apis/visual-recognition-v3#!/visual45recognition/post_v3_classify

Pricing?
http://www.ibm.com/watson/developercloud/visual-recognition.html#pricing-block
`$0.002 / Image` Free Tier 250 images / day
https://www.ibm.com/blogs/bluemix/2016/12/lower-price-watson-visual-recognition/

Sample code:
* https://github.com/watson-developer-cloud/python-sdk/blob/master/examples/visual_recognition_v3.py
* https://github.com/watson-developer-cloud/node-sdk#visual-recognition
* TODO Create simply with curl, requests and fetch... in browser demo (with my API key?)

## This is not a lone island (anymore)

Neats!
https://cloud.google.com/vision/

Not checked
* https://cloudsight.ai/
* https://www.clarifai.com/
* https://imagga.com/
* https://www.microsoft.com/cognitive-services/en-us/computer-vision-api
* https://aws.amazon.com/rekognition/?nc1=h_ls
