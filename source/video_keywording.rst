Video Tagging
==============

The video tags module has been trained on our large in-house image dataset to provide state-of-the-art results for predictions with our 10000 standard concepts and identities matched with faces in our Mobius core face database or custom face groups.

Getting started
---------------

In the following, we show an example of how to do prediction of tags and identities on videos.

First, we need to send the video to the (local) server for analysis.
::

  curl 127.0.0.1:5000/video/tag -X POST -F "data=@./your_video.mp4"

The above command will return an information message:
::

  {"message":"video_tagging","status":"ongoing","task_id":"599600ef-817f-413e-85f5-d4fc55313164"}

The **task_id** will be required in the next step.

Depending on various factors, including duration and resolution of the video, but also the type of tags model selected (|lightweight_model| or |performance_model|), the time until the tagging is finished will vary.
With the following command, you can check the status of the process at any time.
::

  curl 127.0.0.1:5000/status/<task_id>

where you would replace <task_id> with 599600ef-817f-413e-85f5-d4fc55313164 in the example above.

If the video is still being processed, the status message will be:
::

  {"status": "ongoing", "message": "video_tagging"}

When the processing is finished, the status message will contain metadata of the input file and prediction results:
::

    {"video_level":
    {"concepts": [
    ["conceptual", [["togetherness", 1.0], ["beauty in nature", 1.0], ["silhouette", 1.0],...]],
    ["nature", [["sky", 1.0], ["sea", 1.0], ["beach", 1.0],... ]],
    ["people", [["adult", 1.0], ["two people", 1.0], ["women", 1.0],... ]],
    ["photographic", [["full length", 1.0], ["rear view", 0.4], ["side view", 0.4],... ]],
    ["animals", [["animal", 0.4]]],...],
    "identities": {"default": []}},
    "status": "success",

    "segment_level":
    [{"identities": {"default": []}, "timestamp": [0.0, 4.0],
     "concepts": [
    ["conceptual", [["togetherness", 0.9454852938652039], ["beauty in nature", 0.9428154826164246 ], ...]],
    ["nature", [["beach", 0.9955477714538574], ["sky", 0.9953177571296692], ["sea", 0.9952855706214905],...]],
    ["people", [["adult", 0.9553800821304321], ["one person", 0.9429706931114197],...]],...]},

    {"identities": {"default": []}, "timestamp": [2.6666666666666665, 4.0],
    "concepts": [
    ["conceptual", [["togetherness", 0.9402145147323608], ["silhouette", 0.8901964426040649], ...]],
    ["nature", [["beach", 0.9955477714538574], ["sea", 0.9937513470649719], ["sky", 0.9925636053085327], ...]],
    ["people", [["two people", 0.9449461102485657], ["adult", 0.9440362453460693], ["women", 0.9152339696884155],...]],...]},

     "meta_information": {"height": 1080, "width": 1920, "duration": 7.273933, "size": 3.8400964736938477, "fps": 29.97002997002997},
    "warnings": []}

For the prediction of standard concepts, all keywords with a confidence above a certain threshold are returned (0.5 by default) in lists for 13 categories that are pre-defined. Using the categories structures the output to be easier for humans to comprehend as the total standard list of concepts is more than 10 thousand for the latest SDK version.


.. note::

    Depending on the complexity of a video shot, the number of concepts returned will vary. In addition, in case the shot
    detector in disabled, the results might also be of lower quality in cases where a segment contains different shots (and hence, potentially very different concepts).


Results of face recognition are returned as a list called *identities*.

.. note::

    In order to match a face in a frame to faces in a database, the face has to be first detected and located in the frame. This might fail if the face is obscured partially or at an extreme angle. Best detection results can be obtained when the face is facing the camera. For the step of matching the face to the database, no match might be made if the facial landmarks are obscured by sunglasses, hats or other accessiors or if the face is partially too dark. Identities can be only returned when a face is successfully located and the machine is confident that the facial landmarks are a match to the sample in the database.


Arguments
----------


Depending on the features that have been packaged into the SDK, there are a number of arguments that can be passed. The arguments can be passed by adding a "?" after the tag command, followed by the argument=value. Several arguments are separated using the "&". The following example illustrates this:

::

  curl 127.0.0.1:5000/video/tag?keyword_threshold=0.6 -X POST -F "data=@./your_video.mp4"

Below is list of the different arguments that can be set, together with their default values.



**Keyword Tagging**

If you have selected the keyword tagging feature, the following arguments can be set:

* *num_fps* (int, default: *3*): (Integer) Number of frames per second that are to be extracted and analyzed. This value should be increased for fast changing content. Doubling this value roughly doubles the processing time of our SDK.
* *tag_keywords* (default: *true*): Flag to signal if keywording tags should be returned.
* *keyword_threshold* (default: *0.5*): Threshold on the confidence of the keyword predictions. When a lower threshold is set, more tags will be returned but the machine has a lower confidence that they are correct.
* *keyword_topk* (default: *50*): Maximum number of keywords to be returned *per video shot*.
* *tag_faces* (default: true): Flag for the SDK if face recognition should be used and detected labels for faces should be returned in a separate category of tags called *identities*.
* *group_id* (default: `default`): An optional argument that specifies a database to use for face identification. The standard database of face landmark information and corresponding labels of a selection of more than 10 thousand celebrities that can be provided by Mobius is called *mobius_core*.



**Segment-level and Video-level Tagging**

|mobvis_video| offers both segment-level as well as video-level tagging of videos, whose default values depend on whether the **shot detection feature** has been selected. The arguments are:

* *video_level_tags* (default *true*)
* *shot_level_tags* (default *true* if **shot detection has been selected**, *false* otherwise)


Furthermore, an optional argument can be used to specify a fixed video tagging interval. This can be useful in case the shot detection feature has not been selected, but the content is still changing over time.

* *fixed_segment_length* (default *3* seconds)

.. note::

    If *fixed_segment_length* is set, the shot detector is disabled. This means that the video will be analysed in segments of an equal length. In case of drastic scene changes there is a chance that the results will be averaged (pooled) over the scene change hence important concepts might disappear from the results of this segment.


Thumbnails
----------

There is option to keep thumbnails for segments and subsegments. To enable the thumbnails set the argument `thumbnails_enabled` to `True` (`False` by default).

To get a thumbnail for segment use the following command:
::

  curl 127.0.0.1:5000/video/segment/<task_id>/<segment_id>

where segment_id is the index of the segment from predictions (counting from 0).


To get a thumbnail for subsegment use the following command:
::

  curl 127.0.0.1:5000/video/subsegment/<task_id>/<subsegment_id>

where subsegment_id is the index of the subsegment from predictions (counting from 0).



Prediction in Python
---------------------

The code snipped below shows how prediction can be done in Python.

::

    import time

    def analyze_video(video_path):
         with open(video_path,'rb') as video:
             data = {'data': video}
             res = requests.post('http://127.0.0.1:5000/video/tag', files=data).json()
             task_id = res['task_id']
             msg = requests.get('http://127.0.0.1:5000/status/' + task_id).json()

             while(msg['status'] is 'ongoing'):
                 msg = requests.get('http://127.0.0.1:5000/status/' + task_id).json()
                 time.sleep(1.0)

             if(msg['status'] == 'success'):
                pred = msg['result']
             else:
                pred = msg['status']

        return pred
