Keywording
==========

The *pre-trained* image keywording module can be used to obtain predictions for a wide range of applications.

This is an example for prediction:
::

  curl "https://webdemo.mobius.ml/test_api/predict/concepts?MOBIUS_KEY=<your_key>" -X POST -F "data=@./your_image.jpg"

You can call the endpoint from python
::

  def get_keywords(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('https://webdemo.mobius.ml/test_api/predict/concepts?MOBIUS_KEY=<your_key>', files=data).json()
     return pred

.. note::

    Depending of the image, the number of returned keywords might vary in this mode.

All keywords with a confidence above a certain threshold are returned.
For some simple images, the keywording module might only recognise a small number of matching keywords.
However, in cluttered scenes, there might be a long list of matching keywords.

There is an additional request argument `top_k` to obtain the highest scored `k` keywords.
In this mode, the number of keywords is fixed then.
::

  curl "https://webdemo.mobius.ml/test_api/predict/concepts?top_k=10&MOBIUS_KEY=<your_key>" -X POST -F "data=@./your_image.jpg"


Prediction with large number of images
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Please note that prediction calls are time consuming. It's recommended to run predictions
in parallel.

Here is an example how to use multiprocessing in python to speed things up:

::

  from multiprocessing import Pool
  import requests

  pool = Pool(50)
  images = [path_to_image1, path_to_image2, ...] #List of image paths
  results = pool.map(get_keywords, images)
  pool.close()
  pool.join()
