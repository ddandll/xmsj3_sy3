# 实验3 基于TensorFlow Lite实现的Andriod花卉识别应用

## 1. 下载原始代码

访问https://github.com/hoitab/TFLClassify.git

下载代码

![](https://github.com/ddandll/xmsj3/blob/main/%E5%AE%9E%E9%AA%8C3/1.png?raw=true)



## 2. 运行初始化代码

​	a. 选择TFLClassify/build.gradle生成整个项目。

​	b. 修改build.gradle(Module: TFL_Classify.start)中代码为：

```
compileSdkVersion 30

    defaultConfig {
        applicationId "org.tensorflow.lite.examples.classification"
        minSdkVersion 21
        //noinspection OldTargetApi
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
```

​	（将29改为30）

​	c. rebuild,现在虚拟机上运行



​	效果：

![](https://github.com/ddandll/xmsj3/blob/main/%E5%AE%9E%E9%AA%8C3/2.png?raw=true)

## 3. 向应用中添加TensorFlow Lite

​	a. 选择start模块

​	b. 右键“start”模块，或者选择File，然后New>Other>TensorFlow Lite Model

​	c. 选择finish模块中ml文件下的FlowerModel.tflite。

![](https://github.com/ddandll/xmsj3/blob/main/%E5%AE%9E%E9%AA%8C3/3.png?raw=true)

## 4. 添加代码，更改识别出的类型

​	src/main/java/org/tensorflow/lite/examples/classification/MainActivity.kt

更改后的代码：

```
 private class ImageAnalyzer(ctx: Context, private val listener: RecognitionListener) :
        ImageAnalysis.Analyzer {

        // TODO 1: Add class variable TensorFlow Lite Model
        // Initializing the flowerModel by lazy so that it runs in the same thread when the process
        // method is called.
        private val flowerModel = FlowerModel.newInstance(ctx)

        // TODO 6. Optional GPU acceleration


        override fun analyze(imageProxy: ImageProxy) {

            val items = mutableListOf<Recognition>()

            // TODO 2: Convert Image to Bitmap then to TensorImage
            val tfImage = TensorImage.fromBitmap(toBitmap(imageProxy))

            // TODO 3: Process the image using the trained model, sort and pick out the top results
            val outputs = flowerModel.process(tfImage)
                .probabilityAsCategoryList.apply {
                    sortByDescending { it.score } // Sort with highest confidence first
                }.take(MAX_RESULT_DISPLAY) // take the top results

            // TODO 4: Converting the top probability items into a list of recognitions
            for (output in outputs) {
                items.add(Recognition(output.label, output.score))
            }

            // START - Placeholder code at the start of the codelab. Comment this block of code out.
//            for (i in 0 until MAX_RESULT_DISPLAY){
//                items.add(Recognition("Fake label $i", Random.nextFloat()))
//            }
//            for (i in 0..MAX_RESULT_DISPLAY-1){
//                items.add(Recognition("Fake label $i", Random.nextFloat()))
//            }

            // END - Placeholder code at the start of the codelab. Comment this block of code out.

            // Return the result
            listener(items.toList())

            // Close the image,this tells CameraX to feed the next image to the analyzer
            imageProxy.close()
        }
```



重新运行，效果：

![](https://github.com/ddandll/xmsj3/blob/main/%E5%AE%9E%E9%AA%8C3/4.png?raw=true)



## 5. 在手机上效果

![](https://github.com/ddandll/xmsj3/blob/main/%E5%AE%9E%E9%AA%8C3/5.jpg?raw=true)

