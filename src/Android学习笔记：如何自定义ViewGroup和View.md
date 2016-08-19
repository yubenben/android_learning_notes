#概述
自定义ViewGroup和View的行为，主要是通过继承修改基类的onMeasure(),onLayout(),onDraw()三个函数。三个函数功能分别：
onMeasure():

 - 计算当前视图的大小，在layout过程中会使用
 - 调用子视同的函数计算器大小

onLayout():

 - 布局当前视图的子视图的布局

onDraw():

 - 绘制当前视图的实现。

通过三个函数的功能可以看出在实现自己的ViewGroup通常实现onMeasure()和onLayout()函数，当自定义View的时候通常实现onMeasure()和onDraw()函数。

下面分别介绍下自定义ViewGroup和View

#自定义ViewGroup
我们自定义一个ViewGroup的目的通常是为了让它的childView按照我们的需求摆放。为了达到这个目的我们要自己实现onMeasure()函数和onLayout()函数。
###onMeasure 计算view的大小
在View.java中onMeasure函数默认实现如下：

``` java
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
                getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
    }
```

通常一个自定义的ViewGroup实现：


``` java
//某个ViewGroup类型的视图  
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {  

  // 获得它的父容器为它设置的测量模式和大小  
  int sizeWidth = MeasureSpec.getSize(widthMeasureSpec);  
  nt sizeHeight = MeasureSpec.getSize(heightMeasureSpec);  
  int modeWidth = MeasureSpec.getMode(widthMeasureSpec);  
  int modeHeight = MeasureSpec.getMode(heightMeasureSpec); 
  
  for(int i = 0 ; i < getChildCount() ; i++){  
    View child = getChildAt(i);  
    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);  
  }
  
  setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec), getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));  
}
```
在onMeasure()函数中会遍历childview.measure()计算子view的大小，然后通过setMeaureDimension()函数来设置当前视图的大小（在layout过程中会使用到）  。负责设置子控件的测量模式和大小， 根据所有子控件设置自己的宽和高 。只要不是wrap_content，父容器都能正确的计算其尺寸。所以我们自己需要计算如果设置为wrap_content时的宽和高，如何计算呢？那就是通过其childView的宽和高来进行计算。


###onLayout 指定childView的位置
在onLayout()函数中完成对所有childView的位置及大小的指定。

``` java
protected void onLayout(boolean changed, int left, int top, int right, int bottom) {  

    final int count = getChildCount();
    for(i = 0; i < count; i　＋＋)　｛
           final View child  = getChildAt(i);
    　　　　final LayoutParams lp = (LayoutParams) child.getLayoutParams();
    　　　　final int width = child.getMeasureWidth();
    　　　　final int height = child.getMeasureHeight();

           //计算child的left，top位置
           ...
           child.layout(childLeft, childTop, childLeft + width, childTop + height)
    ｝
}
```

在onLayout函数中会使用childView的MeasureWidth，MeasureHeight以及在xml中设定的LayoutParams参数，计算出childView的位置。
  

###ViewGroup的LayoutParams
View通过LayoutParams类告诉其父视图它想要地大小(即，长度和宽度)。
因此，每个View都包含一个ViewGroup.LayoutParams类或者其派生类，View类依赖于ViewGroup.LayoutParams
ViewGroup子类可以实现自定义LayoutParams，自定义LayoutParams提供了更好地扩展性。 

``` java
public class CascadeLayout extends ViewGroup {

   ...


  @Override
  protected boolean checkLayoutParams(ViewGroup.LayoutParams p) {
    return p instanceof LayoutParams;
  }

  @Override
  protected LayoutParams generateDefaultLayoutParams() {
    return new LayoutParams(LayoutParams.WRAP_CONTENT,
        LayoutParams.WRAP_CONTENT);
  }

  @Override
  public LayoutParams generateLayoutParams(AttributeSet attrs) {
    return new LayoutParams(getContext(), attrs);
  }

  @Override
  protected LayoutParams generateLayoutParams(ViewGroup.LayoutParams p) {
    return new LayoutParams(p.width, p.height);
  }

  public static class LayoutParams extends ViewGroup.LayoutParams {
    
    public int verticalSpacing;

    public LayoutParams(Context context, AttributeSet attrs) {
      super(context, attrs);

      TypedArray a = context.obtainStyledAttributes(attrs,
          R.styleable.CascadeLayout_LayoutParams);
      try {
        verticalSpacing = a
            .getDimensionPixelSize(
                R.styleable.CascadeLayout_LayoutParams_layout_vertical_spacing,
                -1);
      } finally {
        a.recycle();
      }
    }

    public LayoutParams(int w, int h) {
      super(w, h);
    }

  }
}
```

主要功能就是在添加子View时为其构建了一个LayoutParams对象。但更重要的是，ViewGroup的子类可以重载上面的几个方法，返回特定的LayoutParams对象，例如：对于LinearLayout而言，则是LinearLayout.LayoutParams对象。这么做地目的是，能在其他需要它的地方，可以将其强制转换成LinearLayout.LayoutParams对象。 checkLayoutParams(),generateDefaultLayoutParams(),generateLayoutParams(),()几个函数必须实现才能在使用的时候将ViewGroup.LayoutParams强制转换成CustomViewGroup.LayoutParams。

#自定义View
有时候为了实现复杂的动画我们会通过继承View的方式来自定义一个实现。自定义过程中我们通过onMeasure()函数来测量View的大小，OnDraw()函数来实现具体的绘制工作。onMeasure()和上门的自定义ViewGroup类似。

###onDraw 绘制
通过继承View的onDraw()函数来实现绘制工作，系统给我们提供了一个Canvas类，也就是画布来具体操作

```java
protected void onDraw(Canvas canvas)  
    {  
        mPaint.setColor(Color.YELLOW);  
        canvas.drawRect(0, 0, getMeasuredWidth(), getMeasuredHeight(), mPaint);  
  
        mPaint.setColor(mTitleTextColor);  
        canvas.drawText(mTitleText, getWidth() / 2 - mBound.width() / 2, getHeight() / 2 + mBound.height() / 2, mPaint);  
    } 
```
####Canvas
Canvas主要用于2D绘图，那么它也提供了很多相应的drawXxx()方法，方便我们在Canvas对象上画画，drawXxx()具有多种类型，可以画出：点、线、矩形、圆形、椭圆、文字、位图等的图形，这里就不再一一介绍了，只介绍几个Canvas中常用的方法：

 - void drawBitmap(Bitmap bitmap,float left,float top,Paint paint)：在指定坐标绘制位图。
 - void drawLine(float startX,float startY,float stopX,float stopY,Paint paint)：根据给定的起始点和结束点之间绘制连线。
 - void drawPath(Path path,Paint paint)：根据给定的path，绘制连线。
 - void drawPoint(float x,float y,Paint paint)：根据给定的坐标，绘制点。
 - void drawText(String text,int start,int end,Paint paint)：根据给定的坐标，绘制文字。
 - int getHeight()：得到Canvas的高度。
 - int getWidth()：得到Canvas的宽度。

####Paint
从上面列举的几个Canvas.drawXxx()的方法看到，其中都有一个类型为paint的参数，可以把它理解为一个"画笔"，通过这个画笔，在Canvas这张画布上作画。 它位于"android.graphics.Paint"包下，主要用于设置绘图风格，包括画笔颜色、画笔粗细、填充风格等。

Paint中提供了大量设置绘图风格的方法，下面列出一些常用的：

 - setARGB(int a,int r,int g,int b)：设置ARGB颜色。
 - setColor(int color)：设置颜色。
 - setAlpha(int a)：设置透明度。
 - setPathEffect(PathEffect effect)：设置绘制路径时的路径效果。
 - setShader(Shader shader)：设置Paint的填充效果。
 - setAntiAlias(boolean aa)：设置是否抗锯齿。
 - setStrokeWidth(float width)：设置Paint的笔触宽度。
 - setStyle(Paint.Style style)：设置Paint的填充风格。
 - setTextSize(float textSize)：设置绘制文本时的文字大小。


#自定义资源属性
Android的资源都是通过xml管理的，为了让我们自定义的视图更方便的在xml文件中使用，我们也需要在资源文件中自定义一些属性方便使用。

编写values/attrs.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <declare-styleable name="Test">
        <attr name="text" format="string" />
        <attr name="testAttr" format="integer" />
    </declare-styleable>

</resources>
```

 我们会在使用CustomView的时候使用这些属性
 

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:zhy="http://schemas.android.com/apk/res/com.example.test"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <com.example.test.MyTextView
        android:layout_width="100dp"
        android:layout_height="200dp"
        zhy:testAttr="520"
        zhy:text="helloworld" />

</RelativeLayout>
```

然后再CustomView的构造函数中把属性解析出来

```
public class MyTextView extends View {

    private static final String TAG = MyTextView.class.getSimpleName();

    public MyTextView(Context context, AttributeSet attrs) {
        super(context, attrs);

        TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.test);

        String text = ta.getString(R.styleable.test_testAttr);
        int textAttr = ta.getInteger(R.styleable.test_text, -1);

        Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);

        ta.recycle();
    }

}
```


以上是自己学习自定义View和ViewGroup总结的笔记。
