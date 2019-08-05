.. _installation-label:

Getting started
==================

All you need in order to use our test API is the Mobius Key. To get the key please contact our sales team by sending an e-mail to sales@mobius.ml.

The Mobius Key
--------------

The Mobius Key has to be passed as an argument by adding a `?MOBIUS_KEY=<your_key>` after a command.

For example:
::

  curl https://webdemo.mobius.ml/test_api/predict/concepts?MOBIUS_KEY=<your_key>
        -X POST -F "data=@./your_image.jpg"


In case that an error message is returned, please visit the troubleshooting section.
