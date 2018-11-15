Video Tagging
==============
The video tagging module has been trained on our large in-house image dataset to provide state-of-the-art keywording results. Depending on the licence, it consists of a video shot detector, a keywording module for static concepts and emotions, as well as an action recognition module.

Getting started
---------------

In the following, we show an example of how to do prediction of keywords and/or actions on videos. In the example, both the keywording and the action recognition feature are enabled. If you only have either one of the tagging features, you can still run this code, but your output will be different.

First, we need to send the video to the (local) vision server for analysis.
::

  curl 127.0.0.1:5000/video/tag -X POST -F "data=@./your_video.mp4"

The above command will return an information message:
::

  {"message":"video_tagging","status":"ongoing","task_id":"599600ef-817f-413e-85f5-d4fc55313164"}

The **task_id** will be required in the next step.
  
Depending on various factors, including duration and resolution of the video, but also the type of keywording model selected (|lightweight_model| or |performance_model|), the time until the keywording is finished will vary. 
With the following command, you can check the status of the process at any time. 
::
  
  curl 127.0.0.1:5000/status/<task_id>
  
where you would replace <task_id> with 599600ef-817f-413e-85f5-d4fc55313164 in the example above. 

If the video is still being processed, the status message will be:
::
  
  {"status": "ongoing", "message": "video_tagging"}
  
When the processing is finished, the status message will contain the keywording results (static and/or actions, depending on your licence):
::
  
  {"status": "success", "result": [{"keywords": [["people", 0.9846186637878418], 
  ["adult", 0.7601927518844604], ["indoors", 0.717658519744873], ...], 
  "time_stamps": [0.0, 19.333333333333332], 
  "actions": [["painting", 0.8850449323654175]]}]}

All keywords with a confidence above a certain threshold are returned (0.5 by default).

.. note::
    
    Depending on the complexity of a video shot, the number of keywords returned will vary. In addition, in case the shot
    detector in disabled, the results might also be of lower quality in cases where a segment contains different shots (and hence, potentially very different concepts). 
    
    

Arguments
----------

Depending on the features that have been bought, there are a number of arguments that can be passed. The arguments can be passed by adding a "?" after the tag command, followed by the argument=value. Several arguments are separated using the "&". The following example illustrates this:
::
  
  curl 127.0.0.1:5000/video/tag?keyword_threshold=0.6&action_threshold=0.7 -X POST -F "data=@./your_video.mp4"
  
Below is list of the different arguments that can be set, together with their default values.

**Keyword Tagging**

If you have bought the keyword tagging feature, the following arguments can be set:

* *tag_keywords* (default *true*): Flag to signal if keywording tags should be returned
* *keyword_threshold* (default *0.5*): Threshold on the confidence of the keyword predictions
* *keyword_topk* (default *50*): Maximum number of keywords to be returned *per video shot*

**Action Tagging**

If you have bought the action tagging feature, the following arguments can be set:

* *tag_actions* (default *true*): Flag to signal if action tags should be returned
* *action_threshold* (default *0.6*): Threshold on the confidence of action predictions
* *action_topk* (default *1*): Maximum number of actions to be returned *per video shot*

**Segment-level and Video-level Tagging**

|mobvis_video| offers both segment-level as well as video-level tagging of videos, whose default values depend on whether the **shot detection feature** has been bought. The arguments are:

* *video_level_tags* (default *true*)
* *shot_level_tags* (default *true* if **shot detection has been bought**, *false* otherwise)

Furthermore, an optional argument can be used to specify a fixed video tagging interval. This can be useful in case the shot detection feature has not been bought, but the content is still changing over time.

* *fixed_segment_length_in_secs* (default *3*)

.. note::
  
  If *fixed_segment_length_in_secs* is set, the shot detector is disabled.


Error messages
---------------

If there is an error on one of the input arguments, the following status message will be returned:
::
  
  {'status': 'error', 'message': 'wrong_arguments_format', 'arg': 'keyword_topk'}
  
If the video format is not supported, the status message is:
::
  
  {'status': 'error', 'message': 'video_reading_error'}
  

Prediction in Python
---------------------

The code snipped below shows how prediction can be done in Python.

::
    #import time
    
    def analyze_video(video_path):
         with open(video_path,'rb') as video:
             data = {'data': video}
             res = requests.post('http://127.0.0.1:5000/video/tag', files=data).json()
             task_id = res['task_id']
             msg = requests.get('http://127.0.0.1:5000/status/' + task_id).json()
             
             while(msg['status'] is 'ongoing'):
                 msg = requests.get('http://127.0.0.1:5000/status/' + task_id).json()
                 #time.sleep(1.0)
                 
             if(msg['status'] == 'success'):
                pred = msg['result']
             else:
                pred = msg['status']
        
        return pred





  
