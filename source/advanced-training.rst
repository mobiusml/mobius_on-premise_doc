Advanced: Customized Training
=================================

In order to use the customisation module for adding keywords and/or styles, it needs to be trained with handpicked data.

Steps for training:

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

where type is type of the sample (positive or negative) and tag is name of the custom tag.

You can do it from python as well.
::

  def add_sample(img, sample_type, tag):
    with open(img,'rb') as image:
      data = {'data': image}
      r = requests.post('http://127.0.0.1:5000/add/%s/%s'%(sample_type, tag), files=data).json()
    return r


Training
------------

To run training send GET request to the following endpoint.
::

  curl 127.0.0.1:5000/train/<tag>

The request will return json with field task_id that can be used to get status of training:
::

  curl 127.0.0.1:5000/status/<task_id>

To specify the number of clusters add argument num_clusters to the endpoint (1 by default).

To apply aesthetic model set an argument apply_aesthetic_score=true (disable by default).
  
To apply stock score model set an argument apply_stock_score=true to the endpoint (disable by default).
::

  curl 127.0.0.1:5000/train/<tag>?num_clusters=3&apply_aesthetic_score=true&apply_stock_score=true

Prediction
----------

Prediction from image
^^^^^^^^^^^^^^^^^^^^^

You can use general endpoint to get a prediction for custom models.
::

  curl 127.0.0.1:5000/predict -X POST -F "data=@./your_img.jpg"

Or in python:
::

  def get_predictions(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict', files=data).json()
     return pred

This endpoint will return predictions for base models and for custom models.

Or you can get only prediction for custom models.
::

  curl 127.0.0.1:5000/predict/custom -X POST -F "data=@./your_img.jpg"

Or in python:
::

  def get_custom_predictions(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/custom', files=data).json()
     return pred

Or even get prediction only for one model:
::

  curl 127.0.0.1:5000/predict/custom/<tag> -X POST -F "data=@./your_img.jpg"

Or in python:
::

  def get_custom_predictions(img, tag):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/custom/%s'%tag, files=data).json()
     return pred


Prediction by features
^^^^^^^^^^^^^^^^^^^^^^

You can also do prediction using features which is much faster.

To extract features use the following endpoint.
::

  curl 127.0.0.1:5000/get_features -X POST -F "data=@./your_img.jpg" --output features.json

And you can do it in python as well.
::

  def get_features(img):
     with open(img,'rb') as image:
         data = {'data': image}
         features = requests.post('http://127.0.0.1:5000/get_features', files=data).json()
     return features

You can save extracted features as you want.


To get predictions from custom models by features use the following endpoint.
::

  curl 127.0.0.1:5000/predict_by_features -X POST -F "data=@./features.json"

And python code.
::

  def get_predictions_by_features(features):
     data = {'data': io.StringIO(unicode(json.dumps(features)))}
     pred = requests.post('http://127.0.0.1:5000/predict_by_features', files=data).json()
     return pred

Remove models
-------------

To remove custom model send GET request to the following endpoint.
::

  curl 127.0.0.1:5000/clean/<tag>

Backup and restore state
-------------------------

To get state of the SDK use the following endpoint.
::

  curl 127.0.0.1:5000/get_state --output state.tar

To restore internal state of SDK use the following endpoint.
::

  curl 127.0.0.1:5000/set_state -X POST -F "data=@./state.tar"
