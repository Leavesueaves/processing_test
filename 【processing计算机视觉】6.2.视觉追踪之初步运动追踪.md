# 【processing计算机视觉】6.2.视觉追踪之初步运动追踪

## 运动追踪
运动追踪，是一种快速的运动目标跟踪方法。此法采用基于时间差分的方法将运动目标从背景中提取出来。运动追踪，(Motion Tracking)意味着在视频的图像(帧)序列中找到特定对象。在后台，您可以检查对象的运动、累积路径、预期路径、速度和速度。

此案例为Golan Levin戈兰·莱文的Frame Differencing代码。通过将当前图像与上一图像进行颜色差异分析，并将差异添加到运行计数中，定义为像素移动总量。

- input:

'''
/**
 * Frame Differencing 帧差异
 * by Golan Levin. 戈兰·莱文
 *
 * Quantify the amount of movement in the video frame using frame-differencing.
 * 使用帧差分量化视频帧中的移动量
*/ 

import processing.video.*;

int numPixels;
int[] previousFrame;
Capture video;

void setup() {
  size(640, 480);
  video = new Capture(this, width, height);
  video.start(); 
  
  numPixels = video.width * video.height;
  previousFrame = new int[numPixels];  //创建一个数组来存储以前捕获的帧
  loadPixels();
}

void draw() {
  if (video.available()) {
    video.read(); //从相机中读取新帧
    video.loadPixels(); //使其像素[]阵列可用
    
    int movementSum = 0;
    for (int i = 0; i < numPixels; i++) {
      color currColor = video.pixels[i];
      color prevColor = previousFrame[i];
      //从当前像素中提取像素颜色
      int currR = (currColor >> 16) & 0xFF;
      int currG = (currColor >> 8) & 0xFF;
      int currB = currColor & 0xFF;
      //从上一个像素中提取像素颜色
      int prevR = (prevColor >> 16) & 0xFF;
      int prevG = (prevColor >> 8) & 0xFF;
      int prevB = prevColor & 0xFF;
      //比较当前像素和上一个像素的RGB像素值
      int diffR = abs(currR - prevR);
      int diffG = abs(currG - prevG);
      int diffB = abs(currB - prevB);
      //将这些差异添加到运行计数中，定义为像素移动总量
      movementSum += diffR + diffG + diffB;
      //将差异图像渲染到屏幕
      pixels[i] = color(diffR, diffG, diffB);
      
      //pixels[i] = 0xff000000 | (diffR << 16) | (diffG << 8) | diffB;
      
      //将当前颜色保存到上一个缓冲区
      previousFrame[i] = currColor;
    }
    //为了防止来自全黑（无移动）的帧的闪烁，仅当图像发生更改时才更新屏幕。
    if (movementSum > 0) {
      updatePixels();
      println(movementSum); //打印到控制台的像素移动总量
    }
  }
}
'''

- output:

![b599e0b5b8c04620b8e6a6f27206c72a](https://img-blog.csdnimg.cn/b599e0b5b8c04620b8e6a6f27206c72a.png)

备注：此案例代码来源于Frame Differencing by Golan Levin，作者为processing官方案例库。
