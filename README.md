# SUN XR: "Open-Vocabulary Object Detection" Component

This component is a part of the [SUN XR](https://sun-xr-project.eu) project.

## Requirements

- torch & torchvision
- stuff in `requirements.txt`

Tested with:
 - Python 3.10.12
 - torch==2.0.1+cu117
 - torchvision==0.15.2+cu117
 - transformers==4.38.2
 - Flask==3.0.2

## Getting Started

Start the server (default port is 5000):
```bash
python server.py
```

The API endpoint is `/detect`. It accepts a POST request with the following parameters:
- `image`: a file with the image to process
- `vocabulary`: a list of strings with the vocabulary to search for in the image

Optional parameters:
- `prompt`: (string) the prompt to use for the image, defaults to "A photo of a"
- `max_predictions`: (integer) the maximum number of predictions to return, defaults to 10
- `score_thresh`: (float) the minimum score for a prediction to be returned, defaults to 0.05
- `nms_tresh`: (float) the IoU threshold for non-maximum suppression, defaults to 0.5


Test it:
```bash
# Download a test image
wget http://lenna.org/len_std.jpg

# Send a request, save results to json file
curl \
    -X POST \
    -F "image=@len_std.jpg" \
    -F "vocabulary=eye" \
    -F "vocabulary=hat" \
    -F "vocabulary=scarf" \
    -o results.json \
    http://localhost:5000/detect

# visualize the results (output.jpg)
python visualize.py len_std.jpg results.json
```

## Output Example
```python
{
  # A list of bounding boxes. Each box is in the format [x0, y0, x1, y1], where:
  #   x0: x-coordinate of the top left point of the box
  #   y0: y-coordinate of the top left point of the box
  #   x1: x-coordinate of the bottom right point of the box
  #   y1: y-coordinate of the bottom right point of the box
  # Coordinates are normalized in [0,1].
  "boxes": [
    [
      0.21327996253967285,
      0.08150815963745117,
      0.8104771375656128,
      0.7465566396713257
    ],
    [
      0.6140727400779724,
      0.4936254620552063,
      0.6885564923286438,
      0.5539261698722839
    ],
    [
      0.48045575618743896,
      0.48640480637550354,
      0.5716360807418823,
      0.5525424480438232
    ],
    [
      0.8094183206558228,
      0.5225362777709961,
      1.0027570724487305,
      0.9810501337051392
    ],
    [
      0.12232333421707153,
      0.09066975116729736,
      0.7587239742279053,
      0.9983150959014893
    ]
  ],

  # Model inference time in seconds
  "inference_time_s": 0.7302215099334717,

  # Detected class for each box. These are 0-based indices in the "vocabulary" list.
  "labels": [
    1,
    0,
    0,
    1,
    2
  ],

  # Confidence score in [0,1] for each box.
  "scores": [
    0.46943578124046326,
    0.19213418662548065,
    0.1765633225440979,
    0.15089093148708344,
    0.053104571998119354
  ],

  # The list of detectable classes (i.e., the input vocabulary).
  "vocabulary": [
    "eye",
    "hat",
    "scarf"
  ]
}
```
