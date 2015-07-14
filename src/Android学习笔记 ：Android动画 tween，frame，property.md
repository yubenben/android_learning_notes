#Drawable Animation
Drawable Animation( Frame Animation ): 帧动画，像放电影一样一帧帧的播放图片的形式展现动画。
在xml中实现代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/rocket_thrust1" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust2" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust3" android:duration="200" />
</animation-list>
```
android:oneshot 为true是动画只执行一次，false时循环执行。一个item表示一个图片，android:duration 为这个帧的显示时间。
使用方法：

```java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```
不要在onCreate中调用start，因为AnimationDrawable还没有完全跟Window相关联，如果想要界面显示时就开始动画的话，可以在onWindowFoucsChanged()中调用start()。

#View Animation
View Animation （ Tween Animation ）：补间动画，指定view开始和结束的形状说明，在指定时间内渐变的动画效果，包括View的位置(position)，大小(size)，旋转(rotation)，和透明度(transparency)。只能用于View。通常使用xml实现动画。

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
</set>
```

 - set 包含动画的集合
    - android:interpolator  指定动画的渐变方式（变化速度）
    - android:shareInterpolator 为true时，集合内的元素共享一个interpolate
 - alpha 透明度变化
   - android:fromAlpha 开始的透明度，0.0代表完全透明  1.0代表完全不透明
   - android:toAlpha 结束时的透明度
 - scale 伸缩动画
   - android:fromXScale   开始的x偏移量，1.0代表没有变化
   - android:toXScale
   - android:fromYScale
   - android:toYScale
   - android:pivotX 伸缩变化时，x轴保持不变的坐标
   - android:pivotY
 - translate 垂直或者水平的运动 属性里面的值有三种格式：-100%~100%代表相对于自身的比例；-100%p~100%p表示相对于父视图的比例；没有后缀的float值就代表绝对变化量
     - android:fromXDelta   x轴开始的偏移量Float值或者百分比
     - android:toXDelta
     - android:fromYDelta
     - android:toYDelta
 - rotate 旋转动画
     - android:fromDegrees 开始的角度
     - android:toDegrees 结束的角度
     - android:pivotX  旋转圆心x轴坐标  5表示距view左边缘5像素，5%表示距view左边缘的百分比5%p表示距离父View 的左边缘的百分比
     - android:pivotY 旋转圆心y轴坐标

例子：

```xml
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false">
    <scale
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="false"
        android:duration="700" />
    <set
        android:interpolator="@android:anim/accelerate_interpolator"
        android:startOffset="700">
        <scale
            android:fromXScale="1.4"
            android:toXScale="0.0"
            android:fromYScale="0.6"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" />
        <rotate
            android:fromDegrees="0"
            android:toDegrees="-45"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" />
    </set>
</set>
```

```java
ImageView image = (ImageView) findViewById(R.id.image);
Animation hyperspaceJump = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
image.startAnimation(hyperspaceJump);
```

#Property Animation
属性动画，在Android 3.0（API 11）中引入，它更改的是对象的实际属性。在View Animation中的动画效果只是更改了View在parent中绘制的绘制参数，View的实际属性并没有变化。
##ValueAnimator
ValueAnimator包含Property Animation动画的所有核心功能，如动画时间，开始、结束属性值，相应时间属性值计算方法等。通过实现ValueAnimator.onUpdateListener接口，来设置当前对象属性。

```
ValueAnimator animation = ValueAnimator.ofFloat(0f, 1f);
animation.setDuration(1000);
animation.addUpdateListener(new AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator animation) {
        Log.i("update", ((Float) animation.getAnimatedValue()).toString());
    }
});
animation.setInterpolator(new CycleInterpolator(3));
animation.start();
```
这个接口只有一个函数onAnimationUpdate()，在这个函数中会传入ValueAnimator对象做为参数，通过这个ValueAnimator对象的getAnimatedValue()函数可以得到当前的属性值。

##ObjectAnimator
继承自ValueAnimator，要指定一个对象及该对象的一个属性，当属性值计算完成时自动设置为该对象的相应属性，即完成了Property Animation的全部两步操作。
实际应用中一般都会用ObjectAnimator来改变某一对象的某一属性，但用ObjectAnimator有一定的限制，要想使用ObjectAnimator，应该满足以下条件：

 - 对象应该有一个setter函数：set[PropertyName]（驼峰命名法）
 - 如果使用ofFloat之类的工场方法，必须要有该对象要有相应属性的getter方法get[PropertyName]
 - 如果有getter方法，其应返回值类型应与相应的setter方法的参数类型一致。

```
ObjectAnimator anim = ObjectAnimator.ofFloat(foo, "alpha", 0f, 1f);
anim.setDuration(1000);
anim.start();
```

##Animating view property
View Animation（Tween Animation）中的动画实现，是通过修改通过其Parent View实现的，在View被drawn时Parents View改变它的绘制参数，draw后再改变参数invalidate，这样虽然View的大小或旋转角度等改变了，但View的实际属性没变。在Android3.0（API 11）中给view加入了新的属性，让动画能够真正改变view 的属性。

 - translationX and translationY:    View相对于原始位置的偏移量
 - rotation, rotationX, and rotationY:    旋转，rotation用于2D旋转角度，3D中用到后两个
 - caleX,scaleY:    缩放比
 - pivotX and pivotY:     view的变化中心点位置，在rotation和scale变化中的圆心位置。默认为对象的中心点
 - x and y：    View的最终坐标，是View的left，top位置加上translationX，translationY
 - alpha:     透明度

##TypeEvalutors
根据属性的开始、结束值与TimeInterpolation计算出的因子计算出当前时间的属性值，android提供了以下几个evalutor：

 - IntEvaluator：属性的值类型为int；
 - FloatEvaluator：属性的值类型为float；
 - ArgbEvaluator：属性的值类型为十六进制颜色值；
 - TypeEvaluator：一个接口，可以通过实现该接口自定义Evaluator

```
public class FloatEvaluator implements TypeEvaluator {

    public Object evaluate(float fraction, Object startValue, Object endValue) {
        float startFloat = ((Number) startValue).floatValue();
        return startFloat + fraction * (((Number) endValue).floatValue() - startFloat);
    }
}
```
根据动画执行的时间跟应用的Interplator，会计算出一个0~1之间的因子，即evalute函数中的fraction参数,然后根据evalute值计算出当前的返回值。

```
ValueAnimator animation = ValueAnimator.ofObject(new MyTypeEvaluator(), startPropertyValue, endPropertyValue);
animation.setDuration(1000);
animation.start();
```

##Using Interpolators
Interpolators定义了动画过程中值的变化方式，如线性均匀改变，加速变快等。
在View Animation中是Interpolate，Property Animation重视TimeInterpolate。这两者是一样的，3.0之前只有Interpolate，3.0之后实现代码转移至TimeInterpolate，Interpolate继承自TimeInterpolate，内部没有任何代码。

 - AccelerateInterpolator　　　　　     加速，开始时慢中间加速
 - DecelerateInterpolator　　　 　　   减速，开始时快然后减速
 - AccelerateDecelerateInterolator　   先加速后减速，开始结束时慢，中间加速
 - AnticipateInterpolator　　　　　　  反向 ，先向相反方向改变一段再加速播放
 - AnticipateOvershootInterpolator　   反向加回弹，先向相反方向改变，再加速播放，会超出目的值然后缓慢移动至目的值
 - BounceInterpolator　　　　　　　  跳跃，快到目的值时值会跳跃，如目的值100，后面的值可能依次为85，77，70，80，90，100
 - CycleIinterpolator　　　　　　　　 循环，动画循环一定次数，值的改变为一正弦函数：Math.sin(2 * mCycles * Math.PI * input)
 - LinearInterpolator　　　　　　　　 线性，线性均匀改变
 - OvershottInterpolator　　　　　　  回弹，最后超出目的值然后缓慢改变到目的值
 - TimeInterpolator　　　　　　　　   一个接口，允许你自定义interpolator，以上几个都是实现了这个接口

##Animating Layout Changes to ViewGroups
在ViewGroup中的子视图可见性或者位置有变化的时候，可以通过LayoutTransition类对其加入动画效果。
有四种形式的容器动画：

 - APPEARING: 动画所运行的项目出现在这个容器中时，即：view显示时的动画
 - CHANGE_APPEARING: 由于在这个容器总新增加了一个view，而导致原来的view位置发生改变所以会触发这个动画。
 - DISAPPEARING: view在这个容器中消失时触发的动画
 - CHANGE_DISAPPEARING: 由于在这个容器中移除了一个view，而导致原来的view位置发生改变所以会触发这个动画。

```
mTransitioner = new LayoutTransition(); 
ObjectAnimator animOut = ObjectAnimator.ofFloat(null, "rotationX", 0f,90f).  
            setDuration(mTransitioner.getDuration(LayoutTransition.DISAPPEARING));  
mTransitioner.setAnimator(LayoutTransition.DISAPPEARING, animOut);
mLinearlayout.setLayoutTransition(mTransitioner); 
```

##AnimatorSet
AnimationSet提供了一个把多个动画组合成一个组合的机制，并可设置组中动画的时序关系，如同时播放，顺序播放等。

```
AnimatorSet bouncer = new AnimatorSet();
bouncer.play(bounceAnim).before(squashAnim1);
bouncer.play(squashAnim1).with(squashAnim2);
bouncer.play(squashAnim1).with(stretchAnim1);
bouncer.play(squashAnim1).with(stretchAnim2);
bouncer.play(bounceBackAnim).after(stretchAnim2);
ValueAnimator fadeAnim = ObjectAnimator.ofFloat(newBall, "alpha", 1f, 0f);
fadeAnim.setDuration(250);
AnimatorSet animatorSet = new AnimatorSet();
animatorSet.play(bouncer).before(fadeAnim);
animatorSet.start();
```


##KeyFrame
Keyframe是一个时间/值对，用于定义在某个时刻动画的状态。比如Keyframe.ofInt(.5f, Color.RED)定义了当动画进行了50%的时候，颜色的值应该是Color.RED。

PropertyValuesHolder保存了view的属性的信息以及在动画进行过程中该属性的值。通过 PropertyValuesHolder.ofKeyframe方法来构建PropertyValuesHolder的实例，改方法接收一个属性名以及 多个Keyframe对象作为参数。当你想通过动画改变多个属性的时候PropertyValuesHolder就非常有用。

```
Keyframe kf0 = Keyframe.ofFloat(0f, 0f);
Keyframe kf1 = Keyframe.ofFloat(.5f, 360f);
Keyframe kf2 = Keyframe.ofFloat(1f, 0f);
PropertyValuesHolder pvhRotation = PropertyValuesHolder.ofKeyframe("rotation", kf0, kf1, kf2);
ObjectAnimator rotationAnim = ObjectAnimator.ofPropertyValuesHolder(target, pvhRotation)
rotationAnim.setDuration(5000);
```

##ViewPropertyAnimator
如果需要对一个View的多个属性进行动画可以用ViewPropertyAnimator类，该类对多属性动画进行了优化，会合并一些invalidate()来减少刷新视图，该类在3.1中引入。

以下三段代码用三种形式实现同一动画效果：

Multiple ObjectAnimator objects

```
ObjectAnimator animX = ObjectAnimator.ofFloat(myView, "x", 50f);
ObjectAnimator animY = ObjectAnimator.ofFloat(myView, "y", 100f);
AnimatorSet animSetXY = new AnimatorSet();
animSetXY.playTogether(animX, animY);
animSetXY.start();
```
One ObjectAnimator

```
PropertyValuesHolder pvhX = PropertyValuesHolder.ofFloat("x", 50f);
PropertyValuesHolder pvhY = PropertyValuesHolder.ofFloat("y", 100f);
ObjectAnimator.ofPropertyValuesHolder(myView, pvhX, pvyY).start();
```

ViewPropertyAnimator

```
myView.animate().x(50f).y(100f);
```

##Declaring Animations in XML

```
<set
  android:ordering=["together" | "sequentially"]>

    <objectAnimator
        android:propertyName="string"
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <animator
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <set>
        ...
    </set>
</set>
```

```
<set android:ordering="sequentially">
    <set>
        <objectAnimator
            android:propertyName="x"
            android:duration="500"
            android:valueTo="400"
            android:valueType="intType"/>
        <objectAnimator
            android:propertyName="y"
            android:duration="500"
            android:valueTo="300"
            android:valueType="intType"/>
    </set>
    <objectAnimator
        android:propertyName="alpha"
        android:duration="500"
        android:valueTo="1f"/>
</set>
```

```
AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext,
    R.anim.property_animator);
set.setTarget(myObject);
set.start();
```