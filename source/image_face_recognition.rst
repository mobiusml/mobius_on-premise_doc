Face recognition
================

Face recognition can be used to get:

* locations of the faces,
* age estimate for each detected face,
* gender estimate for each detected face,
* recognize the persons that are added to the database.

The face recognition database allows to tag the persons on images and videos. Multiple databases can be created and used by specifying `group_id`. The database with more that 11 000 celebrities can be provided by request.

New faces can be registered by providing image with face and person name (or person ID). More on how to create and modify face recognition databases can by found in `Face recognition database`_.

Prediction
----------

To run face recognition following endpoint can be used.
::

  curl 127.0.0.1:5000/predict/faces?group_id=<group_id> -X POST -F "data=@./your_image.jpg"

where `group_id` is optional argument that specify a database to use for face identification. If `group_id` is not specified default `group_id` will be used (`group_id=default`). It is possible to do search in multiple databases in one call by providing the list of group IDs into an argument `group_id`. For example, `group_id=test,default`.

You can call the endpoint from python
::
  group_id = <group_id>

  def get_faces(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/faces?group_id=%s'%(group_id), files=data).json()
     return pred


Face recognition database
-------------------------

In order to identify faces the face recognition database should be created.

There are few restriction on what images can be used for populating face database:

* image should contain only one face,
* face should be at least 112x112 pixels.

Also we recommend to select images where:

* face is looking straight at the camera (small deviations are fine),
* face is not obstructed by hands, sunglasses or other objects (glasses where eyes are visible should be fine, but if possible select a photo without),
* lighting is uniform,
* face is not blurry.


To add images to face recognition database, you can send a POST request to the following endpoint:
::

  curl 127.0.0.1:5000/faces/add?image_id=<image_id>&person_id=<person_id>&group_id=<group_id> -X POST -F "data=@./your_img.jpg"
  
where

* `image_id` is an optional argument that identify added image in the system. Without this argument, the system will generate a random ID number and return it as a response.
* `person_id` is optional argument that specify a string that will be used to identify person. If `person_id` is not provided random string will be used as person_id. 
* `group_id` is optional argument that specify a database to use for face identification. If `group_id` is not specified default `group_id` will be used (`group_id=default`).


In python:
::
  def add_face(img, person_id, group_id='default', image_id=None):
      with open(img,'rb') as image:
          data = {'data': image}
          url = 'http://127.0.0.1:5000/faces/add?group_id=%s&person_id=%s'(group_id, person_id)
          if image_id:
              url += '&image_id=' + image_id
          r = requests.post(url, files=data).json()
      return r


There are few ways to delete items from face recognition databases:

* deleting image using image ID,
* deleting person using person ID,
* deleting face recognition database using group ID.


To delete image:
::

  curl 127.0.0.1:5000/faces/remove/<image_id>?group_id=<group_id>

where

* `image_id` is ID that was provided or returned when faces was added,
* `group_id` is optional argument that specify from which database to remove an image. If `group_id` is not specified default `group_id` will be used (`group_id=default`).


To delete person:
::

  curl 127.0.0.1:5000/faces/remove_person/<person_id>?group_id=<group_id>

where

* `person_id` is ID of the person that was provided or returned when faces was added,
* `group_id` is optional argument that specify from which database to remove a person. If `group_id` is not specified default `group_id` will be used (`group_id=default`).


To delete face recognition database:
::

  curl 127.0.0.1:5000/faces/remove_group/<group_id>

where

* `group_id` specifies a database that need to be removed.


