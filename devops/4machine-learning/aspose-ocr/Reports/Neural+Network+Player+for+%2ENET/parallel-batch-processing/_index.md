---
title: Parallel Batch Processing
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-ocr/Reports/Neural+Network+Player+for+%2ENET/parallel-batch-processing
---
### Parallel Batch Processing
# Parallel Batch Processing

JIRA Issue: [https://ugresha.dynabic.com/jira/browse/OCRNET-3318](https://ugresha.dynabic.com/jira/browse/OCRNET-3318)  
Code: [https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/BatchProcessingImprovements/Aspose.NeuralNetworkPlayer/NeuralNetwork.cs#L157](https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/BatchProcessingImprovements/Aspose.NeuralNetworkPlayer/NeuralNetwork.cs#L157)  
**To process a batch of inputs to a neural network in parallel a separate method was implemented: ComputeBatch. Its performance is measured and analysed.**  
\  
It uses .NET's Parallel library for multithread processing:  
        public float ComputeBatch(float batch)  
          
\  
            float data = new floatbatch.Length;  
            Array.Copy(batch, data, batch.Length);  
            for (int l = 0; l < this.layers.Count; l++)  
              
\  
                NeuralNetworkLayer layer = this.layersl;  
                Parallel.For(  
                    0,  
                    batch.Length,  
                    Consts.MatrixOperationsParallelOptions,  
                    i => datai = layer.Compute(datai));  
            \  
              
            return data;  
          
   
However the speed up was not equal to the factor of parallel threads used for computation. See the table below.  
One possible bottleneck is in allocation of memory for new output arrays owhen computing each layer. An attempt to refactor the code was made to use preallocated arrays through all layers of the network.  
   
It was done using ref keywords and without creating new arrays. See the code: [https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/BatchProcessingImprovements/Aspose.NeuralNetworkPlayer/NeuralNetwork.cs#L157](https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/BatchProcessingImprovements/Aspose.NeuralNetworkPlayer/NeuralNetwork.cs#L157)  
**new Compute method**  
int maxSize = this.GetMaxOutputLen(input.Length);              
float inp = new floatmaxSize;              
float outp = new floatmaxSize;              
Buffer.BlockCopy(input, 0, inp, 0, Buffer.ByteLength(input));              
for (int l = 0; l < this.layers.Count; l++)              
  
 this.layersl.Compute(ref inp, ref outp);  
...  
  
   
But tests show no improvement. Details are in the table below.  
Time was measured for 1000 tests for fully connected layer with 800 inputs and 500 outputs calculation:

1. No activation, BatchSize=1
1. No activation, BatchSize=4
1. RELU activation, BatchSize=1
1. RELU activation, BatchSize=4
1. SOFTMAX activation, BatchSize=1
1. SOFTMAX activation, BatchSize=4

| -------------------------------------------------------------------------------------------------------------------- | ------- | ------------------- | ------ | ------------------- | ------ | ------------------- |
|                                              **Test/** \  
**Machine**                                               |  **1**  |        **2**        | **3**  |        **4**        | **5**  |        **6**        |
|                          AMD64 Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz L2CacheSize=1024Kb                          | 187 ms  | 419 ms \  
**x1.8** | 187 ms | 425 ms \  
**x1.8** | 197 ms | 434 ms \  
**x1.8** |
|          AMD64 Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz L2CacheSize=1024Kb  **SIMD** Float vector size = 8          |   53    |  109 \  
**x1.9**   |   51   |  110 \  
**x1.9**   |   64   |  120 \  
**x2.1**   |
|              **Refactored Compute{**}AMD64 Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz L2CacheSize=1024Kb              |   190   |  421 \  
**x1.8**   |  193   |  378 \  
**x2.0**   |  203   |  392 \  
**x2.0**   |
| **Refactored Compute{**}AMD64 Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz L2CacheSize=1024KbSIMD Float vector size = 8 |   54    |  119 \  
**x1.8**   |   56   |  122 \  
**x1.8**   |   55   |  130 \  
**x1.7**   |   
  |

#### Conclusion

<ins>Refactoring Compute method did not help. Expected speedup with parallel batch processing is about **x4** (the time with batchsize=4 should be the same as with batch size = 1 when 4 threads are used). But the actual speedup was about **x1.7-x2{**}.</ins>  
<ins>Further investigation and tests needed.</ins>  
 