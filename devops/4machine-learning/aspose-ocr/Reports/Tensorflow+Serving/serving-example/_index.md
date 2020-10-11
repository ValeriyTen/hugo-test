---
title: Serving example
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-ocr/Reports/Tensorflow+Serving/serving-example
---
### Serving example


# Serving overview

TF.Serving is a technology to manage ML models in production. It allows to model development and model usage tasks.

The detailed actual description ofServing should be read on the official[{**}{+}Tensorflow website{+}{**}](https://www.tensorflow.org/serving/)

Additional info and examples can be found at:

* [https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-1-make-your-model-ready-for-serving-776a14ec3198](https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-1-make-your-model-ready-for-serving-776a14ec3198)
* [https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-2-containerize-it-db0ad7ca35a7](https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-2-containerize-it-db0ad7ca35a7)
* [https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-3-into-the-cloud-7115ff774bb6](https://towardsdatascience.com/how-to-deploy-machine-learning-models-with-tensorflow-part-3-into-the-cloud-7115ff774bb6)
* <https://towardsdatascience.com/tensorflow-serving-client-make-it-slimmer-and-faster-b3e5f71208fb>
* <https://towardsdatascience.com/serving-tensorflow-models-serverless-6a39614094ff>
* <https://becominghuman.ai/tensorflow-serving-by-creating-and-using-docker-images-336ca4de8671>
* <https://weiminwang.blog/2017/09/12/introductory-guide-to-tensorflow-serving/>
* <https://medium.freecodecamp.org/how-to-deploy-an-object-detection-model-with-tensorflow-serving-d6436e65d1d9>
* <https://medium.com/epigramai/tensorflow-serving-101-pt-1-a79726f7c103>
* <https://medium.com/epigramai/tensorflow-serving-101-pt-2-682eaf7469e7>
* <https://habrahabr.ru/company/ods/blog/332584/>
* [https://groups.google.com/a/tensorflow.org/forum/#msg/discuss/nmANL7jUh-Y/GY4GMLH0CAAJ\](https://groups.google.com/a/tensorflow.org/forum/#\!msg/discuss/nmANL7jUh-Y/GY4GMLH0CAAJ)
* [Medium - Deploying Object Detection Model with TensorFlow Serving --- Part 1](https://medium.com/@KailaGaurav/deploying-object-detection-model-with-tensorflow-serving-7f12ee59b036)

And on gitlab:

* <https://github.com/tensorflow/serving>
* <https://github.com/Vetal1977/tf_serving_example>
* <https://github.com/tobegit3hub/simple_tensorflow_serving>

# Quick guide

Here are descriptions of our internal examples of TF.Serving usage.

The sources are available at [Odessa GitLab - Tensorflow-Serving](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving)

Investigation tasks are: [OCR-1](https://sikorsky.dynabic.com/jira/browse/OCR-1)[OCR-37](https://sikorsky.dynabic.com/jira/browse/OCR-37)[OCR-38](https://sikorsky.dynabic.com/jira/browse/OCR-38)[OCR-51](https://sikorsky.dynabic.com/jira/browse/OCR-51)

## Installation and usage

There are three particular environments for serving:

1. For model export
1. For model serving (server)
1. Forprediction request (client)

Let's take a look at each of them:

---

### {**}1. Model export{**}

To export model to the serving format you don't need any additions, the only latest [Tensorflow python api](https://www.tensorflow.org/api_docs/python/).

1. Make an instance of[SavedModelBuilder](https://www.tensorflow.org/api_docs/python/tf/saved_model/builder/SavedModelBuilder)that points to desired directory path to store the model.
1. Build two [Tensor infos](https://www.tensorflow.org/api_docs/python/tf/saved_model/utils/build_tensor_info)that describes model's input and output layer's tensors. Use the [build_tensor_info](https://www.tensorflow.org/api_docs/python/tf/saved_model/utils/build_tensor_info). [Example_1_](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/simpliest_model.py#L93)[Example_2_](https://github.com/tensorflow/serving/blob/master/tensorflow_serving/example/inception_saved_model.py#L124)
1. Make a [Prediction signature](https://www.tensorflow.org/api_docs/python/tf/saved_model/signature_def_utils) using this two tensor infos using [build_signature_def](https://www.tensorflow.org/api_docs/python/tf/saved_model/signature_def_utils/build_signature_def). Set the '_inputs_'. '_outputs_' and _'method_name=tf.saved_model.signature_constants.PREDICT_METHOD_NAME'
1. _legacy_init_op = tf.group(tf.tables_initializer(), name='legacy_init_op')
1. Add all toSavedModelBuilde instance using[add_meta_graph_and_variables](https://www.tensorflow.org/serving/serving_basic#train_and_export_tensorflow_model).
1. Save _'builder.save()'_

Code example:

        def export(self, export_dir, version):
        with self._graph.as_default(), tf.name_scope(self.ScopeName):
            # create model builder
            builder = tf.saved_model.builder.SavedModelBuilder(export_dir)
    
            # create tensors info
            predict_tensor_inputs_info = tf.saved_model.utils.build_tensor_info(self.x)  # self.x = tf.placeholder('float', shape=[None, 3])
            predict_tensor_scores_info = tf.saved_model.utils.build_tensor_info(self.y)  # tf.placeholder('float', shape=[None, 1])
            # build prediction signature
            prediction_signature = (
                tf.saved_model.signature_def_utils.build_signature_def(
                    inputs={INPUT_PLACEHOLDER_NAME: predict_tensor_inputs_info},
                    outputs={OUTPUT_PLACEHOLDER_NAME: predict_tensor_scores_info},
                    method_name=tf.saved_model.signature_constants.PREDICT_METHOD_NAME
                )
            )
    
            # save the model
            legacy_init_op = tf.group(tf.tables_initializer(), name='legacy_init_op')
            builder.add_meta_graph_and_variables(
                self._session, [tf.saved_model.tag_constants.SERVING],
                signature_def_map={
                    MODEL_SIGNATURE_NAME: prediction_signature
                },
                legacy_init_op=legacy_init_op)
    
            builder.save()
    
            print("Successfully exported {} model version '{}' into '{}'".format(
                MODEL_SIGNATURE_NAME, version, export_dir))
    
    
    In this case:
    # GLOBALS
    INPUT_PLACEHOLDER_NAME = """input"""
    OUTPUT_PLACEHOLDER_NAME = """output"""
    MODEL_SIGNATURE_NAME = """regression"""
    
    The values above ​​must match those in the client.

---

### 2. Model serving (server)

To run this model as server (servable) you need to install lot of requirements: Bazel, gRPC, Protobuf, Cuda, cuDNN, openJDK-8.... and requrements for this requirements with specific versions.

So I've prepared a special **+[Dockerfile](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/blob/master/serving/Dockerfile)+{**}{**}++{**}to build the docker image thatcontains the latest versions of all that you need:

There are examples how to run at the bootom of this file.

I recommend to use **_'-v'_** flag to attach exported model directory instead of copying it inside the container while build.

    
    FROM nvidia/cuda:9.1-cudnn7-devel-ubuntu16.04
    
    RUN apt-get update && apt-get install -y \
            build-essential \
            curl \
            git \
            libfreetype6-dev \
            libpng12-dev \
            libzmq3-dev \
            pkg-config \
            software-properties-common \
            swig \
            zip \
            zlib1g-dev \
            libcurl3-dev \
            wget
    
    RUN add-apt-repository ppa:ubuntu-toolchain-r/test -y \
        && apt-get update \
        && apt-get install -y \
            libstdc++6
    
    RUN apt-get install -y \
        python3 \
        python3-dev \
        python3-numpy \
        python-pip \
        cuda-command-line-tools-9-1 \
        && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*
    
    # Set up grpc
    
    RUN pip install pip --upgrade && \
        pip install enum34 futures mock six && \
        pip install --pre 'protobuf>=3.0.0a3' && \
        pip install -i https://testpypi.python.org/simple --pre grpcio && \
        pip install tensorflow-serving-api
    
    RUN echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | tee /etc/apt/sources.list.d/tensorflow-serving.list
    RUN curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | apt-key add -
    RUN apt-get update && apt-get install tensorflow-model-server
    
    # Set up Bazel.
    
    # We need to add a custom PPA to pick up JDK8, since trusty doesn't
    # have an openjdk8 backport.  openjdk-r is maintained by a reliable contributor:
    # Matthias Klose (https://launchpad.net/~doko).  It will do until
    # we either update the base image beyond 14.04 or openjdk-8 is
    # finally backported to trusty; see e.g.
    #   https://bugs.launchpad.net/trusty-backports/+bug/1368094
    RUN add-apt-repository -y ppa:openjdk-r/ppa && \
        apt-get update && \
        apt-get install -y openjdk-8-jdk openjdk-8-jre-headless && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*
    
    
    # Running bazel inside a `docker build` command causes trouble, cf:
    #   https://github.com/bazelbuild/bazel/issues/134
    # The easiest solution is to set up a bazelrc file forcing --batch.
    RUN echo "startup --batch" >>/root/.bazelrc
    # Similarly, we need to workaround sandboxing issues:
    #   https://github.com/bazelbuild/bazel/issues/418
    RUN echo "build --spawn_strategy=standalone --genrule_strategy=standalone" \
        >>/root/.bazelrc
    ENV BAZELRC /root/.bazelrc
    # Install the most recent bazel release.
    ENV BAZEL_VERSION 0.11.1
    WORKDIR /
    RUN mkdir /bazel && \
        cd /bazel && \
        curl -fSsL -O https://github.com/bazelbuild/bazel/releases/download/$BAZEL_VERSION/bazel-$BAZEL_VERSION-installer-linux-x86_64.sh && \
        curl -fSsL -o /bazel/LICENSE https://raw.githubusercontent.com/bazelbuild/bazel/master/LICENSE && \
        chmod +x bazel-*.sh && \
        ./bazel-$BAZEL_VERSION-installer-linux-x86_64.sh && \
        cd / && \
        rm -f /bazel/bazel-$BAZEL_VERSION-installer-linux-x86_64.sh
    
    ## Download TensorFlow Serving
    #RUN git clone --recurse-submodules https://github.com/tensorflow/serving && \
    #  cd serving && \
    #  git checkout
    
    # Build TensorFlow with the CUDA configuration
    ENV CI_BUILD_PYTHON python
    ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
    ENV TF_NEED_CUDA 1
    ENV TF_CUDA_COMPUTE_CAPABILITIES=3.0,3.5,5.2,6.0,6.1
    
    # Fix paths so that CUDNN can be found
    # See https://github.com/tensorflow/tensorflow/issues/8264
    RUN ls -lah /usr/local/cuda/lib64/*
    RUN mkdir /usr/lib/x86_64-linux-gnu/include/ && \
      ln -s /usr/lib/x86_64-linux-gnu/include/cudnn.h /usr/lib/x86_64-linux-gnu/include/cudnn.h && \
      ln -s /usr/include/cudnn.h /usr/local/cuda/include/cudnn.h && \
      ln -s /usr/lib/x86_64-linux-gnu/libcudnn.so /usr/local/cuda/lib64/libcudnn.so && \
      ln -s /usr/lib/x86_64-linux-gnu/libcudnn.so.7 /usr/local/cuda/lib64/libcudnn.so.7
    
    ## Build TensorFlow Serving and Install it in /usr/local/bin
    #WORKDIR /serving
    #RUN bazel build -c opt --config=cuda \
    #    --crosstool_top=@local_config_cuda//crosstool:toolchain \
    #    tensorflow_serving/model_servers:tensorflow_model_server && \
    #    cp bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server /usr/local/bin/ && \
    #    bazel clean --expunge
    
    # Copy local exported weigth (in protobuf format)
    WORKDIR /
    RUN mkdir /data
    COPY serving-export /data
    
    # Open desired PORT
    # use --expose=9000
    #EXPOSE 9000/tcp
    #EXPOSE 9000/udp
    
    # To run...
    CMD ["/bin/bash"]
    # CMD ["tensorflow_model_server --port=9000 --model_name=pargr --model_base_path=/data"]
    
    # Build this Image
    # docker build -t alexg/serving -f Dockerfile
    
    # Run this image
    # docker run --expose=9001 -p 9001:9001 --name=gan-serving -it gan/serving
    # docker run --expose=9000 -p 9000:9000 --name=gan-serving -it gan/serving
    # docker run --expose=9000 -p 9000:9000 --name=pargr-serving -it -v /home/alex_golshtein/virtualenv/par/NetForge/research/paragraph_splitter/serving-export:/data_attach alexg/serving_v3
    # docker run --expose=9001 -p 9001:9001 --name=regression-serving -it -v /home/alex_golshtein/virtualenv/par/NetForge/research/paragraph_splitter/serving-export-regression:/data_attach alexg/serving_v3
    
    
    # run TF Serving server
    # tensorflow_model_server --port=9000 --model_name=pargr --model_base_path=/data
    # tensorflow_model_server --port=9001 --model_name=regression-model --model_base_path=/data_attach

### 3. Client

To make a python client you need to add in your projectfrom tensorflow_serving.apis import predict_pb2  
from tensorflow_serving.apis import prediction_service_pb2  
from grpc.beta import implementations**gRPC** installation guide can be found on the official [gRPC github](https://github.com/grpc/grpc/tree/master/src/python/grpcio) page._

**tensorflow_serving.apis_**_ are from the[tensorflow-serving-api](https://pypi.org/project/tensorflow-serving-api/1.6.0/) package. {**}It built currently only for Python{**} {**}{+}2.7 (04.2018)+{**} **:**_

{{< hint info >}}
Note: The TensorFlow Serving Python API is only published for Python 2, but will work for Python 3 if you either build it yourself, or download the Python 2 version, unzip it, and copy it into your Python 3 path. There is a feature request to publish the Python 3 package as well.

[Proof_1|https://www.tensorflow.org/serving/setup#packages],[Proof_2|https://github.com/tensorflow/serving/blob/master/tensorflow_serving/g3doc/setup.md#tensorflow-serving-python-api-pip-package-pip]
{{< /hint >}}

{**}{+}But+{**}You can copy-paste the 2.7 package to python 3 {_}dist-pachages{_} and it will work As alternative, you can search for custom build for python 3, e.g. this[tensorflow-serving-api-python3](https://pypi.org/project/tensorflow-serving-api-python3/).

To make a Tensor _'tf.contrib.util.make_tensor_proto{_}' you may need a Tensorflow package, but there are the ways not to use Tf at all, look at<https://towardsdatascience.com/tensorflow-serving-client-make-it-slimmer-and-faster-b3e5f71208fb>and<https://medium.com/epigramai/tensorflow-serving-101-pt-2-682eaf7469e7>

{{< hint info >}}


pip3 install grpcio

pip3 install tensorflow-serving-api-python3



{{< /hint >}}

{{< hint info >}}


from grpc.beta import implementations
import numpy as np
import tensorflow as tf
from tensorflow_serving.apis import predict_pb2
from tensorflow_serving.apis import prediction_service_pb2
from grpc.beta import implementations

{{< /hint >}}

{{< hint info >}}


The first load of

self.request.inputs[INPUT_PLACEHOLDER_NAME|INPUT_PLACEHOLDER_NAME].CopyFrom(tf.contrib.util.make_tensor_proto(data, shape=data.shape))

can take a lot of time (5-20 seconds), so I recomment to preload this libraries by


self.request.inputs[INPUT_PLACEHOLDER_NAME|INPUT_PLACEHOLDER_NAME].CopyFrom(tf.contrib.util.make_tensor_proto([0], shape=[1]))
{{< /hint >}}

       class SimpliestModelClient:
        def __init__(self, hostport, servable_name):  # servable_name is the name you use while server start
            """Args:
            hostport: Host:port address of the Prediction Service.
            servable_name: Servable name, chosen on server start
            """
            self.servable_name = servable_name
            self._init_connection(hostport)
    
        def _init_connection(self, hostport):
            host, port = hostport.split(':')
            # create connection
            self.channel = implementations.insecure_channel(host, int(port))
            self.stub = prediction_service_pb2.beta_create_PredictionService_stub(self.channel)
    
            # initialize a request
            self.request = predict_pb2.PredictRequest()
            self.request.model_spec.name = self.servable_name
            self.request.model_spec.signature_name = MODEL_SIGNATURE_NAME
    
        def do_inference(self, data: np.array):
            """
            Mke a request to serve with given data
            """
            self.request.inputs[INPUT_PLACEHOLDER_NAME].CopyFrom(
                tf.contrib.util.make_tensor_proto(data, shape=data.shape))
    
            # predict
            start = time.time()
    
            result = self.stub.Predict(self.request, _SERVING_REQUEST_TIMEOUT)
    
            end = time.time()
            time_diff = end - start
            print('Serving request time elapased: {}'.format(time_diff))
    
            return result.outputs['output'].float_val
    # GLOBALS
    INPUT_PLACEHOLDER_NAME = """input"""
    OUTPUT_PLACEHOLDER_NAME = """output"""
    MODEL_SIGNATURE_NAME = """regression"""

---

## For more examples look at [GitLab](https://git.odessa.dynabic.com/saltov/ugresha/Research/Tensorflow-Serving/tree/master/serving)or, if it's unavailable, at attachements.

## Restore saved in protobuf model.

### 

To restore the saved for serving model, for example to test it, you need to do following:

    
    def restore(self, export_dir):
        with self._graph.as_default(), tf.name_scope(self.ScopeName):
            # Restore the model from last checkpoints
            model = tf.saved_model.loader.load(self._session, [tf.saved_model.tag_constants.SERVING], export_dir)
            return model