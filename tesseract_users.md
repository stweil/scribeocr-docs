---
layout: default
title: Tesseract Users
nav_order: 6
---

# Overview
This article answers common questions from users of the Tesseract API or CLI. 

### What is the relationship between Scribe OCR and Tesseract?
The built-in `Recognize` feature in Scribe OCR uses Tesseract for recognition.  Additionally, Scribe OCR can be used for proofreading existing OCR data from the Tesseract CLI/API.

### How can I proofread data from the Tesseract CLI?
Scribe OCR allows for proofreading `.hocr` files produced by the Tesseract CLI or API.  Be sure to enable character-level bounding boxes by setting the `hocr_char_boxes` option.  An example command is below.
```sh
tesseract example.png example.hocr -c hocr_char_boxes=1 -c tessedit_create_hocr=1
```

After running recognition, upload both the source image file(s) and `.hocr` files to Scribe OCR to begin proofreading.

### How is Tesseract run in the browser?
Scribe OCR uses [Tesseract.js](https://github.com/naptha/tesseract.js), which is the JavaScript/WebAssembly port of Tesseract.  

# Scribe OCR Recognition vs. Tesseract

### Will running recognition with Scribe OCR produce results identical to the Tesseract CLI?
Not by default.  While Scribe OCR uses Tesseract for recognition, this does not mean that results will be identical when comparing between Scribe OCR and the Tesseract CLI.  By default, Scribe OCR implements several unique steps that result in different recognition results.  While results will vary, on average, the results produced by Scribe OCR should be more accurate for real-world (unprocessed) documents.

Some of the differences that produce different results are listed below.  This list is not exhaustive.
1. Scribe OCR implements an auto-rotate pre-processing step.
	1. This significantly improves accuracy with documents that have some amount of rotation.
2. Scribe OCR combines the results of the Tesseract Legacy and Tesseract LSTM model to improve the accuracy of text and bounding boxes.
	1. While Tesseract has an `oem` option called `OEM_TESSERACT_LSTM_COMBINED`, this option does not actually combine the results of both models, it simply uses the Legacy engine as a fallback strategy when it believes the LSTM engine failed.
3. Scribe OCR uses a fork of Tesseract with changes to font recognition and layout analysis.
	1. Scribe OCR can reliably detect font family and text style (normal and italic), while Tesseract cannot.
	2. Scribe OCR categorizes more regions as horizontal text compared to Tesseract, and fewer regions as vertical text or noise.

### How can I run an unmodified version of Tesseract within Scribe OCR?
Most of the changes made to Tesseract can be disabled by setting the options listed below.  This should produce results similar to running the Tesseract LSTM model from the command line.

- Enable `Info` -> `Optional Features` -> `Advanced Recognition Options`
- Select `Recognize` -> `Alternative Builds` -> `Vanilla Tesseract`
- Select `Engine` -> `LSTM`

Note that even with these settings, there still is a possibility that results will not exactly match your local installation of Tesseract.  These settings do not disable auto-rotation, and you may have a different version installed, among other differences.  If having results that exactly match your local installation of Tesseract is important to you, you should run recognition locally and use Scribe OCR for proofreading. 