Similarity Search
=================================

Mobius Vision SDK provides a powerful similarity search. Our test API is limited, it only allows to find similar images from unsplash.com.

Similarity search with a query image can be used with the following endpoint:
::

  curl "https://webdemo.mobius.ml/test_api/predict/similarity_search?MOBIUS_KEY=<your_key>" -X POST -F "data=@./your_query_img.jpg"

Or using this call from a python script:
::

  def search(img_path):
      with open(img_path, 'rb') as image:
          data = {'data': image}
          r = requests.post('https://webdemo.mobius.ml/test_api/predict/similarity_search?MOBIUS_KEY=<your_key>', files=data).json()
      return r

The output is the list of two element tuples.
Each tuple contains:

* ID of image on unsplash.com. We do not provide images. In order to get image go to https://unsplash.com/photos/<ID>.
* distances in floating point precision that quantifies the similarity to the most similar images found. Since lower distance implies higher similarity, this list is sorted in ascending order.


Example of an output
::

  {  
    "similarity_search":[  
        [  
          "YKd-nPJaAi8",
          153.32421875
        ],
        [  
          "Yju1DCmp39I",
          169.04718017578125
        ],
        [  
          "cdqK7pL21Rw",
          171.44544982910156
        ],
        [  
          "GEGidRMeSy8",
          173.52816772460938
        ],
        [  
          "RMdZsJeKm70",
          178.44012451171875
        ],
        [  
          "dksE75zlf0o",
          182.58900451660156
        ],
        [  
          "x7VCU6mZDdY",
          183.90789794921875
        ],
        [  
          "DfKZs6DOrw4",
          190.76515197753906
        ],
        [  
          "YV_AvFCCDEY",
          195.00106811523438
        ],
        [  
          "SS_yCeIzwTo",
          198.4951171875
        ]
    ],
    "status":"success"
  }

In the example, the image with ID YKd-nPJaAi8 is the most similar to the query image provided, with a distance of 153.32.
