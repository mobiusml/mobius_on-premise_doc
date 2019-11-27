Status Messages
=================================

The SDK is designed to return status messages to the user.
This section is an inventory of all possible status messages and recommended actions.
Status messages contain two main fields: status and message.
The status can be either 'ok', 'error' or 'ongoing' to indicate the status of the processing.
Messages are generally shown in the cases of errors or ongoing processing only.


System set-up
----------------

.. list-table:: Status messages in the context of system set-up
   :widths: 25 25 50
   :header-rows: 1

   * - Status message
     - Possible causes
     - Recommended solution
   * - invalid_signature
     - | Provided signature does not match to signature on token server.
     - | Please check your token or contact us.
   * - token_server_connection_error
     - | System can't connect to token verification server.
       | Can happen if the Mobius Labs token server is offline.
     - | Please check internet connection from inside docker
       | or contact us if problem persists.
   * - token_verification_error
     - | Unexpected error happened in process of token verification
     - | Please check your token and the connection
       | to the internet from within the docker container.
   * - expired_token
     - | Provided token is expired.
     - | Please contact us.
   * - unknown_token
     - | Token is not registered in our token verification system
     - | Please check your token.


General use of SDK
-----------------------

.. list-table:: Status messages in general context of using the SDK
   :widths: 25 25 50
   :header-rows: 1

   * - Status message
     - Possible causes
     - Recommended solution
   * - exceeds_test_limit
     - | Can only appear for test builds of the SDK with restriction
       | on the number of images that can be processed.
     - | Please contact us.
   * - image_reading_error
     - | Various possible causes. SDK can't decode image.
       | Could be corrupted image, not supported image format.
       | Could be a problem with the library OpenCV/pil.
     - | The on-premise SDK supports jpg and png image formats.
       | Another possible solution is to verify that the image is not corrupted.
   * - video_reading_error
     - | Video codec/extension is not supported.
     - | Our SDK uses ffmpeg as backbone, so please make sure that your video is supported by ffmpeg.
   * - data_payload_required
     - | No data was provided or send request but no input data field was found.
     - | Please follow instructions from the documentation how to fill in data.
   * - features_loading_error
     - | The system can't load features or load features from disk for training.
       | Could be caused by having no read rights or file system issues.
     - | Verify location of features and that read access is given.
   * - invalid_features
     - | Provided features for prediction are invalid.
     - | Please follow instructions for predicting on features
       | from the documentation. Verify correctness of storing and passing features.
   * - training
     - | Training is going on (similarity search or custom model training).
       | This is the message for status 'ongoing'.
     - | Please wait for training to complete.
   * - wrong_arguments_format
     - | Arguments passed to the video tagging are not in expected format. 
       | Will pass the argument that caused the error to be thrown. 
     - | Please check the arguments in the video tagging call. 
   * - file_saving_error
     - | The SDK can't save a (custom model or other features) file or
       | extract features. Could be caused by problem with file system or data directory in docker.
     - | Please verify that write access is given.
   * - unknown_task_id
     - | Provided task_id is not registered in the system.
       | Could be caused by passing wrong task ID.
     - | Please verify the correctness of the task ID.
   * - unknown_error
     - | Something unexpected happened. Catch-all error message.
     - | Please send us the traceback and exception fields of the response.


Similarity search feature
-----------------------------

.. list-table:: Status messages that only appear when using similarity search
   :widths: 25 25 50
   :header-rows: 1

   * - Status message
     - Possible causes
     - Recommended solution
   * - index_loading_error
     - | Similarity search module can't load search approximator.
       | Can happen when user has not trained the index.
       | Also when no images have been added to the index. It can be problem with file system.
     - | Please use proper set-up for adding images as explained in the documentation
       | and check file system access.
   * - more_samples_required
     - | This feature needs at least 1000 samples to train similarity search
       | in the initial training round.
     - | Please add more images prior to training.
   * - updating
     - | The updating process is going on.
     - | Please wait
   * - duplicate_image_id
     - | Provided image ID is already in use. Can happen if the same image
       | is passed to the add function multiple times.
     - | Please check the image IDs for uniqueness and remove duplicates.


Custom model training
------------------------

.. list-table:: Status messages that only appear when using custom model training
   :widths: 25 25 50
   :header-rows: 1

   * - Status message
     - Possible causes
     - Recommended solution
   * - unknown_custom_model
     - | Requested custom model does not exist.
     - | Please make sure the correct tag is passed.
       | It can be also a solution to predict with all custom models or all models.
   * - positive_samples_required
     - | The SDK has not been passed any positive samples.
       | It is required for training to have positive samples.
     - | Please add positive samples.
   * - training_error
     - | Multiple possible causes. Fallback error to prevent exceptions
     - | Please send us the traceback and exception fields of the response.
   * - duplicate_image_id
     - | Provided image ID is already in use. Can happen if the same image
       | is passed to the add function multiple times.
     - | Please check the image IDs for uniqueness and remove duplicates.
     
     

Face recognition
------------------------

.. list-table:: Status messages that only appear when using face recognition
   :widths: 25 25 50
   :header-rows: 1

   * - Status message
     - Possible causes
     - Recommended solution
   * - no_faces_detected
     - | No faces are detected on provided image.
     - | Provide another image.
   * - more_than_one_face_detected
     - | More than one face are detected.
     - | Provide another image that contains exactly one face.
   * - face_too_small
     - | Detected face is too small on provided image.
     - | Provide another image or same image in higher resolution.
   * - image_id_not_found
     - | Provided image ID is not found.
     - | Please check the image ID.
   * - person_id_not_found
     - | Provided person ID is not found.
     - | Please check the person ID.
   * - group_id_not_found
     - | Provided group ID is not found.
     - | Please check the group ID.
   * - duplicate_image_id
     - | Provided image ID is already in use. Can happen if the same image
       | is passed to the add function multiple times.
     - | Please check the image IDs for uniqueness and remove duplicates.
