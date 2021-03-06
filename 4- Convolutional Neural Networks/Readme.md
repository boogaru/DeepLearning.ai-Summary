# Convolutional Neural Networks

Đây là course thứ 4 trong loạt bài hướng dẫn deep learning tại [Coursera](https://www.coursera.org/specializations/deep-learning) which is moderated by [DeepLearning.ai](http://deeplearning.ai/). Khóa học được hướng dẫn bởi Andrew Ng.

## Table of contents

* [Convolutional Neural Networks](#convolutional-neural-networks)
   * [Table of contents](#table-of-contents)
   * [Course summary](#course-summary)
   * [Foundations of CNNs](#foundations-of-cnns)
      * [Computer vision](#computer-vision)
      * [Edge detection example](#edge-detection-example)
      * [Padding](#padding)
      * [Strided convolution](#strided-convolution)
      * [Convolutions over volumes](#convolutions-over-volumes)
      * [One Layer of a Convolutional Network](#one-layer-of-a-convolutional-network)
      * [A simple convolution network example](#a-simple-convolution-network-example)
      * [Pooling layers](#pooling-layers)
      * [Convolutional neural network example](#convolutional-neural-network-example)
      * [Why convolutions?](#why-convolutions)
   * [Deep convolutional models: case studies](#deep-convolutional-models-case-studies)
      * [Why look at case studies?](#why-look-at-case-studies)
      * [Classic networks](#classic-networks)
      * [Residual Networks (ResNets)](#residual-networks-resnets)
      * [Why ResNets work](#why-resnets-work)
      * [Network in Network and 1×1 convolutions](#network-in-network-and-1-X-1-convolutions)
      * [Inception network motivation](#inception-network-motivation)
      * [Inception network (GoogleNet)](#inception-network-googlenet)
      * [Using Open-Source Implementation](#using-open-source-implementation)
      * [Transfer Learning](#transfer-learning)
      * [Data Augmentation](#data-augmentation)
      * [State of Computer Vision](#state-of-computer-vision)
   * [Object detection](#object-detection)
      * [Object Localization](#object-localization)
      * [Landmark Detection](#landmark-detection)
      * [Object Detection](#object-detection-1)
      * [Convolutional Implementation of Sliding Windows](#convolutional-implementation-of-sliding-windows)
      * [Bounding Box Predictions](#bounding-box-predictions)
      * [Intersection Over Union](#intersection-over-union)
      * [Non-max Suppression](#non-max-suppression)
      * [Anchor Boxes](#anchor-boxes)
      * [YOLO Algorithm](#yolo-algorithm)
      * [Region Proposals (R-CNN)](#region-proposals-r-cnn)
   * [Special applications: Face recognition &amp; Neural style transfer](#special-applications-face-recognition--neural-style-transfer)
      * [Face Recognition](#face-recognition)
         * [What is face recognition?](#what-is-face-recognition)
         * [One Shot Learning](#one-shot-learning)
         * [Siamese Network](#siamese-network)
         * [Triplet Loss](#triplet-loss)
         * [Face Verification and Binary Classification](#face-verification-and-binary-classification)
      * [Neural Style Transfer](#neural-style-transfer)
         * [What is neural style transfer?](#what-is-neural-style-transfer)
         * [What are deep ConvNets learning?](#what-are-deep-convnets-learning)
         * [Cost Function](#cost-function)
         * [Content Cost Function](#content-cost-function)
         * [Style Cost Function](#style-cost-function)
         * [1D and 3D Generalizations](#1d-and-3d-generalizations)
   * [Extras](#extras)
      * [Keras](#keras)

## Course summary

Here is the course summary as given on the course [link](https://www.coursera.org/learn/convolutional-neural-networks):

> This course will teach you how to build convolutional neural networks and apply it to image data. Thanks to deep learning, computer vision is working far better than just two years ago, and this is enabling numerous exciting applications ranging from safe autonomous driving, to accurate face recognition, to automatic reading of radiology images. 
>
> You will:
> - Understand how to build a convolutional neural network, including recent variations such as residual networks.
> - Know how to apply convolutional networks to visual detection and recognition tasks.
> - Know to use neural style transfer to generate art.
> - Be able to apply these algorithms to a variety of image, video, and other 2D or 3D data.
>
> This is the fourth course of the Deep Learning Specialization.



## Foundations of CNNs

> Learn to implement the foundational layers of CNNs (pooling, convolutions) and to stack them properly in a deep network to solve multi-class image classification problems.

### Computer vision

- Computer vision là một trong những ứng dụng có tốc độ phát triển vũ bão nhờ vào deep learning
- Có thể kể ra những ứng dụng của computer vision xử dụng deep learning như là:
    + Self driving cars
    + Face recognition
- Deep learning cũng mở ra một dạng nghệ thuật mới
- Computer vision với kĩ thuật deep learning luôn kết hợp với nhau để tạo ra những kiến trúc mới có thể giúp ta trong những lĩnh vực khác của computer vision
    + Ví dụ, Andrew Ng áp dụng ý tưởng từ computer vision vào nhận dạng giọng nói
- Ví dụ về computer vision:
    + Image classification
    + Object dectection
        * Phát hiện vật thể và định vị nó
    + Neural style transfer
        * Thay đổi kiểu ảnh bằng cách dùng ảnh khác
- Một trong những thách thức của computer vision là kích thước ảnh quá lớn, và chúng ta lại muốn thuật toán làm hoạt động chính xác và nhanh
    + Ví dụ: tấm ảnh 1000x1000 với 3 triệu feature và sử dụng fully connected neural network. Giả sử lớn ẩn chứa 1000 node, khi đó ta phải train để tìm ra ma trận trọng số [1000, 3 triệu], khối lượng tính toán quá khổng lồ!
- Một trong các giải pháp là sử dụng **convolution layers** thay vì **fully connected layers**.

### Edge detection example

- Toán tử tích chập (convolution operation) là một trong những nền tảng cho CNN. Một ví dụ cho ứng dụng của tích chập đó là phát hiện cạnh cho ảnh
- Layers đầu của CNN có thể phát hiện cạnh, đến middle layers sẽ phát hiện parts của vật thể và layers cuối sẽ kết hợp các parts để tạo ra output
- Vertical edge detection:
  - Một ví dụ của toán tử tích chập để phát hiện cạnh đứng:
     ![](Images/01.png)
  - Như hình thì ma trận `6x6` được tích chập với filter/kernel `3x3` và tạo thành ma trận `4x4`
  - Nếu muốn thực hiện toán tử tích chập trong TensorFlow bạn có thể dùng hàm `tf.nn.conv2d`. Trong Keras thì tìm hiểu về `Conv2d` function.
  - Filter phát hiện cạnh đúng sẽ tìm khung `3x3` trong bức ảnh nơi mà có vùng sáng và vùng tối tách biệt (các cạnh sinh ra từ đây)
  - Nếu ta ứng dụng filter này cho vùng sáng kế bên là vùng tối thì nó sẽ tìm ra cạnh giữa 2 vùng và cho ra giá trị dương. Nhưng nếu ta áp dụng filter này cho vùng tối kế bên là vùng sáng thì nó sẽ cho ta giá trị âm. Cho nên ta cần dùng hàm abs để chuyển tất cả sang số dương.
- Horizontal edge detection
  - Filter sẽ trông như thế này:

    ```
    1	1	1
    0	0	0
    -1	-1	-1
    ```

- Có nhiều cách để ta đặt số vào filter cạnh đứng hoặc cạnh ngang. Ví dụ dưới đây là bộ filter cạnh đứng **Sobel** (Ý tưởng ở đây là chú ý đến hàng giữa):

  ```
  1	0	-1
  2	0	-2
  1	0	-1
  ```

- Also something called **Scharr** filter (The idea is taking great care of the middle row):

  ```
  3	0	-3
  10	0	-10
  3	0	-3
  ```

- Cái mà chúng ta học trong deep learning là để chúng ta không cần chỉnh các thông số cho filter bằng tay, chúng ta xem các thông số đó như những trọng số và sẽ được học qua quá trình huấn luyện. Có thể là filter horizontal, vertical, angled, hoặc bất kì dạng cạnh nào.

### Padding

- Để dùng deep neural networks chúng ta cần dùng **paddings**.
- Trong phần trước chúng ta đã có ví dụ với ma trận `6x6` tích chập với `3x3` filter/kernel tạo ra  ma trận `4x4`.
- Từ đó ta có luật như sau: Nếu ma trận `nxn` tích chập với `fxf` filter/kernel sẽ tạo ra ma trận `n-f+1,n-f+1`.
- Toán tử tích chập sẽ làm co lại ma trận đầu vào nếu f>1.
- Chúng ta muốn dùng tích chập nhiều lần, nhưng nếu bức ảnh bị co lại thì chúng ta sẽ bị mất thông tin trong quá trình xử lý. Ngoài ra thì các pixels ở biên thường ít được quan tâm hơn những pixels khác trong 1 bức ảnh.
- Vậy vấn đề với tích chập là:
  - Đầu ra bị co lại.
  - Mất nhiều thông tin ở cạnh biên.
- Để giải quyết những vấn đề này chúng ta có thể thêm pixels vào input image trước khi tích chập bằng cách thêm vài cột và hàng. Chúng ta sẽ gọi lượng cột/hàng được thêm vào là `P` (ví dụ P = 1 nghĩa là add 4 hướng thêm một cột và hàng)
- Trong hầu hết trường hợp, giá trị add thêm vào mỗi pixels là 0
- Ta có quy tắc sau: nếu ma trận `nxn` tích chập với `fxf` filter/kernel và padding `p` sẽ tạo ra ma trận `n+2p-f+1,n+2p-f+1`. 
- Nếu n = 6, f = 3, and p = 1 thì output image sẽ là `n+2p-f+1 = 6+2-3+1 = 6`. Vậy là ta đã bảo toàn được kích cỡ của ảnh.
- Cùng phép tích chập thì số lượng pad cần để output image cùng size với input image là:

  ```
  P = (f-1) / 2
  ```

- Trong computer vision f thường là số lẻ. Vì chúng ta cần filter có tâm.

### Strided convolution

- Strided convolution là một mẫu được dùng trong CNN.

- Ký hiệu stride `S`.

- Khi chúng ta tính tích chập, chúng ta dùng `S` để báo số lượng pixels mà chúng ta sẽ trượt khi tích chập với filter/kernel. Trong ví dụ trên thì ta trượt S = 1.

- Ta có quy tắc:
  -  Nếu ma trận `nxn` tích chập với `fxf` filter/kernel và padding `p` và stride `s` sẽ trả về ma trận `(n+2p-f)/s + 1,(n+2p-f)/s + 1`. 

- Trường hợp `(n+2p-f)/s + 1` là số lẻ, ta có thể làm tròn **floor** giá trị này.

- Trong toán học thì toán tử tích chập là phải flip ma trận filter trước khi dùng. Và những gì chúng ta làm ở trên gọi là toán tử tương quan (cross-correlation operation). Nhưng để làm đơn giảm tính toán và vì khả năng tự học của CNN nên ta vẫn dùng phép tương quan thay vì tích chập.

- Tương tự như tích chập là tích chập với padding để kích thước output = kích thước input:

  ```
  p = (n*s - n + f - s) / 2
  Khi s = 1 ==> P = (f-1) / 2
  ```

### Convolutions over volumes

- Chúng ta đã biết cách tích chập hoạt động với ảnh 2D, ta hãy xem tích chập hoạt động thế nào với ảnh 3D (RGB image)
- Chúng ta sẽ tích chập 1 tấm ảnh có height, width, # channels với 1 filter có height, width, cùng số channel với bức ảnh.
- Chúng ta có thể gọi nó là stacked filters cho mỗi channel!
- Ví dụ:
  - Input image: `6x6x3`
  - Filter: `3x3x3`
  - Result image: `4x4x1`
  - In the last result p=0, s=1
- Gợi ý : output chỉ là 2D.
- Chúng ta có thể dùng nhiều filters để phát hiện nhiều đặc trưng hoặc cạnh. Ví dụ:
  - Input image: `6x6x3`
  - 10 Filters: `3x3x3`
  - Result image: `4x4x10`
  - In the last result p=0, s=1

### One Layer of a Convolutional Network

- Trước tiên chúng ta tích chập vài filters với input và sau đó thêm bias cho mỗi filter output và sau đó tính RELU cho kết quả:
  - Input image: `6x6x3`         `# a0`
  - 10 Filters: `3x3x3`         `#W1`
  - Result image: `4x4x10`     `#W1a0`
  - Thêm b (bias) `10x1` sẽ cho ra : `4x4x10` image      `#W1a0 + b`
  - Apply RELU sẽ cho ra: `4x4x10` image                `#A1 = RELU(W1a0 + b)`
  - In the last result p=0, s=1
  -Số lượng parameter là: `(3x3x3x10) + 10 = 280`
- The last example forms a layer in the CNN.
- Gợi ý : kích thước input không ảnh hưởng đến số lượng parameters, mà nó chỉ phụ thuộc vào kích thước và số lượng channel của filter.
- Đây là những ký hiệu sẽ sử dụng. Nếu layer l là một conv layer:

  ```
  Hyperparameters
  f[l] = filter size
  p[l] = padding	# Default is zero
  s[l] = stride
  nc[l] = number of filters

  Input:  n[l-1] x n[l-1] x nc[l-1]	Or	 nH[l-1] x nW[l-1] x nc[l-1]
  Output: n[l] x n[l] x nc[l]	Or	 nH[l] x nW[l] x nc[l]
  Where n[l] = (n[l-1] + 2p[l] - f[l] / s[l]) + 1

  Each filter is: f[l] x f[l] x nc[l-1]

  Activations: a[l] is nH[l] x nW[l] x nc[l]
  		     A[l] is m x nH[l] x nW[l] x nc[l]   # In batch or minbatch training
  		     
  Weights: f[l] * f[l] * nc[l-1] * nc[l]
  bias:  (1, 1, 1, nc[l])
  ```

### A simple convolution network example

- Ví dụ xây dựng hệ đơn giản với 3 lớp conv và 1 lớp FC softmax:
  - Input Image là:   `a0 = 39x39x3`
    - `n0 = 39` and `nc0 = 3`
  - First layer (Conv layer):
    - `f1 = 3`, `s1 = 1`, and `p1 = 0`
    - `number of filters = 10`
    - Then output are `a1 = 37x37x10`
      - `n1 = 37` and `nc1 = 10`
  - Second layer (Conv layer):
    - `f2 = 5`, `s2 = 2`, `p2 = 0`
    - `number of filters = 20`
    - The output are `a2 = 17x17x20`
      - `n2 = 17`, `nc2 = 20`
    - Hint shrinking goes much faster because the stride is 2
  - Third layer (Conv layer):
    - `f3 = 5`, `s3 = 2`, `p2 = 0`
    - `number of filters = 40`
    - The output are `a3 = 7x7x40`
      - `n3 = 7`, `nc3 = 40`
  - Forth layer (Fully connected Softmax)
    - `a3 = 7x7x40 = 1960`  as a vector..
- Ta thấy kích thước ảnh đã nhỏ đi rất nhiều sau mỗi layer.
- Types of layer in a convolutional network:
  - Convolution. 		`#Conv`
  - Pooling      `#Pool`
  - Fully connected     `#FC`

### Pooling layers

- Khác với conv layers, CNNs thường dùng pooling layers để giảm kích thước của inputs, tăng tốc độ tính toán và làm khả năng phát hiện đặc trưng mạnh mẽ hơn.
- Ví dụ max pooling:
  - ![](Images/02.png)
  - Các siêu tham số : `f = 2`, `s = 2`, and `p = 0`
- Phương pháp **max pooling** như sau : lấy giá trị lớn nhất trong phạm vi của filter. Thường dùng phương pháp này vì nó hoạt động tốt khi thực nghiệm và tăng tốc tính toán.
- Ví du Max pooling trên 3D input:
  - Input: `4x4x10`
  - `Max pooling size = 2` and `stride = 2`
  - Output: `2x2x10`
- Average pooling sẽ lấy giá trị trung bình thay vì giá trị lớn nhất như max pooling.
- Nếu độ trượt (stride) bằng với kích thước pooling, nó sẽ làm co input lại.
- Hyperparameters summary
  - f : filter size.
  - s : stride.
  - Padding are rarely uses here.
  - Max or average pooling.

### Convolutional neural network example

- Chúng ta sẽ tính toán thử với full CNN. Lấy ví dụ từ hệ thống LeNet-5:
***LeNet-5*** Được phát minh bởi Yann Lecun.
  - Input Image are:   `a0 = 32x32x3`
    - `n0 = 32` and `nc0 = 3`
  - First layer (Conv layer):        `#Conv1`
    - `f1 = 5`, `s1 = 1`, and `p1 = 0`
    - `number of filters = 6`
    - Then output are `a1 = 28x28x6`
      - `n1 = 28` and `nc1 = 6`
    - Then apply (Max pooling):         `#Pool1`
      - `f1p = 2`, and `s1p = 2`
      - The output are `a1 = 14x14x6`
  - Second layer (Conv layer):   `#Conv2`
    - `f2 = 5`, `s2 = 1`, `p2 = 0`
    - `number of filters = 16`
    - The output are `a2 = 10x10x16`
      - `n2 = 10`, `nc2 = 16`
    - Then apply (Max pooling):         `#Pool2`
      - `f1p = 2`, and `s1p = 2`
      - The output are `a2 = 5x5x16`
  - Third layer (Fully connected)   `#FC3`
    - Number of neurons are 120
    - The output `a3 = 120 x 1` . 400 came from `5x5x16`
  - Forth layer (Fully connected)  `#FC4`
    - Number of neurons are 84
    - The output `a4 = 84 x 1` .
  - Fifth layer (Softmax)
    - Number of neurons is 10 if we need to identify for example the 10 digits.
- Conv1 và Pool1 được xem như 1 layer.
- Some statistics about the last example:
  ![](Images/03.png)
- Hyperparameters are a lot. For choosing the value of each you should follow the guideline that we will discuss later or check the literature and takes some ideas and numbers from it.
- Thông thường thì kích thước đầu vào sẽ giảm dần theo số lượng filter
- Một CNN thường chứa 1 hoặc nhiều conv layer + pooling layer
- Fully connected layers has the most parameters in the network.
- To consider using these blocks together you should look at other working examples firsts to get some intuitions.

### Why convolutions?
- Hai lý do chính để dùng nhiều tích chập layer hơn là FC layer:
    + Share parameter
        + 1 cái feature detector (như vertical edge detector) hữu dụng cho phần này của bức ảnh thì cũng có thể hữu dụng cho phần khác của bức ảnh, nên việc share parameter sẽ giảm việc tính toán
    + Kết nối thưa:
        + Trong mỗi layer, mỗi output value chỉ phụ thuộc một vài feature của input (mỗi pixel của output matrix chỉ phụ thuộc vài pixels của input matrix)
- Thêm nữa là số lượng parameter khi dùng conv layer sẽ ít hơn nhiều khi dùng FC layer, nó giúp đẩy nhanh tốc độc training, giảm khả năng overfitting.
- Putting it all together:
   ![](Images/04.png)

## Deep convolutional models: case studies

> Learn about the practical tricks and methods used in deep CNNs straight from the research papers.

### Why look at case studies?

- Chúng ta học về Conv layer, pooling layer, fully connected layers. Các nhà nghiên cứu thị giác máy tính đã mất vài năm để biết cách kết hợp các layers lại với nhau.
- Để có cái nhìn trực quan, ta hãy xem qua ví dụ.
- Một vài kiến trúc neural networks có thể làm tốt nhiệm vụ này và kiêm thêm những nhiệm vụ khác.
- Đây là một số mạng CNN cổ điển:
  - **LeNet-5**
  - **AlexNet**
  - **VGG**
- Kiến trúc CNN tốt nhất đã từng thắng giải ImageNet là **ResNet** và nó có 152 layers!
- Kiến trúc **Inception** được xây dựng bởi Google rất có ích cho ta học tập và ứng dụng.
- Nghiên cứu và thử những models đã đề cập ở trên sẽ giúp ích rất nhiều cho bản thân.

### Classic networks

- Trong phần này chúng ta sẽ nói về những mạng cổ điển : **LeNet-5**, **AlexNet**, và **VGG**


- **LeNet-5**
    - Mục tiêu của model này là xác định số viết tay ở dạng ảnh xám `32x32x1`. Đây là hình ảnh của nó
   ![](Images/05.png)
    - Model này được xuất bản năm 1998. Layer cuối cùng dùng softmax.
    - Nó có 60k parameters.
    - Chiều của ảnh giảm khi số lượng channels tăng lên
    - `Conv ==> Pool ==> Conv ==> Pool ==> FC ==> FC ==> softmax`
    - Hàm kích hoạt được dùng trong paper là Sigmod và Tanh. Ngày nay hàm RELU được dùng trong hầu hết các trường hợp.
  
    - [[LeCun et al., 1998. Gradient-based learning applied to document recognition]](http://ieeexplore.ieee.org/document/726791/?reload=true)

- **AlexNet**
    + Mục tiêu của model là phân ra 1000 class. Đây là hình vẽ của model
   ![](Images/06.png)

    + Tổng kết:

    ```
      Conv => Max-pool => Conv => Max-pool => Conv => Conv => Conv => Max-pool ==> Flatten ==> FC ==> FC ==> Softmax
    ```

    - Tương tự LeNet-5 nhưng lớn hơn
    - Có 60 triệu parameter.
    - Dùng hàm kích hoạt RELU 
    - The original paper sử dụng nhiều GPUs và chuẩn hóa phản hồi cục bộ (Local response normalization - RN).
        - Dùng nhiều GPU vì nó đẩy nhanh tốc độ tính toán.
        - Những nhà nghiên cứu đã chứng minh RN không giúp gì nhiều cho CNN nên chúng ta không cần quan tâm đến nó

    - Bài báo sau đây đã thuyết phục giới nghiên cứu thị giác máy tính về sự quan trọng của deep learning:

    - [[Krizhevsky et al., 2012. ImageNet classification with deep convolutional neural networks]](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)

- **VGG-16**

  - Là phiên bản sửa đổi của AlexNet.
  - Thay vì dùng nhiều siêu tham số(hyperparameters) thì sẽ sử dụng những mạng đơn giản hơn.
  - Tập trung vào các block sau:
    - CONV = 3 X 3 filter, s = 1, same  
    - MAX-POOL = 2 X 2 , s = 2
  - Here are the architecture:
    - ![](Images/07.png)
  - Nhưng mạng này vẫn lớn ngay cả với tiêu chuẩn hiện nay. Nó tầm khoảng 138 triệu parameters.
    - Hầu hết the parameters nằm trong layer fully connected.
  - It has a total memory of 96MB per image for only forward propagation!
    - Most memory are in the earlier layers.
  - Number of filters increases from 64 to 128 to 256 to 512. 512 was made twice.
  - Pooling was the only one who is responsible for shrinking the dimensions.
  - There are another version called **VGG-19** which is a bigger version. But most people uses the VGG-16 instead of the VGG-19 because it does the same.
  - VGG paper is attractive it tries to make some rules regarding using CNNs.
  - [[Simonyan & Zisserman 2015. Very deep convolutional networks for large-scale image recognition]](https://arxiv.org/abs/1409.1556)

### Residual Networks (ResNets)

- Very, very deep NNs are difficult to train because of vanishing and exploding gradients problems.
- In this section we will learn about skip connection which makes you take the activation from one layer and suddenly feed it to another layer even much deeper in NN which allows you to train large NNs even with layers greater than 100.
- **Residual block**
  - ResNets are built out of some Residual blocks.
  - ![](Images/08.png)
  - They add a shortcut/skip connection before the second activation.
  - The authors of this block find that you can train a deeper NNs using stacking this block.
  - [[He et al., 2015. Deep residual networks for image recognition]](https://arxiv.org/abs/1512.03385)
- **Residual Network**
  - Are a NN that consists of some Residual blocks.
  - ![](Images/09.png)
  - These networks can go deeper without hurting the performance. In the normal NN - Plain networks - the theory tell us that if we go deeper we will get a better solution to our problem, but because of the vanishing and exploding gradients problems the performance of the network suffers as it goes deeper. Thanks to Residual Network we can go deeper as we want now.
  - ![](Images/10.png)
  - On the left is the normal NN and on the right are the ResNet. As you can see the performance of ResNet increases as the network goes deeper.
  - In some cases going deeper won't effect the performance and that depends on the problem on your hand.
  - Some people are trying to train 1000 layer now which isn't used in practice. 
  - [He et al., 2015. Deep residual networks for image recognition]

### Why ResNets work

- Lets see some example that illustrates why resNet work.

  - We have a big NN as the following:

    - `X --> Big NN --> a[l]`

  - Lets add two layers to this network as a residual block:

    - `X --> Big NN --> a[l] --> Layer1 --> Layer2 --> a[l+2]`
    - And a`[l]` has a direct connection to `a[l+2]`

  - Suppose we are using RELU activations.

  - Then:

    - ```
      a[l+2] = g( z[l+2] + a[l] )
      	   = g( W[l+2] a[l+1] + b[l+2] + a[l] )
      ```

  - Then if we are using L2 regularization for example, `W[l+2]` will be zero. Lets say that `b[l+2]` will be zero too.

  - Then `a[l+2] = g( a[l] ) = a[l]` with no negative values.

  - This show that identity function is easy for a residual block to learn. And that why it can train deeper NNs.

  - Also that the two layers we added doesn't hurt the performance of big NN we made.

  - Hint: dimensions of z[l+2] and a[l] have to be the same in resNets. In case they have different dimensions what we put a matrix parameters (Which can be learned or fixed)

    - `a[l+2] = g( z[l+2] + ws * a[l] ) # The added Ws should make the dimentions equal`
    - ws also can be a zero padding.

- Using a skip-connection helps the gradient to backpropagate and thus helps you to train deeper networks

- Lets take a look at ResNet on images.

  - Here are the architecture of **ResNet-34**:
  - ![](Images/resNet.jpg)
  - All the 3x3 Conv are same Convs.
  - Keep it simple in design of the network.
  - spatial size /2 => # filters x2
  - No FC layers, No dropout is used.
  - Two main types of blocks are used in a ResNet, depending mainly on whether the input/output dimensions are same or different. You are going to implement both of them.
  - The dotted lines is the case when the dimensions are different. To solve then they down-sample the input by 2 and then pad zeros to match the two dimensions. There's another trick which is called bottleneck which we will explore later.

- Useful concept (**Spectrum of Depth**):

  - ![](Images/12.png)
  - Taken from [icml.cc/2016/tutorials/icml2016_tutorial_deep_residual_networks_kaiminghe.pdf](icml.cc/2016/tutorials/icml2016_tutorial_deep_residual_networks_kaiminghe.pdf)

- Residual blocks types:

  - Identity block:
    - ![](Images/16.png)
    - Hint the conv is followed by a batch norm `BN` before `RELU`. Dimensions here are same.
    - This skip is over 2 layers. The skip connection can jump n connections where n>2
    - This drawing represents [Keras](https://keras.io/) layers.
  - The convolutional block:
    - ![](Images/17.png)
    - The conv can be bottleneck 1 x 1 conv

### Network in Network and 1 X 1 convolutions

- A 1 x 1 convolution  - We also call it Network in Network- is so useful in many CNN models.

- What does a 1 X 1 convolution do? Isn't it just multiplying by a number?

  - Lets first consider an example:
    - Input: `6x6x1`
    - Conv: `1x1x1` one filter.        `# The 1 x 1 Conv`
    - Output: `6x6x1`
  - Another example:
    - Input: `6x6x32`
    - Conv: `1x1x32` 5 filters.     `# The 1 x 1 Conv`
    - Output: `6x6x5`

- The Network in Network is proposed in [Lin et al., 2013. Network in network]

- It has been used in a lot of modern CNN implementations like ResNet and Inception models.

- A 1 x 1 convolution is useful when:

  - We want to shrink the number of channels. We also call this feature transformation.
    - In the second discussed example above we have shrinked the input from 32 to 5 channels.
  - We will later see that by shrinking it we can save a lot of computations.
  - If we have specified the number of 1 x 1 Conv filters to be the same as the input number of channels then the output will contain the same number of channels. Then the 1 x 1 Conv will act like a non linearity and will learn non linearity operator. 

- Replace fully connected layers with 1 x 1 convolutions as Yann LeCun believes they are the same.

  - > In Convolutional Nets, there is no such thing as "fully-connected layers". There are only convolution layers with 1x1 convolution kernels and a full connection table. [Yann LeCun](https://www.facebook.com/yann.lecun/posts/10152820758292143) 

- [[Lin et al., 2013. Network in network]](https://arxiv.org/abs/1312.4400)

### Inception network motivation

- When you design a CNN you have to decide all the layers yourself. Will you pick a 3 x 3 Conv or 5 x 5 Conv or maybe a max pooling layer. You have so many choices.
- What **inception** tells us is, Why not use all of them at once?
- **Inception module**, naive version:
  - ![](Images/13.png)
  - Hint that max-pool are same here.
  - Input to the inception module are 28 x 28 x 192 and the output are 28 x 28 x 256
  - We have done all the Convs and pools we might want and will let the NN learn and decide which it want to use most.
  - [[Szegedy et al. 2014. Going deeper with convolutions]](https://arxiv.org/abs/1409.4842)
- The problem of computational cost in Inception model:
  - If we have just focused on a 5 x 5 Conv that we have done in the last example.
  - There are 32 same filters of 5 x 5, and the input are 28 x 28 x 192.
  - Output should be 28 x 28 x 32
  - The total number of multiplications needed here are:
    - Number of outputs * Filter size * Filter size * Input dimensions
    - Which equals: `28 * 28 * 32 * 5 * 5 * 192 = 120 Mil` 
    - 120 Mil multiply operation still a problem in the modern day computers.
  - Using a 1 x 1 convolution we can reduce 120 mil to just 12 mil. Lets see how.
- Using 1 X 1 convolution to reduce computational cost:
  - The new architecture are:
    - X0 shape is (28, 28, 192)
    - We then apply 16 (1 x 1 Convolution)
    - That produces X1 of shape (28, 28, 16)
      - Hint, we have reduced the dimensions here.
    - Then apply 32  (5 x 5 Convolution)
    - That produces X2 of shape (28, 28, 32)
  - Now lets calculate the number of multiplications:
    - For the first Conv: `28 * 28 * 16 * 1 * 1 * 192 = 2.5 Mil`
    - For the second Conv: `28 * 28 * 32 * 5 * 5 * 16 = 10 Mil`
    - So the total number are 12.5 Mil approx. which is so good compared to 120 Mil
- A 1 x 1 Conv here is called Bottleneck `BN`.
- It turns out that the 1 x 1 Conv won't hurt the performance.
- **Inception module**, dimensions reduction version:
  - ![](Images/14.png)
- Example of inception model in Keras:
  - ![](Images/inception_block1a.png)

### Inception network (GoogleNet)

- The inception network consist of concatenated blocks of the Inception module.
- The name inception was taken from a *meme* image which was taken from **Inception movie**
- Here are the full model:
  - ![](Images/15.png)
- Some times a Max-Pool block is used before the inception module to reduce the dimensions of the inputs.
- There are a 3 Sofmax branches at different positions to push the network toward its goal. and helps to ensure that the intermediate features are good enough to the network to learn and it turns out that softmax0 and sofmax1 gives regularization effect.
- Since the development of the Inception module, the authors and the others have built another versions of this network. Like inception v2, v3, and v4. Also there is a network that has used the inception module and the ResNet together.
- [[Szegedy et al., 2014, Going Deeper with Convolutions]](https://arxiv.org/abs/1409.4842)

### Using Open-Source Implementation

- We have learned a lot of NNs and ConvNets architectures.
- It turns out that a lot of these NN are difficult to replicated. because there are some details that may not presented on its papers. There are some other reasons like:
  - Learning decay.
  - Parameter tuning.
- A lot of deep learning researchers are opening sourcing their code into Internet on sites like [Github](Github.com).
- If you see a research paper and you want to build over it, the first thing you should do is to look for an open source implementation for this paper.
- Some advantage of doing this is that you might download the network implementation along with its parameters/weights. The author might have used multiple GPUs and spent some weeks to reach this result and its right in front of you after you download it.

### Transfer Learning

- Nếu bạn đang dùng một kiến trúc NN cụ thể nào đó đã được train từ trước, bạn có thể dùng nó để pretrain parameters/weights thay vì khởi tạo ngẫu nhiên, để giải quyết bài toán của bạn.

- Điều này có thể giúp bạn boost hiệu suất của NN.

- Pretrained models có thể đã được train trên dataset rất lớn như Imagenet, Ms CÔC, hoặc pascal và đã tốn rất nhiều thời gian để learn những parameters/weights đã được tối ưu. Tận dụng nó bạn sẽ tiết kiệm được rất nhiều thời gian.

- Hãy xem ví dụ sau:
    - Bạn muốn giải bài toán phân loại mèo với 3 class: Tigger, Misty và khác.
    - Bạn không có nhiều data để train NN.
    - Andrew đề xuất lên mạng và download 1 model NN với trọng số đầy đủ, bỏ layer kích hoạt softmax và đặt vào đó hàm kích hoạt của bạn và bắt đầu train network với chỉ layer mới thêm vào trong khi những trọng số của layer khác thì được giữ nguyên.
    - One of the tricks that can speed up your training, is to run the pretrained NN without final softmax layer and get an intermediate representation of your images and save them to disk. And then use these representation to a shallow NN network. This can save you the time needed to run an image through all the layers.
        - Its like converting your images into vectors.

- Một ví dụ khác:
    - Sẽ như thế nào nếu bạn có thật nhiều ảnh mèo cho ví dụ ở trên.
    - Điều duy nhất bạn có thể làm là đóng băng vài layers khi bắt đầu pretrain network và học các trọng số trong network.
    - Có vài ý kiến khác là bỏ đi những layer không đóng băng và đặt layer mới vào.
- Một ví dụ khác:
    - Nếu bạn có đủ data, bạn có thể tinh chỉnh tất cả layer trong mạng pretrain của bạn hoặc không cần khởi tạo các parameter ngẫu nhiên, chỉ cần dùng lại các parameter đã được train. Và mạng mới sẽ bắt đầu train với những parameter đó.

### Data Augmentation

- Nếu data tăng lên, mạng deep NN sẽ tăng hiệu quả. Tăng cường data (data augmentation) là một trong nhưng kĩ thuật được áp dụng trong deep learning để tăng hiệu quả của NN.
- The majority of computer vision applications needs more data right now.
- Some data augmentation methods that are used for computer vision tasks includes:
  - Mirroring.
  - Random cropping.
    - The issue with this technique is that you might take a wrong crop.
    - The solution is to make your crops big enough.
  - Rotation.
  - Shearing.
  - Local warping.
  - Color shifting.
    - For example, we add to R, G, and B some distortions that will make the image identified as the same for the human but is different for the computer.
    - In practice the added value are pulled from some probability distribution and these shifts are some small.
    - Makes your algorithm more robust in changing colors in images. 
    - There are an algorithm which is called ***PCA color augmentation*** that decides the shifts needed automatically.
- Implementing distortions during training:
  - You can use a different CPU thread to make you a distorted mini batches while you are training your NN.
- Data Augmentation has also some hyperparameters. A good place to start is to find an open source data augmentation implementation and then use it or fine tune these hyperparameters.

### State of Computer Vision

- Trong những bài toán cụ thể có thể bạn sẽ có ít hoặc nhiều data.
- Speech recognition problems for example has a big amount of data, while image recognition has a medium amount of data and the object detection has a small amount of data nowadays.
- Nếu bài toán của bạn có lượng lớn data, người nghiên cứu sẽ dùng:
  - Simpler algorithms.
  - Less hand engineering.
- Nếu không đủ data, thì sẽ tốn nhiều công sức hơn như là : chọn mạng NN phức tạp hơn.
- Vì chúng ta không có nhiều data cho lĩnh vực computer vision, nên phải phụ thuộc nhiều vào hand engineering.
- Chúng ta sẽ xem ở chapter kế, trong việc Object dectection có rất ít data, sẽ phải dùng một mạng NN rất phức tạp
- Tips for doing well on benchmarks/winning competitions:
  - Ensembling.
    - Train several networks independently and average their outputs. Merging down some classifiers.
    - After you decide the best architecture for your problem, initialize some of that randomly and train them independently.
    - This can give you a push by 2%
    - But this will slow down your production by the number of the ensembles. Also it takes more memory as it saves all the models in the memory.
    - People use this in competitions but few uses this in a real production.
  - Multi-crop at test time.
    - Run classifier on multiple versions of test versions and average results.
    - There is a technique called 10 crops that uses this.
    - This can give you a better result in the production.
- Use open source code
  - Use architectures of networks published in the literature.
  - Use open source implementations if possible.
  - Use pretrained models and fine-tune on your dataset.

## Object detection

> Chúng ta sẽ học cách nào ứng dụng CNN vào một trong những lĩnh vực khó nhất và hot nhất của thị giác máy tính: object detection.

### Object Localization

- Object detection là một trong những lĩnh vực mà deep learning đã làm rất tốt trong 2 năm qua.

- What are localization and detection?

  - **Image Classification**: 
    - Là kiểu Phân loại 1 tấm hình vào 1 class cụ thể. Toàn bộ tấm hình đại diện cho 1 class. Chúng ta không muốn biết chính xác vật thể nằm ở đâu. Thông thường thì chỉ có 1 vật thể trong ảnh.
     ![](Images/Classification.jpg)
  - **Classification with localization**:
    - Đưa ra 1 tấm ảnh với class không phải là cả tấm ảnh mà là 1 đối tượng trong tấm ảnh đó. Chúng ta cần phát hiện ra class và dfung 1 khung chữ nhật để bao quanh nó. Thường chỉ có 1 object bên trong ảnh.
     ![](Images/ClassificationLoc.jpg)
  - **Object detection**:
    - Đưa ra 1 tấm ảnh chúng ta muốn phát hiện tất cả object bên trong ảnh và phân loại nó ra từng class cụ thể. Một tấm ảnh có thể có nhiều class khác nhau.
    ![](Images/ObjectDetection.png)
  - **Semantic Segmentation**:
    - Chúng ta muốn đánh nhãn cho mỗi pixel bên trong tấm hình với một danh mục các nhãn biết trước. Semantic segmentation không phân biệt các vật cụ thể, chỉ quan tâm đến pixel. Nó phát hiện pixels chứ ko phát hiện vật thể.
    - Nếu có 2 object của cùng 1 class, chúng ta sẽ ko thể phân biệt được chúng.
    - ![](Images/SemanticSegmentation.png)
  - **Instance Segmentation**
    - Loại này như 1 bài toán tổng quát. Thay vì chúng ta muốn dự đoán cái bounding box, thì chúng ta lại muốn biết pixel label cũng như phân biệt chúng.
    ![](Images/InstanceSegmentation.png)

- Giải bài toán image classification --> dùng Conv net với Softmax ở layer cuối

- Giải bài toán Classification with localization --> dùng Conv net với softmax ở layer cuối và một bộ 4 số `bx`, `by`, `bh`, `bw` để xác định vị trí của class trong image. Dataset nên chứa bộ 4 số này chung với class.

- Cấu trúc của target label Y trong classification with localization problem: 

    ```
    Y = [
      		Pc				# Probability of an object is presented
      		bx				# Bounding box
      		by				# Bounding box
      		bh				# Bounding box
      		bw				# Bounding box
      		c1				# The classes
      		c2
      		...
    ]
    ```

  - Example (Object is present):

    ```
      Y = [
        		1		# Object is present
        		0
        		0
        		100
        		100
        		0
        		1
        		0
      ]
    ```

  - Example (When object isn't presented):

    ```
      Y = [
        		0		# Object isn't presented
        		?		# ? means we dont care with other values
        		?
        		?
        		?
        		?
        		?
        		?
      ]
    ```

- The loss function for the Y we have created (Example of the square error):

  - ```
    L(y',y) = {
      			(y1'-y1)^2 + (y2'-y2)^2 + ...           if y1 = 1
      			(y1'-y1)^2						if y1 = 0
    		}
    ```

  - In practice we use logistic regression for `pc`, log likely hood loss for classes, and squared error for the bounding box.

### Landmark Detection

- In some of the computer vision problems you will need to output some points. That is called **landmark detection**.

- For example, if you are working in a face recognition problem you might want some points on the face like corners of the eyes, corners of the mouth, and corners of the nose and so on. This can help in a lot of application like detecting the pose of the face.

- Y shape for the face recognition problem that needs to output 64 landmarks:

  - ```
    Y = [
      		THereIsAface				# Probability of face is presented 0 or 1
      		l1x,
      		l1y,
      		....,
      		l64x,
      		l64y
    ]
    ```

- Another application is when you need to get the skeleton of the person using different landmarks/points in the person which helps in some applications.

- Hint, in your labeled data, if `l1x,l1y` is the left corner of left eye, all other `l1x,l1y` of the other examples has to be the same.

### Object Detection

- Chúng ta sẽ dùng Conv net để giải bài toán object detection, sử dụng kỹ thuật gọi là cửa sổ trượt.
- Thuật toán đó hoạt động như sau: ta đã xây dựng được Conv net, trong 1 tấm ảnh lớn thì tạo ra 1 sliding window cho nó trượt hết các vùng trong tấm ảnh, rồi lấy từng giữ liệu đó cho vào Conv net.
- Ví dụ : ta đang giải quyết bài toán nhận dạng xe hơi.
- Đầu tiên, chúng ta sẽ train 1 Conv net trên ảnh đã crop chứa xe hơi và không chứa xe hơi.
  ![](Images/18.png)
- Sau khi training, chúng ta sẽ dùng nó kết hợp kỹ thuật cửa sổ trượt.
- Sliding windows detection algorithm:
  1. Quyết định kích cỡ của hình chữ nhật (bounding box).
  2. Chia tấm ảnh thành những hình chữa nhật (với kích thước đã chọn ở trên). Nên bao trùm tất cả các vùng. Bạn có thể dùng vài strides.
  3.Với mỗi hình chữ nhật (ma trận) hãy nạp nó vào mạng Conv để quyết định xem nó có phải oto hay không
  4. Chọn bounding box nhỏ hơn hoặc lớn hơn và lặp lại quá trình 2 đến 3 lần.
  5. Lưu lại hình chữ nhật có chứa ô tô.
  6. Nếu hai hoặc hơn số hình chữ nhật cắt nhau, hãy chọn hình chữ nhật có độ chính xác cao hơn (xác suất cao hơn).
- Nhược điểm của cửa sổ trượt là thời gian tính toán.
- In the era of machine learning before deep learning, people used a hand crafted linear classifiers that classifies the object and then use the sliding window technique. The linear classier make it a cheap computation. But in the deep learning era that is so  computational expensive due to the complexity of the deep learning model.
- Để giải quyết vấn đề khối lượng tính toán, chúng ta có thể triển khai cửa sổ trượt với ***Convolutional approach***.
- Một ý tưởng khác là giảm kích thước model deep learning lại.

### Convolutional Implementation of Sliding Windows

- Để hạn chế việc phải slide quá nhiều lần. Thì ta sẽ tích hợp cửa sổ trượt vào Convnet luôn, bằng cách thay các layer FC bằng Conv layer. Sau đó, đưa cả tấm ảnh chứa nhiều vật thể vào Convnet vừa tạo được luôn.
- Chuyển FC layer thành conv layer (predict image class from four classes):
   ![](Images/19.png)
    - Ảnh 14x14x3 + 16 filter 5x5 --> 10x10x6... qua các giai đoạn thì giảm còn 5x5x16 và sau đó dùng layer FC để kết nối 400 units và thêm 1 layer FC nữa và cuối cùng ouput y dùng softmax.
    - Như hình trên thì chúng ta đã chuyển FC layer sang Conv layer bằng cách dùng 1 filter có height và width bằng với input.
- **Convolution implementation of sliding windows**:
  - First lets consider that the Conv net you trained is like this (No FC all is conv layers):
     ![](Images/20.png)
  - Giờ ta có tấm ảnh 16x16x3 và ta sẽ áp dụng cửa sổ trượt với nó.
  - The convolution implementation will be as follows:
    - ![](Images/21.png)
  - Simply we have feed the image into the same Conv net we have trained.
  - The left cell of the result "The blue one" will represent the the first sliding window of the normal implementation. The other cells will represent the others.
  - Its more efficient because it now shares the computations of the four times needed.
  - Another example would be:
    - ![](Images/22.png)
  - This example has a total of 16 sliding windows that shares the computation together.
  - [[Sermanet et al., 2014, OverFeat: Integrated recognition, localization and detection using convolutional networks]](https://arxiv.org/abs/1312.6229)
- Điểm yếu của phương pháp này chính là vị trí của bounding box không chính xác. Có thể không có bounding box nào chính xác vị trí của đối tượng bạn muốn nhận dạng
   ![](Images/23.png)
  - In red, the rectangle we want and in blue is the required car rectangle.

### Bounding Box Predictions

- A better algorithm than the one described in the last section is the [YOLO algorithm](https://arxiv.org/abs/1506.02640).

- YOLO stands for *you only look once* and was developed back in 2015.

- Yolo Algorithm:

  - ![](Images/24.png)

  1. Lets say we have an image of 100 X 100
  2. Place a  3 x 3 grid on the image. For more smother results you should use 19 x 19 for the 100 x 100
  3. Apply the classification and localization algorithm we discussed in a previous section to each section of the grid. `bx` and `by` will represent the center point of the object in each grid and will be relative to the box so the range is between 0 and 1 while `bh` and `bw` will represent the height and width of the object which can be greater than 1.0 but still a floating point value.
  4. Do everything at once with the convolution sliding window. If Y shape is 1 x 8 as we discussed before then the output of the 100 x 100 image should be 3 x 3 x 8 which corresponds to 9 cell results.
  5. Merging the results using predicted localization mid point.

- We have a problem if we have found more than one object in one grid box.

- One of the best advantages that makes the YOLO algorithm popular is that it has a great speed and a Conv net implementation.

- How is YOLO different from other Object detectors?  YOLO uses a single CNN
  network for both classification and localizing the object using bounding boxes.

- In the next sections we will see some ideas that can make the YOLO algorithm better.

### Intersection Over Union

- Intersection Over Union is a function used to evaluate the object detection algorithm.
- It computes size of intersection and divide it by the union. More generally, *IoU* *is a measure of the overlap between two bounding boxes*.
- For example:
  - ![](Images/25.png)
  - The red is the labeled output and the purple is the predicted output.
  - To compute Intersection Over Union we first compute the union area of the two rectangles which is "the first rectangle + second rectangle" Then compute the intersection area between these two rectangles.
  - Finally `IOU = intersection area / Union area`
- If `IOU >=0.5` then its good. The best answer will be 1.
- The higher the IOU the better is the accuracy.

### Non-max Suppression

- One of the problems we have addressed in YOLO is that it can detect an object multiple times.
- Non-max Suppression is a way to make sure that YOLO detects the object just once.
- For example:
  - ![](Images/26.png)
  - Each car has two or more detections with different probabilities. This came from some of the grids that thinks that this is the center point of the object.
- Non-max suppression algorithm:
  1. Lets assume that we are targeting one class as an output class.
  2. Y shape should be `[Pc, bx, by, bh, hw]` Where Pc is the probability if that object occurs.
  3. Discard all boxes with `Pc < 0.6`  
  4. While there are any remaining boxes:
     1. Pick the box with the largest Pc Output that as a prediction.
     2. Discard any remaining box with `IoU > 0.5` with that box output in the previous step i.e any box with high overlap(greater than overlap threshold of 0.5).
- If there are multiple classes/object types `c` you want to detect, you should run the Non-max suppression `c` times, once for every output class.

### Anchor Boxes

- In YOLO, a grid only detects one object. What if a grid cell wants to detect multiple object?
  - ![](Images/27.png)
  - Car and person grid is same here.
  - In practice this happens rarely.
- The idea of Anchor boxes helps us solving this issue.
- If Y = `[Pc, bx, by, bh, bw, c1, c2, c3]` Then to use two anchor boxes like this:
  - Y = `[Pc, bx, by, bh, bw, c1, c2, c3, Pc, bx, by, bh, bw, c1, c2, c3]`  We simply have repeated  the one anchor Y.
  - The two anchor boxes you choose should be known as a shape:
    - ![](Images/28.png)
- So Previously, each object in training image is assigned to grid cell that contains that object's midpoint.
- With two anchor boxes, Each object in training image is assigned to grid cell that contains object's midpoint and anchor box for the grid cell with <u>highest IoU</u>. You have to check where your object should be based on its rectangle closest to which anchor box.
- Example of data:
  - ![](Images/29.png)
  - Where the car was near the anchor 2 than anchor 1.
- You may have two or more anchor boxes but you should know their shapes.
  - how do you choose the anchor boxes and people used to just choose them by hand. Maybe five or ten anchor box shapes that spans a variety  of shapes that cover the types of objects you seem to detect frequently.
  - You may also use a k-means algorithm on your dataset to specify that.
- Anchor boxes allows your algorithm to specialize, means in our case to easily detect wider images or taller ones.

### YOLO Algorithm

- YOLO is a state-of-the-art object detection model that is fast and accurate

- Lets sum up and introduce the whole YOLO algorithm given an example.

- Suppose we need to do object detection for our autonomous driver system.It needs to identify three classes:

  1. Pedestrian (Walks on ground).
  2. Car.
  3. Motorcycle.

- We decided to choose two anchor boxes, a taller one and a wide one.

  - Like we said in practice they use five or more anchor boxes hand made or generated using k-means.

- Our labeled Y shape will be `[Ny, HeightOfGrid, WidthOfGrid, 16]`, where Ny is number of instances and each row (of size 16) is as follows:

  - `[Pc, bx, by, bh, bw, c1, c2, c3, Pc, bx, by, bh, bw, c1, c2, c3]`

- Your dataset could be an image with a multiple labels and a rectangle for each label, we should go to your dataset and make the shape and values of Y like we agreed.

  - An example:
    - ![](Images/30.png)
  - We first initialize all of them to zeros and ?, then for each label and rectangle choose its closest grid point then the shape to fill it and then the best anchor point based on the IOU. so that the shape of Y for one image should be `[HeightOfGrid, WidthOfGrid,16]`

- Train the labeled images on a Conv net. you should receive an output of `[HeightOfGrid, WidthOfGrid,16]` for our case.

- To make predictions, run the Conv net on an image and run Non-max suppression algorithm for each class you have in our case there are 3 classes.

  - You could get something like that:
    - ![](Images/31.png)
    - Total number of generated boxes are grid_width * grid_height * no_of_anchors = 3 x 3 x 2
  - By removing the low probability predictions you should have:
    - ![](Images/32.png)
  - Then get the best probability followed by the IOU filtering:
    - ![](Images/33.png)

- YOLO are not good at detecting smaller object.

- [YOLO9000 Better, faster, stronger](https://arxiv.org/abs/1612.08242)

  - Summary:

  - ```
    ________________________________________________________________________________________
    Layer (type)                     Output Shape          Param #     Connected to                
    ========================================================================================
    input_1 (InputLayer)             (None, 608, 608, 3)   0                                 
    ________________________________________________________________________________________
    conv2d_1 (Conv2D)                (None, 608, 608, 32)  864         input_1[0][0]         
    ________________________________________________________________________________________
    batch_normalization_1 (BatchNorm (None, 608, 608, 32)  128         conv2d_1[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_1 (LeakyReLU)        (None, 608, 608, 32)  0     batch_normalization_1[0][0] 
    ________________________________________________________________________________________
    max_pooling2d_1 (MaxPooling2D)   (None, 304, 304, 32)  0           leaky_re_lu_1[0][0]   
    ________________________________________________________________________________________
    conv2d_2 (Conv2D)                (None, 304, 304, 64)  18432       max_pooling2d_1[0][0] 
    ________________________________________________________________________________________
    batch_normalization_2 (BatchNorm (None, 304, 304, 64)  256         conv2d_2[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_2 (LeakyReLU)        (None, 304, 304, 64)  0     batch_normalization_2[0][0] 
    _______________________________________________________________________________________
    max_pooling2d_2 (MaxPooling2D)   (None, 152, 152, 64)  0           leaky_re_lu_2[0][0]   
    ________________________________________________________________________________________
    conv2d_3 (Conv2D)                (None, 152, 152, 128) 73728       max_pooling2d_2[0][0] 
    ________________________________________________________________________________________
    batch_normalization_3 (BatchNorm (None, 152, 152, 128) 512         conv2d_3[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_3 (LeakyReLU)        (None, 152, 152, 128) 0     batch_normalization_3[0][0] 
    ________________________________________________________________________________________
    conv2d_4 (Conv2D)                (None, 152, 152, 64)  8192        leaky_re_lu_3[0][0]   
    ________________________________________________________________________________________
    batch_normalization_4 (BatchNorm (None, 152, 152, 64)  256         conv2d_4[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_4 (LeakyReLU)        (None, 152, 152, 64)  0     batch_normalization_4[0][0] 
    ________________________________________________________________________________________
    conv2d_5 (Conv2D)                (None, 152, 152, 128) 73728       leaky_re_lu_4[0][0]   
    ________________________________________________________________________________________
    batch_normalization_5 (BatchNorm (None, 152, 152, 128) 512         conv2d_5[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_5 (LeakyReLU)        (None, 152, 152, 128) 0     batch_normalization_5[0][0] 
    ________________________________________________________________________________________
    max_pooling2d_3 (MaxPooling2D)   (None, 76, 76, 128)   0           leaky_re_lu_5[0][0]   
    ________________________________________________________________________________________
    conv2d_6 (Conv2D)                (None, 76, 76, 256)   294912      max_pooling2d_3[0][0] 
    _______________________________________________________________________________________
    batch_normalization_6 (BatchNorm (None, 76, 76, 256)   1024        conv2d_6[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_6 (LeakyReLU)        (None, 76, 76, 256)   0     batch_normalization_6[0][0] 
    _______________________________________________________________________________________
    conv2d_7 (Conv2D)                (None, 76, 76, 128)   32768       leaky_re_lu_6[0][0]   
    ________________________________________________________________________________________
    batch_normalization_7 (BatchNorm (None, 76, 76, 128)   512         conv2d_7[0][0]       
    _______________________________________________________________________________________
    leaky_re_lu_7 (LeakyReLU)        (None, 76, 76, 128)   0     batch_normalization_7[0][0] 
    ________________________________________________________________________________________
    conv2d_8 (Conv2D)                (None, 76, 76, 256)   294912      leaky_re_lu_7[0][0]   
    ________________________________________________________________________________________
    batch_normalization_8 (BatchNorm (None, 76, 76, 256)   1024        conv2d_8[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_8 (LeakyReLU)        (None, 76, 76, 256)   0     batch_normalization_8[0][0] 
    ________________________________________________________________________________________
    max_pooling2d_4 (MaxPooling2D)   (None, 38, 38, 256)   0           leaky_re_lu_8[0][0]   
    ________________________________________________________________________________________
    conv2d_9 (Conv2D)                (None, 38, 38, 512)   1179648     max_pooling2d_4[0][0] 
    ________________________________________________________________________________________
    batch_normalization_9 (BatchNorm (None, 38, 38, 512)   2048        conv2d_9[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_9 (LeakyReLU)        (None, 38, 38, 512)   0     batch_normalization_9[0][0] 
    ________________________________________________________________________________________
    conv2d_10 (Conv2D)               (None, 38, 38, 256)   131072      leaky_re_lu_9[0][0]   
    ________________________________________________________________________________________
    batch_normalization_10 (BatchNor (None, 38, 38, 256)   1024        conv2d_10[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_10 (LeakyReLU)       (None, 38, 38, 256)   0    batch_normalization_10[0][0]
    ________________________________________________________________________________________
    conv2d_11 (Conv2D)               (None, 38, 38, 512)   1179648    leaky_re_lu_10[0][0]   
    ________________________________________________________________________________________
    batch_normalization_11 (BatchNor (None, 38, 38, 512)   2048        conv2d_11[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_11 (LeakyReLU)       (None, 38, 38, 512)   0    batch_normalization_11[0][0]
    _______________________________________________________________________________________
    conv2d_12 (Conv2D)               (None, 38, 38, 256)   131072      leaky_re_lu_11[0][0] 
    ________________________________________________________________________________________
    batch_normalization_12 (BatchNor (None, 38, 38, 256)   1024        conv2d_12[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_12 (LeakyReLU)       (None, 38, 38, 256)   0   batch_normalization_12[0][0]
    ________________________________________________________________________________________
    conv2d_13 (Conv2D)               (None, 38, 38, 512)   1179648     leaky_re_lu_12[0][0] 
    ________________________________________________________________________________________
    batch_normalization_13 (BatchNor (None, 38, 38, 512)   2048        conv2d_13[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_13 (LeakyReLU)       (None, 38, 38, 512)   0    batch_normalization_13[0][0]
    ________________________________________________________________________________________
    max_pooling2d_5 (MaxPooling2D)   (None, 19, 19, 512)   0           leaky_re_lu_13[0][0] 
    _______________________________________________________________________________________
    conv2d_14 (Conv2D)               (None, 19, 19, 1024)  4718592     max_pooling2d_5[0][0] 
    ________________________________________________________________________________________
    batch_normalization_14 (BatchNor (None, 19, 19, 1024)  4096        conv2d_14[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_14 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_14[0][0]
    ________________________________________________________________________________________
    conv2d_15 (Conv2D)               (None, 19, 19, 512)   524288      leaky_re_lu_14[0][0] 
    ________________________________________________________________________________________
    batch_normalization_15 (BatchNor (None, 19, 19, 512)   2048        conv2d_15[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_15 (LeakyReLU)       (None, 19, 19, 512)   0    batch_normalization_15[0][0]
    ________________________________________________________________________________________
    conv2d_16 (Conv2D)               (None, 19, 19, 1024)  4718592     leaky_re_lu_15[0][0] 
    ________________________________________________________________________________________
    batch_normalization_16 (BatchNor (None, 19, 19, 1024)  4096        conv2d_16[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_16 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_16[0][0]
    ________________________________________________________________________________________
    conv2d_17 (Conv2D)               (None, 19, 19, 512)   524288      leaky_re_lu_16[0][0] 
    ________________________________________________________________________________________
    batch_normalization_17 (BatchNor (None, 19, 19, 512)   2048        conv2d_17[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_17 (LeakyReLU)       (None, 19, 19, 512)   0    batch_normalization_17[0][0]
    _______________________________________________________________________________________
    conv2d_18 (Conv2D)               (None, 19, 19, 1024)  4718592     leaky_re_lu_17[0][0] 
    ________________________________________________________________________________________
    batch_normalization_18 (BatchNor (None, 19, 19, 1024)  4096        conv2d_18[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_18 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_18[0][0]
    ________________________________________________________________________________________
    conv2d_19 (Conv2D)               (None, 19, 19, 1024)  9437184     leaky_re_lu_18[0][0] 
    ________________________________________________________________________________________
    batch_normalization_19 (BatchNor (None, 19, 19, 1024)  4096        conv2d_19[0][0]       
    ________________________________________________________________________________________
    conv2d_21 (Conv2D)               (None, 38, 38, 64)    32768       leaky_re_lu_13[0][0]
    ________________________________________________________________________________________
    leaky_re_lu_19 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_19[0][0]
    ________________________________________________________________________________________
    batch_normalization_21 (BatchNor (None, 38, 38, 64)    256         conv2d_21[0][0]       
    ________________________________________________________________________________________
    conv2d_20 (Conv2D)               (None, 19, 19, 1024)  9437184     leaky_re_lu_19[0][0]
    ________________________________________________________________________________________
    leaky_re_lu_21 (LeakyReLU)       (None, 38, 38, 64)    0    batch_normalization_21[0][0]
    ________________________________________________________________________________________
    batch_normalization_20 (BatchNor (None, 19, 19, 1024)  4096        conv2d_20[0][0]       
    ________________________________________________________________________________________
    space_to_depth_x2 (Lambda)       (None, 19, 19, 256)   0           leaky_re_lu_21[0][0] 
    ________________________________________________________________________________________
    leaky_re_lu_20 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_20[0][0]
    ________________________________________________________________________________________
    concatenate_1 (Concatenate)      (None, 19, 19, 1280)  0         space_to_depth_x2[0][0] 
                                                                      leaky_re_lu_20[0][0] 
    ________________________________________________________________________________________
    conv2d_22 (Conv2D)               (None, 19, 19, 1024)  11796480    concatenate_1[0][0]   
    ________________________________________________________________________________________
    batch_normalization_22 (BatchNor (None, 19, 19, 1024)  4096        conv2d_22[0][0]       
    ________________________________________________________________________________________
    leaky_re_lu_22 (LeakyReLU)       (None, 19, 19, 1024)  0    batch_normalization_22[0][0]
    ________________________________________________________________________________________
    conv2d_23 (Conv2D)               (None, 19, 19, 425)   435625      leaky_re_lu_22[0][0] 
    ===============================================================================================
    Total params: 50,983,561
    Trainable params: 50,962,889
    Non-trainable params: 20,672
    _______________________________________________________________________________________________
    ```

- You can find implementations for YOLO here:

  - https://github.com/allanzelener/YAD2K
  - https://github.com/thtrieu/darkflow
  - https://pjreddie.com/darknet/yolo/

### Region Proposals (R-CNN)

- R-CNN is an algorithm that also makes an object detection.

- Yolo tells that its faster:

  - > Our model has several advantages over classifier-based systems. It looks at the whole image at test time so its predictions are informed by global context in the image. It also makes predictions with a single network evaluation unlike systems like R-CNN which require thousands for a single image. This makes it extremely fast, more than 1000x faster than R-CNN and 100x faster than Fast R-CNN. See our paper for more details on the full system.

- But one of the downsides of YOLO that it process a lot of areas where no objects are present.

- **R-CNN** stands for regions with Conv Nets.

- R-CNN tries to pick a few windows and run a Conv net (your confident classifier) on top of them.

- The algorithm R-CNN uses to pick windows is called a segmentation algorithm. Outputs something like this:

  - ![](Images/34.png)

- If for example the segmentation algorithm produces 2000 blob then we should run our classifier/CNN on top of these blobs.

- There has been a lot of work regarding R-CNN tries to make it faster:

  - R-CNN:
    - Propose regions. Classify proposed regions one at a time. Output label + bounding box.
    - Downside is that its slow.
    - [[Girshik et. al, 2013. Rich feature hierarchies for accurate object detection and semantic segmentation]](https://arxiv.org/abs/1311.2524)
  - Fast R-CNN:
    - Propose regions. Use convolution implementation of sliding windows to classify all the proposed regions.
    - [[Girshik, 2015. Fast R-CNN]](https://arxiv.org/abs/1504.08083)
  - Faster R-CNN:
    - Use convolutional network to propose regions.
    - [[Ren et. al, 2016. Faster R-CNN: Towards real-time object detection with region proposal networks]](https://arxiv.org/abs/1506.01497)
  - Mask R-CNN:
    - https://arxiv.org/abs/1703.06870

- Most of the implementation of faster R-CNN are still slower than YOLO.

- Andew Ng thinks that the idea behind YOLO is better than R-CNN because you are able to do all the things in just one time instead of two times.

- Other algorithms that uses one shot to get the output includes **SSD** and **MultiBox**.

  - [[Wei Liu, et. al 2015 SSD: Single Shot MultiBox Detector]](https://arxiv.org/abs/1512.02325)

- **R-FCN** is similar to Faster R-CNN but more efficient.

  - [[Jifeng Dai, et. al 2016 R-FCN: Object Detection via Region-based Fully Convolutional Networks ]](https://arxiv.org/abs/1605.06409)

## Special applications: Face recognition & Neural style transfer

> Discover how CNNs can be applied to multiple fields, including art generation and face recognition. Implement your own algorithm to generate art and recognize faces!

### Face Recognition

#### What is face recognition?

- Face recognition system identifies a person's face. It can work on both images or videos.
- **<u>Liveness detection</u>** within a video face recognition system prevents the network from identifying a face in an image. It can be learned by supervised deep learning using a dataset for live human and in-live human and sequence learning.
- Face verification vs. face recognition:
  - Verification:
    - Input: image, name/ID. (1 : 1)
    - Output: whether the input image is that of the claimed person.
    - "is this the claimed person?"
  - Recognition:
    - Has a database of K persons
    - Get an input image
    - Output ID if the image is any of the K persons (or not recognized)
    - "who is this person?"
- We can use a face verification system to make a face recognition system. The accuracy of the verification system has to be high (around 99.9% or more) to be use accurately within a recognition system because the recognition system accuracy will be less than the verification system given K persons. 

#### One Shot Learning

- One of the face recognition challenges is to solve one shot learning problem.
- One Shot Learning: A recognition system is able to recognize a person, learning from one image.
- Historically deep learning doesn't work well with a small number of data.
- Instead to make this work, we will learn a **similarity function**:
  - d( **img1**, **img2** ) = degree of difference between images.
  - We want d result to be low in case of the same faces.
  - We use tau T as a threshold for d:
    - If d( **img1**, **img2** ) <= T    Then the faces are the same.
- Similarity function helps us solving the one shot learning. Also its robust to new inputs.

#### Siamese Network

- We will implement the similarity function using a type of NNs called Siamease Network in which we can pass multiple inputs to the two or more networks with the same architecture and parameters.
- Siamese network architecture are as the following:
  - ![](Images/35.png)
  - We make 2 identical conv nets which encodes an input image into a vector. In the above image the vector shape is (128, )
  - The loss function will be `d(x1, x2) = || f(x1) - f(x2) ||^2`
  - If `X1`, `X2` are the same person, we want d to be low. If they are different persons, we want d to be high.
  - [[Taigman et. al., 2014. DeepFace closing the gap to human level performance]](https://www.cv-foundation.org/openaccess/content_cvpr_2014/html/Taigman_DeepFace_Closing_the_2014_CVPR_paper.html)

#### Triplet Loss

- Triplet Loss is one of the loss functions we can use to solve the similarity distance in a Siamese network.
- Our learning objective in the triplet loss function is to get the distance between an **Anchor** image and a **positive** or a **negative** image.
  - Positive means same person, while negative means different person.
- The triplet name came from that we are comparing an anchor A with a positive P and a negative N image.
- Formally we want:
  - Positive distance to be less than negative distance
  - `||f(A) - f(P)||^2  <= ||f(A) - f(N)||^2`
  - Then
  - `||f(A) - f(P)||^2  - ||f(A) - f(N)||^2 <= 0`
  - To make sure the NN won't get an output of zeros easily:
  - `||f(A) - f(P)||^2  - ||f(A) - f(N)||^2 <= -alpha`
    - Alpha is a small number. Sometimes its called the margin.
  - Then
  - `||f(A) - f(P)||^2  - ||f(A) - f(N)||^2 + alpha <= 0`
- Final Loss function:
  - Given 3 images (A, P, N)
  - `L(A, P, N) = max (||f(A) - f(P)||^2  - ||f(A) - f(N)||^2 + alpha , 0)`
  - `J = Sum(L(A[i], P[i], N[i]) , i)` for all triplets of images.
- You need multiple images of the same person in your dataset. Then get some triplets out of your dataset. Dataset should be big enough.
- Choosing the triplets A, P, N:
  - During training if A, P, N are chosen randomly (Subjet to A and P are the same and A and N aren't the same) then one of the problems this constrain is easily satisfied 
    - `d(A, P) + alpha <= d (A, N)` 
    - So the NN wont learn much
  - What we want to do is choose triplets that are **hard** to train on.
    - So for all the triplets we want this to be satisfied:
    - `d(A, P) + alpha <= d (A, N)`
    - This can be achieved by for example same poses!
    - Find more at the paper.
- Details are in this paper [[Schroff et al.,2015, FaceNet: A unified embedding for face recognition and clustering]](https://arxiv.org/abs/1503.03832)
- Commercial recognition systems are trained on a large datasets like 10/100 million images.
- There are a lot of pretrained models and parameters online for face recognition.

#### Face Verification and Binary Classification

- Triplet loss is one way to learn the parameters of a conv net for face recognition there's another way to learn these parameters as a straight binary classification problem.
- Learning the similarity function another way:
  - ![](Images/36.png)
  - The final layer is a sigmoid layer.
  - `Y' = wi * Sigmoid ( f(x(i)) - f(x(j)) ) + b` where the subtraction is the Manhattan distance between f(x(i)) and f(x(j))
  - Some other similarities can be Euclidean and Ki square similarity.
  - The NN here is Siamese means the top and bottom convs has the same parameters.
- The paper for this work: [[Taigman et. al., 2014. DeepFace closing the gap to human level performance]](https://www.cv-foundation.org/openaccess/content_cvpr_2014/html/Taigman_DeepFace_Closing_the_2014_CVPR_paper.html)
- A good performance/deployment trick:
  - Pre-compute all the images that you are using as a comparison to the vector f(x(j))
  - When a new image that needs to be compared, get its vector f(x(i)) then put it with all the pre computed vectors and pass it to the sigmoid function.
- This version works quite as well as the triplet loss function.
- Available implementations for face recognition using deep learning includes:
  - [Openface](https://cmusatyalab.github.io/openface/)
  - [FaceNet](https://github.com/davidsandberg/facenet)
  - [DeepFace](https://github.com/RiweiChen/DeepFace)

### Neural Style Transfer

#### What is neural style transfer?

- Neural style transfer is one of the application of Conv nets.
- Neural style transfer takes a content image `C` and a style image `S` and generates the content image `G` with the style of style image.
- ![](Images/37.png)
- In order to implement this you need to look at the features extracted by the Conv net at the shallower and deeper layers.
- It uses a previously trained convolutional network like VGG, and builds on top of that. The idea of using a network trained on a different task and applying it to a new task is called transfer learning.

#### What are deep ConvNets learning?

- Visualizing what a deep network is learning:
  - Given this AlexNet like Conv net:
    - ![](Images/38.png)
  - Pick a unit in layer l. Find the nine image patches that maximize the unit's activation. 
    - Notice that a hidden unit in layer one will see relatively small portion of NN, so if you plotted it it will match a small image in the shallower layers while it will get larger image in deeper layers.
  - Repeat for other units and layers.
  - It turns out that layer 1 are learning the low level representations like colors and edges.
- You will find out that each layer are learning more complex representations.
  - ![](Images/39.png)
- The first layer was created using the weights of the first layer. Other images are generated using the receptive field in the image that triggered the neuron to be max.
- [[Zeiler and Fergus., 2013, Visualizing and understanding convolutional networks]](https://arxiv.org/abs/1311.2901)
- A good explanation on how to get **receptive field** given a layer:
  - ![](Images/receptiveField.png)
  - From [A guide to receptive field arithmetic for Convolutional Neural Networks](https://medium.com/@nikasa1889/a-guide-to-receptive-field-arithmetic-for-convolutional-neural-networks-e0f514068807)

#### Cost Function

- We will define a cost function for the generated image that measures how good it is.
- Give a content image C, a style image S, and a generated image G:
  - `J(G) = alpha * J(C,G) + beta * J(S,G)`
  - `J(C, G)` measures how similar is the generated image to the Content image.
  - `J(S, G)` measures how similar is the generated image to the Style image.
  - alpha and beta are relative weighting to the similarity and these are hyperparameters.
- Find the generated image G:
  1. Initiate G randomly
     - For example G: 100 X 100 X 3
  2. Use gradient descent to minimize `J(G)`
     - `G = G - dG`  We compute the gradient image and use gradient decent to minimize the cost function.
- The iterations might be as following image:
  - To Generate this:
    - ![](Images/40.png)
  - You will go through this:
    - ![](Images/41.png)

#### Content Cost Function

- In the previous section we showed that we need a cost function for the content image and the style image to measure how similar is them to each other.
- Say you use hidden layer `l` to compute content cost. 
  - If we choose `l` to be small (like layer 1), we will force the network to get similar output to the original content image.
  - In practice `l` is not too shallow and not too deep but in the middle.
- Use pre-trained ConvNet. (E.g., VGG network)
- Let `a(c)[l]` and `a(G)[l]` be the activation of layer `l` on the images.
- If `a(c)[l]` and `a(G)[l]` are similar then they will have the same content
  - `J(C, G) at a layer l = 1/2 || a(c)[l] - a(G)[l] ||^2`

#### Style Cost Function

- Meaning of the ***style*** of an image:
  - Say you are using layer l's activation to measure ***style***.
  - Define style as correlation between **activations** across **channels**. 
    - That means given an activation like this:
      - ![](Images/42.png)
    - How correlate is the orange channel with the yellow channel?
    - Correlated means if a value appeared in a specific channel a specific value will appear too (Depends on each other).
    - Uncorrelated means if a value appeared in a specific channel doesn't mean that another value will appear (Not depend on each other)
  - The correlation tells you how a components might occur or not occur together in the same image.
- The correlation of style image channels should appear in the generated image channels.
- Style matrix (Gram matrix):
  - Let `a(l)[i, j, k]` be the activation at l with `(i=H, j=W, k=C)`
  - Also `G(l)(s)` is matrix of shape `nc(l) x nc(l)`
    - We call this matrix style matrix or Gram matrix.
    - In this matrix each cell will tell us how correlated is a channel to another channel.
  - To populate the matrix we use these equations to compute style matrix of the style image and the generated image.
    - ![](Images/43.png)
    - As it appears its the sum of the multiplication of each member in the matrix.
- To compute gram matrix efficiently:
  - Reshape activation from H X W X C to HW X C
  - Name the reshaped activation F.
  - `G[l] = F * F.T`
- Finally the cost function will be as following:
  - `J(S, G) at layer l = (1/ 2 * H * W * C) || G(l)(s) - G(l)(G) ||`
- And if you have used it from some layers
  - `J(S, G) = Sum (lamda[l]*J(S, G)[l], for all layers)`
- Steps to be made if you want to create a tensorflow model for neural style transfer:
  1. Create an Interactive Session.
  2. Load the content image.
  3. Load the style image
  4. Randomly initialize the image to be generated
  5. Load the VGG16 model
  6. Build the TensorFlow graph:
     - Run the content image through the VGG16 model and compute the content cost
     - Run the style image through the VGG16 model and compute the style cost
     - Compute the total cost
     - Define the optimizer and the learning rate
  7. Initialize the TensorFlow graph and run it for a large number of iterations, updating the generated image at every step.

#### 1D and 3D Generalizations

- So far we have used the Conv nets for images which are 2D.
- Conv nets can work with 1D and 3D data as well.
- An example of 1D convolution:
  - Input shape (14, 1)
  - Applying 16 filters with F = 5 , S = 1
  - Output shape will be 10 X 16
  - Applying 32 filters with F = 5, S = 1
  - Output shape will be 6 X 32
- The general equation `(N - F)/S + 1` can be applied here but here it gives a vector rather than a 2D matrix.
- 1D data comes from a lot of resources such as waves, sounds, heartbeat signals. 
- In most of the applications that uses 1D data we use Recurrent Neural Network RNN.
- 3D data also are available in some applications like CT scan:
  - ![](Images/44.png)
- Example of 3D convolution:
  - Input shape (14, 14,14, 1)
  - Applying 16 filters with F = 5 , S = 1
  - Output shape (10, 10, 10, 16)
  - Applying 32 filters with F = 5, S = 1
  - Output shape will be (6, 6, 6, 32)

## Extras

### Keras

- Keras is a high-level neural networks API (programming framework), written in Python and capable of running on top of several lower-level frameworks including TensorFlow, Theano, and CNTK.
- Keras was developed to enable deep learning engineers to build and experiment with different models very quickly.
- Just as TensorFlow is a higher-level framework than Python, Keras is an even higher-level framework and provides additional abstractions.
- Keras will work fine for many common models.
- Layers in Keras:
  - Dense (Fully connected layers).
    - A linear function followed by a non linear function.
  - Convolutional layer.
  - Pooling layer.
  - Normalisation layer.
    - A batch normalization layer.
  - Flatten layer
    - Flatten a matrix into vector.
  - Activation layer
    - Different activations include: relu, tanh, sigmoid, and softmax.
- To train and test a model in Keras there are four steps:
  1. Create the model.
  2. Compile the model by calling `model.compile(optimizer = "...", loss = "...", metrics = ["accuracy"])`
  3. Train the model on train data by calling `model.fit(x = ..., y = ..., epochs = ..., batch_size = ...)`
     - You can add a validation set while training too.
  4. Test the model on test data by calling `model.evaluate(x = ..., y = ...)`
- Summarize of step in Keras: Create->Compile->Fit/Train->Evaluate/Test
- `Model.summary()` gives a lot of useful informations regarding your model including each layers inputs, outputs, and number of parameters at each layer.
- To choose the Keras backend you should go to `$HOME/.keras/keras.json` and change the file to the desired backend like Theano or Tensorflow or whatever backend you want.
- After you create the model you can run it in a tensorflow session without compiling, training, and testing capabilities.
- You can save your model with `model_save` and load your model using `model_load ` This will save your whole trained model to disk with the trained weights.








<br><br>
<br><br>
These Notes were made by [Mahmoud Badry](mailto:mma18@fayoum.edu.eg) @2017