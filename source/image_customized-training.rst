Customized Training
=================================

In order to use the customisation module for adding keywords and/or styles, it needs to be trained with handpicked data.

The steps required for training are:

* add positive and negative examples
* run training
* repeat training for fine-tuning in order to improve results
* test trained customisation module using the predict function

.. image::
   data/PA3.png
   :align: center

Adding positive and negative samples
-------------------------------------

To add images to SDK send POST request to the following endpoint.
::

  curl 127.0.0.1:5000/add/<type>/<tag> -X POST -F "data=@./your_img.jpg"

where type is type of the sample (can be only either 'positive' or 'negative') and tag is name of the custom tag.

.. note::



Below is an example on how to do add multiple samples at the same time using Python.
::

    from multiprocessing import Pool
    import requests
    import os
    import fnmatch

    #Function that adds one sample to the training data
    def add_sample((img, sample_type, tag)):
      with open(img,'rb') as image:
        data = {'data': image}
        r = requests.post('http://127.0.0.1:5000/add/%s/%s'%(sample_type, tag), files=data).json()
      return r



This example shows how to add positive and negative samples to the customized training. In the example, we want to train a
#filter for people. Hence, the positive samples are all the images with people; the negative samples will be the other images.

::

    path_to_positives = '/your_image_folder/Positives/' #Put positive samples in here
    path_to_negatives = '/your_image_folder/Negatives/' #Put negative samples in here
    tag = 'people' #Tag used for the filter

    #Get all the paths to the images
    pos_image_names = [path_to_positives + name for name in fnmatch.filter(os.listdir(path_to_positives), '*.jpg')]
    neg_image_names = [path_to_negatives + name for name in fnmatch.filter(os.listdir(path_to_negatives), '*.jpg')]

    #Merge the two lists so that they can be passed to the pool.map() function.
    images = [(item, 'positive', tag) for item in pos_image_names] + [(item, 'negative', tag) for item in neg_image_names]

    pool = Pool(50)
    r = pool.map(add_sample, images)
    pool.close()
    pool.join()


Training
------------

To run training, you can send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/train/<tag>

The following request will return a json file with field task_id that can be used to get status of training:
::

  curl 127.0.0.1:5000/status/<task_id>
  
Resetting a model
------------

To reset a model, you can send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/clean/<tag>



Prediction from Images
-----------------------

Custom model only
^^^^^^^^^^^^^^^^^^^^^^^^

To get the prediction of a custom model with the name 'tag', you can call the following endpoint:
::

  curl 127.0.0.1:5000/predict/custom/<tag> -X POST -F "data=@./your_img.jpg"

In python:
::

  def get_custom_predictions(img, tag):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/custom/%s'%tag, files=data).json()
     return pred

All custom models
^^^^^^^^^^^^^^^^^^

You can also get predictions for **all** custom models by calling the endpoint:
::

  curl 127.0.0.1:5000/predict/custom -X POST -F "data=@./your_img.jpg"

In python:
::

  def get_custom_predictions(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/custom', files=data).json()
     return pred

All models
^^^^^^^^^^

Lastly, you can use the general endpoint to get a prediction for all custom models, as well as the base models:
::

  curl 127.0.0.1:5000/predict -X POST -F "data=@./your_img.jpg"

In python:
::

  def get_predictions(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict', files=data).json()
     return pred


Prediction by Features
----------------------

You can also do prediction using features, which is significantly faster.

Extract features
^^^^^^^^^^^^^^^^

For this, you first have to extract the features. This can be done by calling the following endpoint:
::

  curl 127.0.0.1:5000/get_features -X POST -F "data=@./your_img.jpg" --output features.json

In python:
::

  def get_features(img):
     with open(img,'rb') as image:
         data = {'data': image}
         features = requests.post('http://127.0.0.1:5000/get_features', files=data).json()
     return features


Predict with Features
^^^^^^^^^^^^^^^^^^^^^

Predictions by features from custom models can be obtained by calling the endpoint:
::

  curl 127.0.0.1:5000/predict_by_features -X POST -F "data=@./features.json"

In python:
::

  def get_predictions_by_features(features):
     data = {'data': io.StringIO(unicode(json.dumps(features)))}
     pred = requests.post('http://127.0.0.1:5000/predict_by_features', files=data).json()
     return pred


Load a pretrained custom Model
------------------------------

You can load a pretrained custom model by calling the following endpoint:
::

  curl 127.0.0.1:5000/set_state -X POST -F "data=@./state.tar"


Caching system
--------------

To prevent from uploading the same images multiple time for multiple models one can use caching system.

There are two steps at using caching system:

1. Add image to caching system.

2. Assign cached image to custom model or use cached image to get prediction for custom model.

Adding image to cache
^^^^^^^^^^^^^^^^^^^^^

To add image to caching system, you can send a POST request to the following endpoint:
::

  curl 127.0.0.1:5000/add?image_id=<image_id> -X POST -F "data=@./your_img.jpg"
  
where image_id is an optional argument. Without this argument, the system will generate a random ID number and return it as a response.

In python:
::

  def add_sample_to_cache(img, image_id=None):
      with open(img,'rb') as image:
          data = {'data': image}
          url = 'http://127.0.0.1:5000/add'
          if image_id:
              url += '?image_id=' + image_id
          r = requests.post(url, files=data).json()
      return r

Assigning cached image
^^^^^^^^^^^^^^^^^^^^^^

To assign cached image to custom model, send a GET request to the same endpoint:
::

  curl 127.0.0.1:5000/add?image_id=<image_id>

where image_id is ID that you got on previous step.

In python:
::

  def assign_cached_image(sample_type, tag, image_id):
      url = 'http://127.0.0.1:5000/add/%s/%s?image_id='%(sample_type, tag, image_id)
      r = requests.get(url).json()
      return r


Prediction on cached image
^^^^^^^^^^^^^^^^^^^^^^^^^^

To get custom models predictions for cached image, send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/predict/custom?image_id=<image_id>

Or you can get prediction one for one custom model:
::

  curl 127.0.0.1:5000/predict/custom/<tag>?image_id=<image_id>

In python:
::

  def get_predictions_cached(image_id, tag=None):
      if tag is None:
          pred = requests.get('http://127.0.0.1:5000/predict/custom?image_id=%s'%(image_id)).json()
      else:
          pred = requests.get('http://127.0.0.1:5000/predict/custom/%s?image_id=%s'%(tag, image_id)).json()
      return pred
