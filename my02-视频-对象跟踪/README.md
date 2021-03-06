https://www.learnopencv.com/object-tracking-using-opencv-cpp-python/

我们将学习如何以及何时使用OpenCV 3.2 - BOOSTING，MIL，KCF，TLD，MEDIANFLOW和GOTURN中提供的6种不同的跟踪器。我们还将学习现代跟踪算法背后的一般理论。
简单地说，在视频的连续帧中定位对象称为`跟踪`。

定义听起来很直观，但在计算机视觉和机器学习中，跟踪是一个非常广泛的术语，涵盖概念上相似但技术上不同的想法。例如，所有以下不同但相关的想法通常在对象跟踪下进行研究

密集光流：这些算法有助于估计视频帧中每个像素的运动矢量。
稀疏光流：这些算法，如Kanade-Lucas-Tomashi（KLT）功能跟踪器，跟踪图像中几个特征点的位置。
卡尔曼滤波：一种非常流行的信号处理算法，用于根据先前的运动信息预测运动物体的位置。该算法的早期应用之一是导弹指导！另外这里也提到，“引导阿波罗11号月球模块下降到月球的车载电脑卡尔曼滤波器”。
平移和凸轮移位：这些是用于定位密度函数的最大值的算法。它们也用于跟踪。
单个对象跟踪器：在这类跟踪器中，第一帧使用矩形标记，以指示要跟踪的对象的位置。然后使用跟踪算法在后续帧中跟踪对象。在大多数现实生活中，这些跟踪器与对象检测器结合使用。
多目标追踪算法：当我们有一个快速物体检测器的情况下，检测每个帧中的多个对象是有意义的，然后运行一个跟踪查找算法，它识别一帧中的哪个矩形对应于下一帧中的一个矩形。
跟踪与检测

如果您曾经玩过OpenCV脸部检测功能，您就可以实时了解它，您可以轻松地在每一帧中检测到脸部。那么，为什么你首先需要跟踪？我们来探讨您可能想跟踪视频中的对象的不同原因，而不仅仅是重复检测。

跟踪比检测更快：通常跟踪算法比检测算法快。原因很简单 当您跟踪在前一帧中检测到的对象时，您会了解到对象的外观。您也知道前一帧中的位置以及其运动的方向和速度。因此，在下一帧中，您可以使用所有这些信息来预测对象在下一帧中的位置，并围绕对象的预期位置进行小型搜索，以准确定位对象。一个良好的跟踪算法将使用所有关于该对象的信息，而检测算法始终从零开始。因此，在设计高效系统的同时，通常在第 n 帧上运行对象检测， 而在n-1帧之间采用跟踪算法。为什么我们不简单地检测第一帧中的对象并随后跟踪？追踪从额外的信息中获益的确实是真的，但是当他们在障碍物后面延长一段时间或者移动得如此之快以致跟踪算法无法赶上时，也可能失去对象的跟踪。跟踪算法累积误差也是常见的，边框跟踪对象慢慢地偏离其跟踪对象。为了解决这些跟踪算法的问题，检测算法经常运行。针对对象的大量示例对检测算法进行了培训。因此，他们 对物体的一般类有更多的了解。另一方面，跟踪算法更多地了解他们正在跟踪的类的具体实例。
检测失败时跟踪可以帮助：如果您在视频上运行面部检测器，并且人的脸部被物体遮挡，则脸部检测器很可能会失败。另一方面，良好的跟踪算法将处理某种程度的遮挡。


跟踪保留身份：对象检测的输出是包含对象的矩形数组。但是，没有附加对象的身份。例如，在下面的视频中，检测红点的检测器将输出与帧中检测到的所有点对应的矩形。在下一帧中，它将输出另一个矩形数组。在第一帧中，特定的点可以由阵列中的位置10处的矩形表示，并且在第二帧中可以在位置17处表示。在帧上使用检测时，我们不知道哪个矩形对应于哪个对象。另一方面，跟踪提供了一种字面连接点的方法！

OpenCV 3 Tracking API

OpenCV 3附带了一个新的跟踪API，其中包含许多单个对象跟踪算法的实现。OpenCV 3.2 - BOOSTING，MIL，KCF，TLD，MEDIANFLOW和GOTURN有6种不同的跟踪器。

注意：OpenCV 3.1具有这5个跟踪器（BOOSTING，MIL，KCF，TLD，MEDIANFLOW）的实现。OpenCV 3.0具有以下4个跟踪器（BOOSTING，MIL，TLD，MEDIANFLOW）的实现。

在我们提供算法的简要描述之前，让我们看看设置和用法。在下面的评论代码中，我们首先通过选择跟踪器类型 - BOOSTING，MIL，KCF，TLD，MEDIANFLOW或GOTURN来设置跟踪器。然后我们打开一个视频并抓住一帧。我们定义一个包含第一帧对象的边界框，并使用第一个框架和边界框来初始化跟踪器。最后，我们从视频中读取帧，只是循环更新跟踪器，以获得当前帧的新的边界框。