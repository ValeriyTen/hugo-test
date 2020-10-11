---
title: Player and layers architecture
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-ocr/Reports/Neural+Network+Player+for+%2ENET/player-and-layers-architecture
---
### Player and layers architecture
# Player and layers architecture

### Based on Task

Design Layer Architecture (Interfaces and Implementation)  
[https://ugresha.dynabic.com/jira/browse/OCRNET-3315](https://ugresha.dynabic.com/jira/browse/OCRNET-3315)

### Player architecture

Player delivers classes for loading, creating and processing neural networks. It deals with neural networks which can be represented as chain of different layers, with no intermediate memory. All calculations is performed with single precision arithmetic (float). Sometimes implementation can use double for accumulators.

##### Neural network construction

Create several layers and put them via AddLayer into NeuralNetwork (now it is called NeuralNetworkPlayer). Now you can process your data via newly created neural network. You can make forward pass in batch mode (array of array of floats aka float) or for single sample (array of floats aka float)

##### Deserialization

Providers for neural networks can be any handy framework (TensorFlow, Keras, Caffe, Torch). Layers are initialized with its properties and weights as array of floats (float).  
The chain creation process from layers is fully manual at the moment.

##### Evaluation

All data are supposed to be array of floats.

    NeuralNetworkLayer[] layers =
    {
     new FullyConnectedLayer("layer1",
       new float[,] { { 0.01f, 0.05f, 0.09f }, { 0.02f, 0.06f, 0.10f }, { 0.03f, 0.07f, 0.11f }, { 0.04f, 0.08f, 0.12f } },
       new float[] { 0.13f, 0.14f, 0.15f, 0.16f },
       NeuralNetworkLayer.ActivationType.Tanh ),
     new FullyConnectedLayer("layer2",
       new float[,] { { 0.17f, 0.19f, 0.21f, 0.23f },{ 0.18f, 0.20f, 0.22f, 0.24f } },
       new float[] { 0.25f, 0.26f },
       NeuralNetworkLayer.ActivationType.Softmax )
    };
    
    NeuralNetwork simpleNetwork = new NeuralNetwork(layers,"2LayerPerceptron");
    float[] input = new float[] { 1f, 2f, 3f };
    float[] output = simpleNetwork.Compute(input);

\  
Parallelization is implemented as:

    public float[][] ComputeBatch(float[][] batch)
            {
                float[][] data = new float[batch.Length][];
                Array.Copy(batch, data, batch.Length);
                for (int l = 0; l < this.layers.Count; l++)
                {
                    NeuralNetworkLayer layer = this.layers[l];
                    Parallel.For(
                        0,
                        batch.Length,
                        Consts.MatrixOperationsParallelOptions,
                        i => data[i] = layer.Compute(data[i]));
                }
                return data;
            }

##### Checking network architecture and connections

NeuralNetwork class along with NeuralNetworkLayers class have several methods that outputsinput/output dimensionsand checks dimentionality correctness.CheckInputShape() is always called in DEBUG mode in network computation. You can use Summary() method for getting Keras-like summary of your network with layers names, their shapes and # of params. GetLayersStatistics() allows you to get the time taken for processing data for each network's layer. It works when special directive DEVEL is defined (+it works in Release and in DevelRelease configurations+).

### Layers' architecture

Any layer inherits from Layer (NeuralNetworkLayer). It is defined with weights data and its properties (e.g., activation function). There are no separate layers with activation functions. All layers are dublicated with SIMD version. The later has 'SIMD' suffix in the name

##### Deserialization

The process is fully manual. There are some helpers to load data from csv files

##### Evaluation

Any Layer is evaluated by calling Compute() function

    FullyConnectedLayer layer = new FullyConnectedLayer("FCL1_30x50", weights, biases, NeuralNetworkLayer.ActivationType.None);
    float[] output = layer.Compute(input);

    float[][] protoweights = new float[][] {{0.17f, 0.19f, 0.21f, 0.23f },
    {0.18f, 0.20f, 0.22f, 0.24f }};
    
    float[] biases = new float[] { 0.25f, 0.26f };
    FullyConnectedSimdLayer layer = new FullyConnectedSimdLayer(weights, biases, NeuralNetworkLayer.ActivationType.None);
    
    float[] input =  new float[] { 0.4699f, 0.5227f, 0.5717f, 0.6169f };
    float[] output = layer.Compute(input);

### Import from Netforge

A static method

    NeuralNetwork CreateNetworkFromJson(string jsonFile, bool useSimd = false)

allows you to import network architecture and weights from Netforge experiments.  
jsonFilespecifies the path to structure.json thatcontainnetwork architecture. It is assumed thatcsv-fielswith layers' weights are located near thejson-file as they are exported by Netforge by default. So implemented method will find thosecsvseasily and loads all the weights.  
Imported examples (json and csv files are located in Data folder ([https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/tree/master/Tests/FCLTimeTests/Data](https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/tree/master/Tests/FCLTimeTests/Data)):

* Multilayer perceptron[https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/master/Tests/FCLTimeTests/Program.cs#L467](https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/master/Tests/FCLTimeTests/Program.cs#L467)
* Bicl Network[https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/master/Tests/FCLTimeTests/Program.cs#L496](https://ugresha-scm.dynabic.com/research/Aspose.NeuralNetworkPlayer/blob/master/Tests/FCLTimeTests/Program.cs#L496)

\

#### Debugging Layers computation

If your face some errors ordiscrepancy in outputs of your layers - you can use simple numpy calculations to check the correctness of matrix multiplications and activationsapplied.  
Here is a small sample how to check fully connected layers computations in Python with Numpy.

    import numpy as np
    import os
    import csv
    
    def read_weights(csvfile):
        with open(csvfile, 'r') as f:
            reader = csv.reader(f)
            your_list = list(reader)
            arr_str = your_list[2:]
            arr_f = []
            for line in arr_str:
                w_str = line[0].split(' ')
                arr_f.append([float(s) for s in w_str])
            return np.array(arr_f).T
    
    
    def read_biases(csvfile):
        with open(csvfile, 'r') as f:
            reader = csv.reader(f)
            your_list = list(reader)
            b_str = your_list[2][0].split(' ')
            return np.array([float(s) for s in b_str])
    
    b004 = read_biases('mnist_export/export/004_FullConnected.b.csv')
    w004 = read_weights('mnist_export/export/004_FullConnected.w.csv')
    b002 = read_biases('mnist_export/export/002_FullConnected.b.csv')
    w002 = read_weights('mnist_export/export/002_FullConnected.w.csv')
    b006 = read_biases('mnist_export/export/006_FullConnected.b.csv')
    w006 = read_weights('mnist_export/export/006_FullConnected.w.csv')
    
    inp = read_weights('mnist_export/export/tests/test_000_inp.csv')
    
    alpha = 0.1
    def softmax(x):
        e_x = np.exp(x)
        return e_x / e_x.sum()
    
    
    def relu(x):
        np.max([0.0,x])
    
    def leaky_relu(alpha, x):
        if x<0:
            return alpha*x
        else:
            return x
    
    out1 = w002.dot(inp)+b002
    f1 = [leaky_relu(alpha,x) for x in out1]
    out2 = w004.dot(f1)+b004
    f2 = [leaky_relu(alpha,x) for x in out2]
    out3 = w006.dot(f2)+b006
    
    res = softmax(out3)
    print(res)

\

###### Further improvements and suggestions

NN processing algorithms require debug routine for easy debugging and quick errors finding.  
Add debug routine for easy embed dumping outputs/messages in code, self-excluding from FINAL solution, easy to be tuned off/on groups (categories) of layers by config or registry.  
DEBUG version should contain complex and computationally expensive invariants. Use such checks for algorithms self-testing alike unit tests