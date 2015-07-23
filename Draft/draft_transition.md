# Transition 总结

> Android 5.0 (API level 21) supports these enter and exit transitions:

> explode - Moves views in or out from the center of the scene.
> slide - Moves views in or out from one of the edges of the scene.
> fade - Adds or removes a view from the scene by changing its opacity.


> Android 5.0 (API level 21) also supports these shared elements transitions:

> changeBounds - Animates the changes in layout bounds of target views.
> changeClipBounds - Animates the changes in clip bounds of target views.
> changeTransform - Animates the changes in scale and rotation of target views.
> changeImageTransform - Animates changes in size and scale of target images.

>  Activity and Fragment animations could already be specified in previous platform versions using the Activity#overridePendingTransition() and FragmentTransaction#setCustomAnimation() methods
 
 ---------------
 
 ### BAD NEWS
 
 #ViewAnimationUtils  
 Added in API level 21
 
 #TransitionManager   
 Added in API level 19
 
 
 #Getting Started with Activity & Fragment Transitions
 [http://www.androiddesignpatterns.com/2014/12/activity-fragment-transitions-in-android-lollipop-part1.html](http://www.androiddesignpatterns.com/2014/12/activity-fragment-transitions-in-android-lollipop-part1.html)
 
 #TransitionSample  
 [https://possiblemobile.com/2013/11/new-transitions-framework/](https://possiblemobile.com/2013/11/new-transitions-framework/)
 
>  #Activity / Fragment  
> 
> final LinearLayout view = (LinearLayout)findViewById(R.id.easy_root);
> TransitionManager.beginDelayedTransition(view);
  

>  #SceneToScene Activity  
> 
> RelativeLayout sceneBase = (RelativeLayout)findViewById(R.id.scene_base);
> ViewGroup scene2Group = (ViewGroup)getLayoutInflater().inflate(R.layout.scene_2, sceneBase, false);  
> final Scene scene2 = new Scene(sceneBase, scene2Group);
        TransitionManager.go(scene2);
        
 # Layout Transitions
 [https://www.youtube.com/watch?v=55wLsaWpQ4g](https://www.youtube.com/watch?v=55wLsaWpQ4g)
 
 
 # PreLollipopTransition 现在要看的就是之前版本如何使用
 [https://github.com/takahirom/PreLollipopTransition](https://github.com/takahirom/PreLollipopTransition)
 
 # SwipyRefreshLayout 我觉得这东西会有用的
 [https://github.com/OrangeGangsters/SwipyRefreshLayout](https://github.com/OrangeGangsters/SwipyRefreshLayout)
 
 # 国内 的Material收集
 [https://github.com/lightSky/Awesome-MaterialDesign](https://github.com/lightSky/Awesome-MaterialDesign)

### Question
a question from stackoverflow  
[http://stackoverflow.com/questions/27344357/android-5-activity-transition-on-lower-api](http://stackoverflow.com/questions/27344357/android-5-activity-transition-on-lower-api)
 

