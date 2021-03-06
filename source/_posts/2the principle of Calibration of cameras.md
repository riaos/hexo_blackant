---
title: the principle of Calibration of cameras
date: 2019-12-14 13:15:14
mathjax: true
tags: article
---
some papers always describe they present a novel method of Intrinsic Calibration of cameras, but few say they can realize intrinsic and extrinsic calibration of cameras. How they implement that? And, most of people know the calibration is to obtain the accurate intrinsic. What's the propose of extrinsic calibration of cameras?
<!--more-->
## Extrinsic

Before we explain that, firstly, we should introduce some conceptions, such as the world frame, the camera frame, image frame and pixel frame.

### Frames

when we define the world frame, it is usually used to describe the real position of object (camera, pixel in space) on the reality. and it should describe 3 degrees of freedom information.

For the camera frame, it often considers as the bridge between the world frame and the image/pixel frame, which observes other object from its origin. besides, it should describe 3 degrees of freedom information.

in order to describe the relation of projection between camera and image, the image frame is introduced naturally. Moreover, its origin often locates on the center of image and it have 2 degrees of freedom imformation. 

Lastly, we will talk about the pixel frame. Its origin often locates on the left upper corner and it have 2 degrees of freedom imformation. Concretely, (342, 123) means that the pixel is on the 342 rows, 123 columns.

![introduction of frames](/img/introductionofframes1.png)

the world frame: $X_w, Y_w, Z_w$, the camera frame: $X_c, Y_c, Z_c$, the image frame: $x, y$, the pixel frame: $u, v$

where, the z aixs of the camera frame is equal to the focal axis, it's also perpendicular to the image plane and through the origin of the image frame. the distance between them is focal length $f$. Besides, it can be seen the planes of pixel and image are the same, while the origin of the pixel frame is left upper corner.

### Transformation

the transformation of frame of a rigid object from the world frame to the camera frame can be calculated by rotation and translation. It usually consider as a homogeneous coordinate matrix, which was combined with a rotation matrix and translation vector. 

$$
\begin{align}\begin{bmatrix}x_c\\y_c\\z_c\\1\end{bmatrix}=\begin{bmatrix}R & t\\ 0^{T}_3&1\end{bmatrix}\begin{bmatrix}x_w\\y_w\\z_w\\1\end{bmatrix}=\begin{bmatrix}r_1&r_2&r_3&t\end{bmatrix}\begin{bmatrix}x_w\\y_w\\0\\1\end{bmatrix}=\begin{bmatrix}r_1&r_2&t\end{bmatrix}\begin{bmatrix}x_w\\y_w\\1\end{bmatrix}\end{align} \tag{1}
$$



where, R is rotation matrix, $t$ is translation vector. Suppose the initial plane of calibration is equal to the plane of $x_w-y_w$,  and its origin is also $x_w-O-y_w$, thus, the $z_w$ is 0 and $r_3$ can not take into account. So, the last formula can be derived. 

Therefore, the following is the transformation matrix.

$$
\begin{bmatrix}R & t\\ 0^{T}_3&1\end{bmatrix} \tag{2}
$$

so, it's the **Extrinsic** which has 6 parameters.

##Intrinsic

now, let's revise the projection of camera. firstly, we talk about the undistortion circumstance. 

### undistortion

It turns to talk about the transformation from the camera frame to the image frame.

![the principle of projection](/img/projection1.png)

Suppose that there is a point $(X_C, Y_C, Z_C)$ in the space, which has three degrees of freedom information when it is observed from **the camera frame**. So, if the undistortion happens, it can be derived as the following:

$$
x_c=f\frac{X_C}{Z_C}, y_c=f\frac{Y_C}{Z_C}\tag{3}
$$

Where $x_c, y_c$ are the image coordinate in the image frame.

it can be considered as the formula of homogeneous coordinate:

$$
Z_C\begin{bmatrix}x_c\\y_c\\1\end{bmatrix}=\begin{bmatrix}f&0&0\\0&f&0\\0&0&1\end{bmatrix}\begin{bmatrix}1&0&0&0\\0&1&0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix}=\begin{bmatrix}f&0&0&0\\0&f&0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix} \tag{4}
$$

Consequently, equation 4 is transformation from image frame to camera frame. **Note that a point in camera frame must be a three dimensions $(X_C, Y_C, Z_C)$.**

### From actual image frame to pixel frame

Since the origins between the pixel frame and image frame are not equal. Suppose that the origin of pixel frame is $(u_0, v_0)$ in the image frame, the size of each pixel in x and y axis of the image frame is $d_x, d_y$, respectively. Also, the actual coordinate of pixel is $(x_c, y_c)$ in the image frame. So, the coordinate of pixel in the pixel frame:

$$
u=\frac{x_c}{d_x}+u_0, v=\frac{y_c}{d_y}+v_0\tag{6}
$$

it is more convinent to calculate when considering the homogeneous coordinate:

$$
\begin{bmatrix}u\\v\\1\end{bmatrix}=\begin{bmatrix}\frac{1}{d_x}&0&u_0\\0&\frac{1}{d_y}&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}x_c\\y_c\\1\end{bmatrix}\tag{7}
$$

### General equations

if we want to re-considers the equation 4 from **the pixel frame**, Try to multiply $\begin{bmatrix}\frac{1}{d_x}&0&u_0\\\\0&\frac{1}{d_y}&v_0\\\\0&0&1\end{bmatrix}$ on the two sides of equation 4.

$$
Z_C\begin{bmatrix}\frac{1}{d_x}&0&u_0\\0&\frac{1}{d_y}&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}x_c\\y_c\\1\end{bmatrix}=\begin{bmatrix}\frac{1}{d_x}&0&u_0\\0&\frac{1}{d_y}&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}f&0&0&0\\0&f&0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix}\tag{8}\ 
$$

Then, equation 7 can be used to replace the left terms.

$$
Z_C\begin{bmatrix} u\\v \\1\end{bmatrix}=\begin{bmatrix}\frac{1}{d_x}&0&u_0\\0&\frac{1}{d_y}&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}f&0&0&0\\0&f&0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix}=\begin{bmatrix}\frac{f}{d_x}&0&u_0&0\\0&\frac{f}{d_y}&v_0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix}\tag{9}
$$

If it doesn't consider the distortion, This equation 9 is also called the transformation from pixel frame to camera frame.

Let us define a new matrix to replace the complex 3x4 matrix, is called $M$. 



$$
M=\begin{bmatrix}\frac{1}{d_x}&0&u_0\\0&\frac{1}{d_y}&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}f&0&0&0\\0&f&0&0\\0&0&1&0\end{bmatrix}=\begin{bmatrix}\frac{f}{d_x}&0&u_0&0\\0&\frac{f}{d_y}&v_0&0\\0&0&1&0\end{bmatrix} \tag{8}
$$

Thus, $M$ has 4 parameters, it has been fixed and is called by the **intrinsic matrix** which will not change as the thing goes, is a 3x4 matrix.

Hence, it can be rewritten as the following:

$$
Z_C\begin{bmatrix} u\\v \\1\end{bmatrix}=\begin{bmatrix}\frac{f}{d_x}&0&u_0&0\\0&\frac{f}{d_y}&v_0&0\\0&0&1&0\end{bmatrix}\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix}=M\begin{bmatrix}X_C\\Y_C\\Z_C\\1\end{bmatrix} \tag{9}
$$

Where the rightmost matrix vector $\begin{bmatrix}X_C\\\Y_C\\\Z_C\\\1\end{bmatrix}$ is a homogeneous coordinate, which is the point in **the camera frame**.

### Conclusion

Lastly, it can be sum up by a graph.

![](/img/core1.png)

### Distortion

in fact, any image is projected from three dimensions to two dimensions through a camera, it will produce distortion image. As you can see from textbook, it can be divided two types of distortions. one is radial distortion, the other is tangential distortion.

For radial distortion, it is produced by the craftsmanship and often uses the Taylor series of on the point of r=0 to promoxity the value, so after the correction, the relationship of can be derived as the following:

$$
\left\{\begin{matrix}x_{rcorr}=x_p(1+k_1r^2+k_2r^4+k_3r^6)
\\y_{rcorr}=y_p(1+k_1r^2+k_2r^4+k_3r^6)\end{matrix}\right.\tag{9}
$$

Thus, we should do solve three parameters of distortion.

For tangential distortion, it is made by the error of installation. After the correction, the relationship of can be derived as the following:

$$
\left\{\begin{matrix}x_{tcorr}=x_p+[2p_1x_py_p+p_2(r^2+2x^2_p)]
\\y_{tcorr}=y_p+[p_1(r^2+2y^2_p)+2p_2x_py_p)]\end{matrix}\right. \tag{10}
$$

Thus, there are two parameters need to solve.

In conclusion, it should to solve five parameters to correct the distortion of projection.

## Method to Solve Intrinsic and Extrinsic

If we can offer $K$ Checkboard images, each Checkboard has $N$ corners, then it will have $2KN$ constrain equations. Meanwhile, if the distortion will not take into account, it needs to know $4$ intrinsic and $6K$ extrinsic, since the extrinsic will change once the pose moves. Thereby, when the condition satisfies $2KN > =4+6K$, namely $K(N-3)>=2$, it can solve the matrix of extrinsic and intrinsic. Moreover, as the shape of Checkboard has beed already decided, the corners always keep 4. So, when $N=4$, namely $K>=2$. In other words, it needs at least 2 Checkboard images to derive the extrinsic and intrinsic of camera while overlooking the distortion.

Actually, to eliminate the noise and keep the stability of value, it often prepare more than 10 Checkboard images to obtain accurate results.

Meanwhile, it turns to introduce the conception of Homography, which defines the relationship of projection from one plane to another plane, to contain Intrinsic and Extrinsic. According to the prior knowledge about the relationship of  coordinate projection from the pixel frame to the world frame (equation 1) :

$$
\begin{bmatrix}u\\v\\1\end{bmatrix}=s\begin{bmatrix}f_x&0&u_0\\0&f_y&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}r_1&r_2&t\end{bmatrix}\begin{bmatrix}x_w\\y_w\\1\end{bmatrix}\tag{11}
$$

Where s is the proportion coefficient of arbitary scale. $u,v$ is the pixel coordinate in the pixel frame, $x_w, y_w$ is the world coordinate in the world frame, but it lose the axis of $z$ as it locates on the co-plane.

Therefore, the Homography matrix is defined by the calibration of cameras (from object plane to image plane) :

$$
H=s\begin{bmatrix}f_x&0&u_0\\0&f_y&v_0\\0&0&1\end{bmatrix}\begin{bmatrix}r_1&r_2&t\end{bmatrix}=sM\begin{bmatrix}r_1&r_2&t\end{bmatrix}\tag{12}
$$

### Undistortion

If assuming that $H=\begin{bmatrix}h_1&h_2&h_3\end{bmatrix}$, it can be decomposed as the following:

$$
h_1=sMr_1,\ or\ r_1=\lambda M^{-1}h_1\\h_2=sMr_2\ or \ r_2=\lambda M^{-1}h_2\\h_3=sMt\ or\ t=\lambda M^{-1}h_3,\ where\ \lambda=s^{-1},\  is\ also\ a\ scalar. \tag{13}
$$

since the rotation vector is inter-Orthogonal  in construction, namely $r_1$ and $r_2$ are inter-Orthogonal, it offers two constrains for each Homography matrix.

firstly, the dot product of rotation vector is 0, since each plane of rotation vector is inter-vertical:

$$
r^T_1r_2=0\tag{14}
$$

the result can be obtained by replacing and simplifying (remove the $\lambda$, since it is a scalar:

$$
h^T_1(M^{-1})^TM^{-1}h_2=0\tag{15}
$$

secondly, the length of each rotation vector is equal, namely scale invariable for rotation: 

$$
\left \|r_1  \right \|=\left \|r_2  \right \|=1, namely\ r^T_1r_1=r^T_2r_2\tag{16}
$$

$$
h^T_1(M^{-1})^TM^{-1}h_1=h^T_2(M^{-1})^TM^{-1}h_2\tag{17}
$$

Suppose that 

$$
B=(M^{-1})^TM^{-1}\tag{18}
$$

As the $M$ is a three square matrix, the inverse of $M$ is also a three square matrix. Obviously, $B$  belongs to three square matrix, which is also symmetric matrix (according to the rule that A matrix times its transpose is also a symmetric matrix), and likes the following:

$$
B=\begin{bmatrix}B_{11}&B_{12}&B_{13}\\B_{21}&B_{22}&B_{23}\\B_{31}&B_{32}&B_{33}\end{bmatrix}\tag{19}
$$

the previous equation 14 and 16 can be formulated as the following:

$$
\left\{\begin{matrix}h^T_1Bh_2=0\\h^T_1Bh_1=h^T_2Bh_2\end{matrix}\right.\tag{20}
$$

both of them can be covered by $h^T_iBh_j$.

For a symmetric matrix, it only have six valid values. the example of a sysmmetric matrix is the following:

$$
\begin{bmatrix}1&2&3\\2&5&6\\3&6&9\end{bmatrix}\tag{21}
$$

Clearly, only 1, 2, 3, 5, 6, 9 are valid values.

Now, it can be spread out as the Quadratic, which likes the following, when supposing that i and j are from 1 to 3, respectively :

$$
h^T_iBh_j=\begin{bmatrix}h_1h_1\\h_1h_2+h_2h_1\\h_2h_2\\h_1h_3+h_3h_1\\h_3h_2+h_2h_3\\h_3h_3\end{bmatrix}^{T}\begin{bmatrix}B_{11}\\B_{12}\\B_{22}\\B_{13}\\B_{32}\\B_{23}\end{bmatrix}=v^T_{ij}b \tag{22}
$$

where $b$ is equal to $\begin{bmatrix}B_{11}\\\B_{12}\\\B_{22}\\\B_{13}\\\B_{32}\\\B_{23}\end{bmatrix}$.

Therefore, equation 19 is equal to the following:

$$
v^T_{12}b=0\\(v^T_{11}-v^T_{22})b=0,\ namely, \begin{bmatrix}v^T_{12}\\(v^T_{11}-v^T_{22}\end{bmatrix}b=0 \tag{23}
$$

## Homography

It will be decomposed four answers, Thus, we can eliminate other possible terms by using two constraints. 

one is both image frames must be in the same side of the object plane.

the other is all the reference points being visible, they must be in the front of the camera.