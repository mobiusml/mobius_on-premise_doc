Customized Training
=================================

In order to use the customisation module for adding keywords and/or styles, it needs to be trained with handpicked data.

The steps required for training are:

* add images
* assign images as positives or negatives to custom model
* run training
* repeat training for fine-tuning in order to improve results
* test trained customisation module using the predict function

.. image::
   data/PA3.png
   :align: center

Adding images
-------------

The first step is to add images to the system by sending a POST request to the following endpoint:
::

  curl 127.0.0.1:5000/system/database/image/add?image_id=<unique_id> -X POST -F "data=@./your_img.jpg"

where image_id is an optional argument. Without this argument, the system will generate a random ID number and return it as a response.

.. note::

  The IDs for image samples should be unique, since they are used internally to identify the images. We recommend either using numbers, or a combination of numbers and letters. They should be passed as a string.

You can also add samples using the following python script:
::

  def add_sample(item):
    img_path = item['path']
    image_id = item['image_id']
    with open(img_path, 'rb') as image:
        data = {'data': image}
        r = requests.post('http://127.0.0.1:5000/system/database/image/add?image_id=%s' % image_id, files=data).json()
    return r

Lastly, here is an example how to use multiprocessing in python to speed things up:
::

  from multiprocessing import Pool
  import requests

  def add_sample(item):
    img_path = item['path']
    image_id = item['image_id']
    with open(img_path, 'rb') as image:
        data = {'data': image}
        r = requests.post('http://127.0.0.1:5000/system/database/image/add?image_id=%s' % image_id, files=data).json()
    return r

  pool = Pool(50)
  images = [{'image_id': '1', 'path': path_to_image1}, {'image_id': '2', 'path': path_to_image2}, ...] #List of image paths
  results = pool.map(add_sample, images)
  pool.close()
  pool.join()

When images are added to the database, the SDK processes them by extracting numerical features according to our pre-trained Mobius Vision keywording model. All processed samples are stored in an auxiliary structure.

.. warning::

  This step is computationally expensive. If the number of images is large (e.g., more than 1 million), it may take more than one day to process all images on a single GPU.

Assigning images
----------------

To assign added image to custom model, send a GET request to the same endpoint:
::

  curl 127.0.0.1:5000/custom/assign/<image_id>/<type>/<tag>

where image_id is ID that you got (or specified) on previous step, type is type of the sample (can be only either 'positive' or 'negative') and tag is name of the custom tag.

In python:
::

  def assign_image(sample_type, tag, image_id):
      url = 'http://127.0.0.1:5000/custom/assign/%s/%s/%s'%(image_id, sample_type, tag)
      r = requests.get(url).json()
      return r


Training
------------

To run training, you can send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/custom/train/<tag>

The following request will return a json file with field task_id that can be used to get status of training:
::

  curl 127.0.0.1:5000/status/<task_id>
  

Resetting a model
------------

To reset a model, you can send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/custom/delete/<tag>



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


Prediction on added image
--------------------------

To get custom models predictions for added image, send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/custom/predict/<image_id>

Or you can get prediction one for one custom model:
::

  curl 127.0.0.1:5000/custom/predict/<tag>/<image_id>

In python:
::

  def get_predictions_added(image_id, tag=None):
      if tag is None:
          pred = requests.get('http://127.0.0.1:5000/custom/predict/%s'%(image_id)).json()
      else:
          pred = requests.get('http://127.0.0.1:5000/custom/predict/%s/%s'%(tag, image_id)).json()
      return pred


Prediction on the batch of added images
---------------------------------------

To get custom models predictions for the batch of added images, send a GET request to the following endpoint:
::

  curl 127.0.0.1:5000/custom/predict_batch?image_id=<list_of_image_ids>&tags=<list_of_tags>


where `list_of_image_ids` is the list of image_id concatenated with comma and `list_of_tags` is the list of custom models concatenated with comma.

In python:
::
  def get_predictions_batch(image_id_list, tag_list):
      pred = session.get('http://127.0.0.1:5000/custom/predict_batch?tags=%s&image_id=%s'%
                        (','.join(tag_list), ','.join(image_id_list))).json()
      return pred

Load a pretrained custom Model
------------------------------

You can load a pretrained custom model by calling the following endpoint:
::

  curl 127.0.0.1:5000/set_state -X POST -F "data=@./state.tar"
