Face recognition
================

Face recognition can be used to get:

* face detection (bounding boxes) of the faces;
* age estimate for all detected faces;
* gender estimate for all detected faces;
* person identification (if the person is added to the database with a sample image).

The face recognition database allows to tag the persons on images and videos. Multiple databases can be created and used by specifying a `group_id`. A pre-populated database with more than 11 000 celebrities can be provided by request.

New faces can be registered by providing an image showing the face of the person and the person name (or person ID). More information on how face recognition databases can be created and modified can be found in `Face recognition database`_.

Prediction
----------

To run face recognition, the following endpoint can be used.
::

  curl 127.0.0.1:5000/predict/faces?group_id=<group_id> -X POST -F "data=@./your_image.jpg",

where `group_id` is an optional argument that specifies a database to use for face identification. If `group_id` is not specified, the default `group_id` will be used (`group_id=default`). It is possible to do search in multiple databases in one call by providing the list of group IDs into an argument `group_id`. For example, `group_id=test,default` would search for matching faces in both the `default` and the `test` database.

A sample output can be found here:
::

  {"faces":[{"age":39,"box":[152,47,1034,1196],"gender":"male","person":{"id":"Leo Varadkar","score":0.8864131314413889}}],"status":"success"}

The face recognition endpoint can be called from python as follows:
::
  group_id = <group_id>

  def get_faces(img):
     with open(img,'rb') as image:
         data = {'data': image}
         pred = requests.post('http://127.0.0.1:5000/predict/faces?group_id=%s'%(group_id), files=data).json()
     return pred


Face recognition database
-------------------------

In order to identify faces, a face recognition database has to be created.

There are a few restrictions on what images can be used for populating the face database:

* the image should contain only one face;
* the size of the face should be at least 112x112 pixels.

We further recommend to select images where:

* the face is looking straight at the camera (small deviations are fine);
* the face is not obstructed by hands, sunglasses or other objects (glasses where eyes are visible should be fine, but if possible select a photo without);
* the lighting is uniform;
* the face is not blurry.


To add images to the face recognition database, you can send a POST request to the following endpoint:
::

  curl 127.0.0.1:5000/faces/add?image_id=<image_id>&person_id=<person_id>&group_id=<group_id> -X POST -F "data=@./your_img.jpg"
  
where

* `image_id` is an optional argument that is used to identify the added image in the system. Without this argument, the system will generate a random ID number and return it as a response;
* `person_id` is an optional argument that specifies a string that will be used to identify the person. If `person_id` is not provided, a random string will be used as person_id;
* `group_id` is an optional argument that specifies the database where the face will be added to. If `group_id` is not specified, the default `group_id` will be used (`group_id=default`).


Below is a python code snippet that can be used to add faces to a face database: 
::
  def add_face(img, person_id, group_id='default', image_id=None):
      with open(img,'rb') as image:
          data = {'data': image}
          url = 'http://127.0.0.1:5000/faces/add?group_id=%s&person_id=%s'(group_id, person_id)
          if image_id:
              url += '&image_id=' + image_id
          r = requests.post(url, files=data).json()
      return r


There are a few ways to delete items from a face recognition database:

* deleting image using image ID;
* deleting person using person ID (this removes all information stored for that person);
* deleting a whole face recognition database using a group ID (this removes all persons stored in the specified group);


To delete an image, the following endpoint can be called:
::

  curl 127.0.0.1:5000/faces/remove/<image_id>?group_id=<group_id>

where

* `image_id` is the ID that was provided or returned when the face was added;
* `group_id` is an optional argument that specifies the database from which the image is to be removed. If `group_id` is not specified, the default `group_id` will be used (`group_id=default`).


To delete a person:
::

  curl 127.0.0.1:5000/faces/remove_person/<person_id>?group_id=<group_id>

where

* `person_id` is the ID of the person that was provided or returned when faces was added;
* `group_id` is an optional argument that specifies the database from which the person is to be removed. If `group_id` is not specified, the default `group_id` will be used (`group_id=default`).


To delete a face recognition database:
::

  curl 127.0.0.1:5000/faces/remove_group/<group_id>

where

* `group_id` specifies the face database that is to be removed.


