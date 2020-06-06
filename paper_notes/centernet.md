# [Objects as points](https://arxiv.org/pdf/1904.07850.pdf)

_May 2019_

tl;dr: Object detection as detection of the center point of the object and regression of its associated properties. CenterNet is the first real time anchor-free object detector.

#### Overall impression
CenterNet is a very generic object detection framework that can be used for 2D object detection, 3d object detection (from monocular RGB image), key point regression. The backbone can be chosen to meet different speed/accuracy tradeoff points.

#### Key ideas
- Other properties, such as object size, dimension, 3D extent, orientation, and pose are regressed directly from image features at the center location.
- Runs at real time:
	- ResNet-18 and up-conv layers: 28 AP with 142 FPS
	- DLA-34: 37 AP with 52 FPS (best speed/accuracy tradeoff)
	- Hourglass-104: 45 AP with 1.4 FPS
- High res output: stride=4.
- GT: center/keypoints with Gaussian kernel blur
- 2D bbox prediction: C ch for class-specific heatmaps, 2 ch for offset (only at center to remove discretization error), 2 ch for size
- Decoding points to bbox: find top 100 peaks from heatmaps, then apply offset prediction and size prediction. There is no need for NMS. 

#### Technical details
- Changing the proposal classifier to a multi-class classification forms the basis of one-stage detectors.
- For 3D bbox detection, it needs quite some engineering effort to make it work.
- Depth is hard to regress so they adopted a *scale-invariant mean squared error (in log space)*, $D(y, y') = \sum_i (\log y_i - \log y_i' - \sum_i (\log y_i - \log y_i'))^2 $. All scalar multiples of y have the same error, hence the scale invariance. (However the formulation in CenterNet is really hard to digest.)
- In human pose estimation, simply regress the offset to k key points does not yield SOTA. Generate heatmap for each keypoint and snap regressed key points to heatmap peaks yields much better results. In other words, center offset is just used as a grouping cue. 
- Hourglass network is quite large, but yields SOTA for key point regression.
- It takes a few days to converge. It seems pretty hard to train. 
- TridentNet has the SOTA for two stage object detection. 48.4 AP, but also very slow (Mask RCNN is 15x faster).

#### Notes
- Blog post from [知乎](https://www.zhihu.com/question/321206428) to compare two CenterNet papers.

