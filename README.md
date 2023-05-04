Download Link: https://assignmentchef.com/product/solved-csci5561-homework-2-registration
<br>
<h1>VLFeat Installation</h1>

Figure 1: Given an image (a), you will extract SIFT features using VLFeat.

One of key skills to learn in computer vision is the ability to use other open source code, which allow you not to re-invent the wheel. We will use VLFeat by A. Vedaldi and B. Fulkerson (2008) for SIFT extraction given your images. Install VLFeat from following:

<a href="http://www.vlfeat.org/install-matlab.html">http://www.vlfeat.org/install-matlab.html</a>

Run vl demo sift basic to double check the installation is completed.

(Note) You will use this library only for SIFT feature extraction and its visualization. All following visualizations and algorithms must be done by your code. Using VLFeat, you can extract keypoints and associated descriptors as shown in Figure 1.

(SIFT visualization) Use VLFeat to visualize SIFT features with scale and orientation as shown in Figure 1. You may want to follow the following tutorial:

<a href="http://www.vlfeat.org/overview/sift.html">http://www.vlfeat.org/overview/sift.html</a>

<h1>2             SIFT Feature Matching</h1>

Figure 2: You will match points between the template and target image using SIFT features.

The SIFT is composed of scale, orientation, and 128 dimensional local feature descriptor (integer), <strong>f </strong>∈ Z<sup>128</sup>. You will use the SIFT features to match between two images, <em>I</em><sub>1 </sub>and <em>I</em><sub>2</sub>. Use two sets of descriptors from the template and target, find the matches using nearest neighbor with the ratio test. You may use knnsearch built-in function in MATLAB.

function [x1, x2] = FindMatch(I1, I2)

<strong>Input: </strong>two input gray-scale images with uint8 format.

<strong>Output: </strong>x1 and x2 are <em>n </em>× 2 matrices that specify the correspondence.

<strong>Description: </strong>Each row of x1 and x2 contains the (<em>x,y</em>) coordinate of the point correspondence in <em>I</em><sub>1 </sub>ad <em>I</em><sub>2</sub>, respectively, i.e., x1(i,:) &#x2194; x2(i,:).

(Note) You can only use VLFeat for the SIFT descriptor extraction. Matching with the ratio test needs to be implemented by yourself.

<h1>3             Feature-based Image Alignment</h1>

Figure 3: You will compute an affine transform using SIFT matches filtered by RANSAC. Blue: outliers; Orange: inliers; Red: the boundary of the transformed template.

(Note) From this point, you cannot use any function provided by VLFeat.

The noisy SIFT matches can be filtered by RANSAC with an affine transformation as shown in Figure 3.

function [A] = AlignImageUsingFeature(x1, x2, ransac_thr, ransac_iter) <strong>Input: </strong>x1 and x2 are the correspondence sets (<em>n </em>× 2 matrices). ransac_thr and ransac_iter are the error threshold and the number of iterations for RANSAC. <strong>Output: </strong>3 × 3 affine transformation.

<strong>Description: </strong>The affine transform will transform <em>x</em><sub>1 </sub>to <em>x</em><sub>2</sub>, i.e., <em>x</em><sub>2 </sub>= <em>Ax</em><sub>1</sub>. You may visualize the inliers and the boundary of the transformed template to validate your implementation.

<h1>4             Image Warping</h1>

Figure 4: You will use the affine transform to warp the target image to the template using the inverse mapping. Using the warped image, the error map |<em>I</em><sub>tpl </sub>− <em>I</em><sub>wrp</sub>| can be computed to validate the correctness of the transformation where <em>I</em><sub>tpl </sub>and <em>I</em><sub>wrp </sub>are the template and warped images.

Given an affine transform <em>A</em>, you will write a code to warp an image <em>I</em>(<em>x</em>) → <em>I</em>(<em>Ax</em>).

function [I_warped] = WarpImage(I, A, output_size)

<strong>Input: </strong>I is an image to warp, A is the affine transformation from the original coordinate to the warped coordinate, output_size=[h,w] is the size of the warped image where w and h are the width and height of the warped image.

<strong>Output: </strong>I_warped is the warped image with the size of output_size.

<strong>Description: </strong>The inverse mapping method needs to be applied to make sure the warped image does not produce empty pixel. You are allowed to use interp2 build-in function in MATLAB for bilinear interpolation.

(Validation) Using the warped image, the error map |<em>I</em><sub>tpl </sub>− <em>I</em><sub>wrp</sub>| can be computed to validate the correctness of the transformation where <em>I</em><sub>tpl </sub>and <em>I</em><sub>wrp </sub>are the template and warped images.

<h1>5             Inverse Compositional Image Alignment</h1>

Figure 5: You will use the initial estimate of the affine transform to align (i.e., track) next image. (a) Template image from the first frame image. (b) The second frame image with the initialization of the affine transform. (c) The second frame image with the optimized affine transform using the inverse compositional image alignment.

Given the initial estimate of the affine transform A from the feature based image alignment (Section 4) as shown in Figure 5(b), you will track the next frame image using the inverse compositional method (Figure 5(c)). You will parametrize the affine transform with 6 parameters <em>p </em>= (<em>p</em><sub>1</sub><em>,p</em><sub>2</sub><em>,p</em><sub>3</sub><em>,p</em><sub>4</sub><em>,p</em><sub>5</sub><em>,p</em><sub>6</sub>), i.e.,

(1)

where <em>W</em>(<em>x</em>;<em>p</em>) is the warping function from the template patch to the target image.

is the coordinate of the point before warping, and <em>A</em>(<em>p</em>) is the affine transform parametrized by <em>p</em>.

function [A_refined] = AlignImage(template, target, A)

<strong>Input: </strong>gray-scale template template and target image target; the initialization of 3×3 affine transform A, i.e., <em>x</em><sub>tgt </sub>=A<em>x</em><sub>tpl </sub>where <em>x</em><sub>tgt </sub>and <em>x</em><sub>tpl </sub>are points in the target and template images, respectively.

<strong>Output: </strong>A_refined is the refined affine transform based on inverse compositional image alignment

<strong>Description: </strong>You will refine the affine transform using inverse compositional image alignment, i.e., A→A_refined. The pseudo-code can be found in Algorithm 1. <strong>Tip: </strong>You can validate your algorithm by visualizing their error map as shown in Figure 6(d) and 6(h). Also you can visualize the error plot over iterations, i.e., the error must decrease as shown in Figure 6(i).

Figure 6: (a,e) Template images of the first frame. (b) Warped image based on the initialization of the affine parameters. (c) Template image is overlaid by the initialization. (d) Error map of the initialization. (f) Optimized warped image using the inverse compositional image alignment. (g) Template image is overlaid by the optimized warped image. (h) Error map of the optimization. (i) An error plot over iterations.

<strong>Algorithm 1 </strong>Inverse Compositional Image Alignment

1: Initialize <em>p </em>= <em>p</em><sub>0 </sub>from input A.

2: Compute the gradient of template image, ∇<em>I</em><sub>tpl </sub>3: Compute the Jacobian  at (<em>x</em>;0).

4: Compute the steepest decent images ∇<em>I</em><sub>tpl </sub><em><u><sup>∂W</sup></u><sub>∂p</sub></em>

5: Compute the 6 × 6 Hessian

6: <strong>while</strong><strong>do</strong>

7:                Warp the target to the template domain <em>I</em><sub>tgt</sub>(<em>W</em>(<em>x</em>;<em>p</em>)).

8:                  Compute the error image <em>I</em><sub>err </sub>= <em>I</em><sub>tgt</sub>(<em>W</em>(<em>x</em>;<em>p</em>)) − <em>I</em><sub>tpl</sub>.

P h∇<em>I</em>tpl <em><u>∂W</u>∂p </em>iT <em>I</em>err.

9:               Compute <em>F </em>=       <em>x</em>

10:               Compute ∆<em>p </em>= <em>H</em><sup>−1</sup><em>F</em>.

11:                   Update <em>W</em>(<em>x</em>;<em>p</em>) ← <em>W</em>(<em>x</em>;<em>p</em>) ◦ <em>W</em>(<em>x</em>;∆<em>p</em>) = <em>W</em>(<em>W</em>(<em>x</em>;∆<em>p</em>);<em>p</em>).

12: <strong>end while</strong>

13: Return A_refined made of <em>p</em>.

<h1>6             Putting Things Together: Multiframe Tracking</h1>

Figure 7: You will use the inverse compositional image alignment to track 4 frames of images.

Given a template and a set of consecutive images, you will (1) initialize the affine transform using the feature based alignment and then (2) track over frames using the inverse compositional image alignment.

function [A_cell] = TrackMultiFrames(template, image_cell)

<strong>Input: </strong>template is gray-scale template. image_cell is a cell structure that contains a set of consecutive image frames, i.e., image_cell{i} is the <em>i</em><sup>th </sup>frame.

<strong>Output: </strong>A_cell is the set of affine transforms from the template to each frame of image, i.e., A_cell{i} is the affine transform from the template to the <em>i</em><sup>th </sup>image. <strong>Description: </strong>You will apply the inverse compositional image alignment sequentially to track over frames as shown in Figure 7. Note that the template image needs to be updated at every frame, i.e., template←WarpImage(I, inv(A), size(template)).