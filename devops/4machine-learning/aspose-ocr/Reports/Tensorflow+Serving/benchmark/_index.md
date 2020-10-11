---
title: Benchmark
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-ocr/Reports/Tensorflow+Serving/benchmark
---
### Benchmark
Benchmarks are implemented in[simpliest_model_](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/simpliest_model.py)and[splitter_model._](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/splitter_model_benchmark.py)

| ---------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|      [simpliest_model_](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/simpliest_model.py)      |  This is a simpliest model (regression) the works very fast (about 0.005 sec for 100k batch) and will be used for the networking and data prepare benchmarking.  |
|  [splitter_model_](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/splitter_model_benchmark.py)  |                                      This is a model for splitting a page of text on paragraphs, that works in production.                                       |   
This benchmark made for two different bath size (1 and 1000000) for each model. The benchmark performs a 10000 iterations for each batch. |

The "Direct" columns shows the same test with local model loading (i.e. withouttf.serving)

This is a shortened table, so look into attached excel file to see a full data.[[sikorskywiki:^Serving_benchmark.xlsx.WebHome]]

The first row is excluded from computations, because the first request initiate model restore operation into gpu memory, that requires a lot of time.  
\

[[sikorskywiki:^Serving_benchmark.xlsx.WebHome]]

{{viewxls name="Serving_benchmark.xlsx" sheet="list1"}}{{/viewxls}}

* Full time = "Data prepare" + "Request" (the full time that required to make prediction on client)
* Data prepare = Diff = "Full time" - "Request" (the time to prepare request data - to copy tensor data into request body)
* Direct = prediction time for this batch using local request (local model load) (without serving)
* Network loss= "Request" - "Direct" (Estimated loss of network communication between the client and the server. Since it is not possible to measure the exact execution time of the prediction on the server, so it's used the time ofa similar operation when the model is loaded locally)

| -------------------------------------------------------- | ------------------ | ----------- | --------------------- | ----------- | ------------- | -------------- | ------------- | ------------- | ------------- |
|                         Test name                        |       Replies      |  Batch size |       Data type       |  Data size  |   Full time   |  Request time  |  Data prepare |     Direct    |  Network loss |
|       Test on Simpliest model Batch 1 of 3 float32       |  10000 iterations  |      1      |       3 float32       |  12 bytes   |  0,000687087  |  0,000539889   |  0,000147198  |  0,000223841  |  0,000316047  |
|        Test on Simpliest model Batch 1000000 of 3        |  10000 iterations  |   1000000   |       3 float32       |  11.44 MiB  |  0,087823611  |  0,054979556   |  0,032844055  |  0,005047318  |  0,049932237  |
|  Test on Paragraph model Batch 1 of 1024, 32, 1 float32  |  10000 iterations  |      1      |  1024, 32, 1 float32  |   128 KiB   |  0,050422021  |   0,05084802   |   0,000426    |  0,044906083  |  0,005515938  |
|  Test on Paragraph model Batch 50 of 1024 32 1 float32   |  10000 iterations  |     50      |  1024, 32, 1 float32  |   6.4 MiB   |  0,733908587  |  0,729150824   |  0,004757717  |  0,57794013   |  0,151210695  |   
\  
\  
\  
--  
\  
\  
\ |