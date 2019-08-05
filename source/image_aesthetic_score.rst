Aesthetics
===========

Prediction with the aesthetics module works in a similar way to the keywording module.
That is, the aesthetic score can be obtained by issuing the following command:
::

  curl "https://webdemo.mobius.ml/test_api/predict/aesthetic?MOBIUS_KEY=<your_key>" -X POST -F "data=@./your_image.jpg"

The score is a floating point value between 0 and 1, where a score close to zero indicates
that the aesthetics of the image are poor while a score close to 1 indicates that the aesthetics are very good.

Or in python
::

  def get_aesthetic(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('https://webdemo.mobius.ml/test_api/predict/aesthetic?MOBIUS_KEY=<your_key>', files=data).json()
     return pred

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
  results = pool.map(get_aesthetic, images)
  pool.close()
  pool.join()
