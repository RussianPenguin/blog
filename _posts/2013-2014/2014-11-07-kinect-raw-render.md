---
layout: post
title: 'Kinect: Приведение координат сенсора в метрические'
type: post
categories:
- JFF
- kinect
tags:
- 3d
- ogre3d
permalink: "/2014/11/07/kinect-%d0%bf%d1%80%d0%b8%d0%b2%d0%b5%d0%b4%d0%b5%d0%bd%d0%b8%d0%b5-%d0%ba%d0%be%d0%be%d1%80%d0%b4%d0%b8%d0%bd%d0%b0%d1%82-%d1%81%d0%b5%d0%bd%d1%81%d0%be%d1%80%d0%b0-%d0%b2-%d0%bc%d0%b5%d1%82%d1%80/"
---
В одниокм из своих проектов с использованием сенсора пришлось задаваться вопросом: как привиодить координаты сенсора (глубину) в координаты метрические. Если этого не сделать, то прямые углы у стен прямыми не будут.

Будет это выглядеть как на картинке ниже.

![Kinect: сырой рендер (без преобразования в метрические координаты)]({{ site.baseurl }}/assets/images/2014/11/tutorialapplication-render-window_113.png){:.img-fluid}

А как это сделать?

В пакете freenect есть демка glpclview в коде которой можно увидеть матрицу преобразований координат сенсора в нужные нам координаты.

```cpp
// Do the projection from u,v,depth to X,Y,Z directly in an opengl matrix  
// These numbers come from a combination of the ros kinect_node wiki, and  
// nicolas burrus' posts.  
void LoadVertexMatrix()  
{  
 float fx = 594.21f;  
 float fy = 591.04f;  
 float a = -0.0030711f;  
 float b = 3.3309495f;  
 float cx = 339.5f;  
 float cy = 242.7f;  
 GLfloat mat[16] = {  
 1/fx,     0,  0, 0,  
 0,    -1/fy,  0, 0,  
 0,       0,  0, a,  
 -cx/fx, cy/fy, -1, b  
 };  
 glMultMatrixf(mat);  
}
```

Не будем заострять внимание на очень понятных комментариях в коде :), а попробуем понять, чтоже эта штука делает.

Сходу информации почти нет - гугл говорит, что это есть лишь приведение координат согласно калибровочным данным самого кинекта ([тыц](https://groups.google.com/forum/#!msg/openkinect/c7OvB0GqNjU/z-4hbz4SdJYJ "Матрица преобразований координат сенсора kinect в реальные")).

Но это нифига не проясняет.

Дальнейшее гугление нашло [пруф](http://answers.ros.org/question/67339/converting-kinect-depth-image-to-real-world-coordinate/ "Converting Kinect depth image to Real world coordinate.") на форуме ROS, а так же [пруф](https://groups.google.com/forum/#!topic/openkinect/ihfBIY56Is8 "Depth to Real World XY Coordinate") в гуглогруппах.

В итоге на скорую руку был состряпан код, который делает нужное преобразование.

```cpp
/**  
 * Преобразует глубину в реальное значение (в миллиметрах)  
 */  
double raw_depth_to_millimeters(int depth_value){  
 double depth_value_f = (float) depth_value;  
 if (depth_value < 2047){  
 float depth = 1000.0 / (depth_value_f  * -0.0030711016 + 3.3309495161);  
 return depth;  
 }  
 return 0.0f;  
}

/**  
 * Преобразует вируальную точку point в точку с реальными координатами (в миллиметрах)  
 */  
Ogre::Vector3 depth_to_realword(Ogre::Vector3 point){  
 double fx_d = 1.0 / 5.9421434211923247e+02;  
 double fy_d = 1.0 / 5.9104053696870778e+02;  
 double cx_d = 3.3930780975300314e+02;  
 double cy_d = 2.4273913761751615e+02;

double depth = raw_depth_to_millimeters(point.z);

return Ogre::Vector3(  
 (point.x - cx_d) * depth * fx_d,  
 (point.y - cy_d) * depth * fy_d,  
 depth);  
}
```

Про этот код важно помнить, что координаты x и y должны назодиться в первой четверти (больше нуля).

Теперь изображение выглядит куда лучше. :)

![Изображение с сенсора kineck после преобразования в метрические координаты.]({{ site.baseurl }}/assets/images/2014/11/tutorialapplication-render-window_112.png){:.img-fluid}

