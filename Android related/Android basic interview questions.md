## Android basic interview questions

**1. What is ANR how to avoid it? **

A: On Android, if your application has not responded sensitively for a while, the system will display a dialog box to the user. This dialog box is called
Use the Program Not Responding (ANR) dialog box.
Users can choose to let the program continue to run, but they are using your
When you use an application, you don't want to process this dialog every time. therefore
In the program, the design of response performance is very important, so the system will not display
Show ANR to the user.

Different components have different ANR times. The Activity is 5 seconds, the BroadCastReceiver is 10 seconds, and the Service is 20 seconds (all in the foreground).

solution:

Will all time-consuming operations, such as access to the network, Socket communication, query large
SQL statements, complex logic calculations, etc. are placed in the child thread, of course
The UI is updated by means of handler.sendMessage, runonUITread, AsyncTask, and so on. In any case, make sure the user interface is smooth.
degree. If the time-consuming operation requires the user to wait, then the degree bar can be displayed on the interface.
    
**2. What are the Activity and Fragment life cycles? **

![image](https://upload-images.jianshu.io/upload_images/2893137-d63537703193a6d1.png?imageMogr2/auto-orient/)

![image](http://www.jackywang.tech/AndroidInterview-Q-A/picture/fragment-life.png)
    
**3. The life cycle of the activity when the horizontal and vertical screens are switched**

When the android:configChanges of the Activity is not set, the cut screen will re-enter each life cycle, and will be executed once when the horizontal screen is cut, and will be executed twice when the vertical screen is cut.
When setting the android:configChanges=”orientation” of the Activity, the cut screen will still call each life cycle, and the switch will only be executed once.
When setting the android:configChanges="orientation |keyboardHidden" of the Activity, the cut screen will not recall each life cycle, only the onConfigurationChanged method will be executed.

**4. AsyncTask defects and problems**
    
About thread pool:

The thread pool ThreadPoolExecutr corresponding to asynctask is shared within the scope of the process and is static, so asynctsk controls all subclass instances in the process scope. Because of this limitation, when using the default thread pool, if the number of threads exceeds the maximum capacity of the thread pool, the thread pool will burst (the default serial execution after 3.0, there will be no problem). For this situation, you can try to customize the thread pool, used with synctask.

About the default thread pool:

The maximum number of CPU_COUNT+1 in the core thread pool is CPU_COUNT*2+1, and the maximum waiting queue for the thread waiting queue is 28, but the thread pool can be customized. The thread pool is managed by AsyncTask. The thread pool allows tasks to run in parallel. It is necessary to pay attention to the consistency of data under concurrent conditions. New data may be overwritten by old data like volatile variables. So if you want the tasks to run in series, use SERIAL_EXECUTOR.
    
The difference between AsyncTask in different SDK versions:

Calling AsyncTask's excute method can't immediately execute the program. Reasons for analysis and improvement. By consulting the official documentation, it is found that when AsyncTask is first introduced, asynchronous services are executed sequentially in a separate thread, that is, only one task is executed at a time, and cannot be paralleled. Execution, starting from 1.6, AsyncTas introduced a thread pool, supporting the execution of 5 asynchronous tasks at the same time, that is, only 5 threads can run, more than the thread can only wait, waiting for the thread before the execution is completed Schedule and run. In other words, if the number of AsyncTask instances in a process exceeds five, then if the first 5 are running for a long time, then the sixth can only wait for an opportunity. This is a limitation of syncTask and cannot be solved for versions prior to 2.3. If your application requires a large number of background threads to perform tasks, you can only abandon the use of AsyncTask and create your own thread pool to manage Threa. I have to say that although AsyncTask is more convenient to use than Thread, it can only run 5 threads at the same time, which greatly limits its power. You must carefully design your application, stagger the time to use AsyncTask, and try to divide it. At the time, or if the guaranteed number will not be greater than 5, you will encounter the problem mentioned last time. It may be that Google is aware of the limitations of AsynTask. From Android 3.0, there are some adjustments to the AsyncTask API: each time only one thread is started to execute a task, and then the second task is executed, which is equivalent to only one. The background line is executing the submitted task.

Life cycle

Many developers will think that an AsyncTask created in an Activity is destroyed as the Activity is destroyed. However, it is not. AsynTask will execute until the doInBackground() method is executed. Then, if cancel(boolean) is called, the onCancelled(Result result) method will be executed; otherwise, the onPostExecuteResult result) method will be executed. If we cancel the AsyncTask before our Activity is destroyed, it is possible to crash our AsyncTask. Because the view it wants to process is gone. Therefore, we must ensure that the task is cancelled before the destruction activity. In summary, we need to ensure that AsyncTask is properly canceled using AyncTask.

2. Memory leak

If AsyncTask is declared as a non-static inner class of Activity, AsyncTask will keep a reference to the Activity. If Activty has been destroyed, the background thread of AsyncTask is still executing, it will continue to keep this reference in memory, resulting in the Activity can not be recycled, resulting in memory leaks.

3. The result is lost

Screen rotation or Activity is killed by the system in the background, etc. will cause Actvity to re-create, the previously run AsyncTask will hold a reference to the previous activity, this reference has been invalid, then call onPostExcute () and then update the interface will no longer Effective.

4. Parallel or serial

In versions prior to Android 1.6, AsyncTask was serial, and in version 1.62.3, it was changed to parallel. The version after 2.3 has been modified to support parallel and serial. When you want to execute serially, you can directly execute the execute() method, if you need to execute executeOnExecutor(Excutor).

**5.onSaveInstanceState() and onRestoreIntanceState()**

When the user or programmer actively destroys an Activity, it will not fall.
Other situations will be mobilized to save the interface information. If the finish() in the code or the user presses back, it will not be used.

**6.Android process priority? **

1. The foreground process:

That is, the Activity that the user is interacting with or the Service used by Activy, etc., if the system is out of memory, the foreground process is the most killed.

2. Visible process:

It can be Activit in pause state (onPause) or Service bound to it, which is visible to the user, but cannot interact with the user due to missing focus.

3. Service process:

The ervice that is started by using the startService method is not visible to the user, but it is the user's concern. For example, the user is listening to music on a non-music interface or a file that is being downloaded on a non-download page; when the system wants space to run before the two The process will be terminated

4. Background process:

Which runs the execution of the onStop method, but it is not the user's current concern, such as QQ hanging in the background, this process system is killed first if there is no memory

5. Empty process:

A process that does not contain any of the application's program components. The kind of process system is generally not allowed to exist.

**7.Serializable and Parcelable**

Serialization, which means that an object is converted to a storable or transportable state. The serialized object can be transmitted over the network or stored to the ground.

Serializable (Java comes with):

Serializable is the meaning of serialization, which means converting an object into a stored or transportable state. Serialized objects can be transferred over the network or stored locally.

Parcelable (for android only):

In addition to Serializable, the use of Parcelable can also achieve the effect of the phase, but unlike the serialization of objects, the implementation of the Parcelable method is to decompose a complete object, and each part after decomposition is the data supported by the Intent. Type, which also implements the function of passing objects.

**8. Animation**

- tween tween animation. By specifying the first and last state of the View and the change time mode, a series of graphic transformations are performed on the content of the View to achieve an animation effect. Alpha, Scale, Translate, Rotate.
- frame frame animation. AnimationDrawable controls the animation-list.xml layout
- PropertyAnimation property animation 3.0 is introduced, the core idea of ​​property animation is the change of value.

The Property Animation animation has two steps:

1. Calculate the attribute value

2. Set the property values ​​for the properties of the target object, ie apply and refresh the animation
    
![image](https://upload-images.jianshu.io/upload_images/2893137-fe2225f697a60433.png?imageMogr2/auto-orient/)
    
The calculation properties are divided into 3 processes:

Process one:

Calculating the completed animation score elapsed fraction To execute an animation, you need to create a ValueAnimator and specify the start, end, and duration of the target object's properties. During the entire animation after calling start, ValueAnimator calculates a score between 0 and 1 based on the finished animation time, representing the percentage of completed animation for that animation. 0 means 0% and 1 means 100%.

Process two:

Calculate interpolation (animation rate of change) interpolatedfraction When ValueAnimator calculates the completed animation score, it will call the currently set TimeInterpolator to calculate an interpolated score. During the calculation, the completed percentage will be added to the new In the interpolation calculation.

Process three:

Calculating Property Values ​​When the interpolation score is calculated, ValueAnimator calls the appropriate TypeEvaluator to calculate the value of the property in motion based on the interpolation score.
The above analysis introduces two concepts: the completed elapsed fraction (elapsedfraction) and the interpolated fraction (interpolated fraction).

Principles and features:

1. The basic principle of animation:

In fact, it is to use the interpolator and the estimator to calculate the properties of the View at each moment, and then change the properties of the View to the animation of the View.

2.View animation:

Just the image changes, the actual position of the view is still in the original place.

3. Frame animation:

An animation that uses AnimatonDrawable to play after defining a series of images in xml.

4.View property animation:

1. Interpolator: The effect is to calculate the percentage of attribute change based on the percentage of time elapsed.

2. Valuator: A class that calculates the value of the attribute from the basis of 1

**9.Context related**

- Activity and Service and Application Context are different, Activity inherits from ContextThemeWraper. Others inherit from CntextWrapper.

- Each Activity and Service and Application Context is a new ContextImpl object

- getApplication() is used to get the Application instance, but the method can only be called in the Activity and Service. In most cases, we will use Application in Activity or Servic, but if you want to get an instance of Application in some other scenarios, such as BroadcastReceiver, you can use getApplicationContext() method, getApplicationContext( ) The scope of the getApplication() method is much broader. Any instance of the Context can get our Application object by calling the getApplicationContext() method.

- Create Toast and dialogs. You can't use the context of the Application. You can only use the context of the Activity.

- The number of Context is equal to the number of Activity + the number of Service +1, this 1 is Application

**10. New features in Android versions**

Android5.0 new features

- MaterialDesign design style
- Support multiple devices
- Support for 64-bit ART virtual machine

Android6.0 new features

- Dynamic rights management
- Support fast charging switching
- Support folder drag and drop application
- Camera added professional mode

Android7.0 new features

- Split screen multitasking
- Enhanced Java8 language mode
- night mode

Android8.0 new features

- Picture in picture
- Notification sign
- Autofill frame
- System Optimization
- Background restrictions
- Wait a lot to optimize
    
**11.Json**

The full name of JSON is JavaScript Object Notation, which is the JavaScript object notation.
JSON is a syntax for storing and exchanging textual information, similar to XML, but smaller, faster, and easier to parse than XM
JSON is a lightweight text data exchange format, independent of language, with my descriptive, easier to understand
An object can contain multiple name/value pairs, such as:

    {"name":"zhangsan" , "age":25}
Parse using Google's GSON package
Introducing dependencies in Android Studio:

    Compile 'com.google.code.gson:gson:2.7'
It is worth noting that the variable name in the entity class must be the same as the value name in json.
Json1 analysis
Our entity class here is Student.class

    Gson gson = new Gson();
    Student student = gson.fromJson(json1,Student.class);
    
Json2 analysis
We can parse into an int array or parse it into an Integer's List.
Parse into an array:

    Gson gson = new Gson();
    Int[] ages = gson.fromJson(json2, int[].class);
    
Parse into a List:

    Gson gson = new Gson();
    List<Integer> ages = gson.fromJson(json2, newTypeToken<List<Integer>>(){}.getType);
    
Json3 analysis
It can also be parsed into a List or an array, and we will parse it directly into a List.

    Gson gson = new Gson();
    List<Student> students = gson.fromJson(json3, newTypeToke<List<Student>>(){}.getType);

**12.android What kind of xml class is parsed, which one is recommended? And their principles and differences**

DOM analysis

advantage:

1. The XML tree is completely stored in memory, so its data and structure can be directly modified.

2. You can access any node in the XML tree at any time through the parser.

3. The API of the DOM parser is relatively simple to use.

Disadvantages:

If the XML document is relatively large, reading the document into memory is not a consumption of system resources.

scenes to be used:

- DOM is the official W3C standard for representing XML documents in a platform- and language-independent manner.
- DOM is a collection of nodes organized in a hierarchy. This hierarchy allows people to look for specific information in the tree. Analysis of the structure usually requires loading the entire document and constructing the hierarchy before any work can be done.
- DOM is a base object hierarchy.

SAX analysis

advantage:

SAX's memory requirements are lower because it allows developers to decide which tags to process themselves. Especially when developers only need to process some of the data contained in the document, SAX's ability to scale is better reflected.

Disadvantages:

When using the SAX method for XML parsing, it needs to be executed sequentially, so it is difficult to access different data in the same document. In addition, the parsing encoding process based on this method is also relatively complicated.

scenes to be used:

This method is very effective when the amount of data contained is very large, and it is not necessary to traverse or analyze all the data rows of the document. This method does not read the entire document into the memory, but only needs to read the document label required by the program. You can do it.

Xmlpull analysis

The android SDK provides xmlpullapi, xmlpull and sax similar, is based on the stream operation file, and then according to the node event callback developer to write the handler. Because it is the base stream processing, so xmlpull and sax are more memory resources, not It will be like dom to show all nodes in memory in the form of oak. xmpull is more concise than sax, and does not need to scan the entire stream.

**13.The difference between Jar and Aar**

There is only code in the Jar package. There is not only code in aar but also code and resource files, such as drawable files and xml resource files. For some AndroidLibrary that doesn't change very often, we can directly reference aar to speed up compilation.
    
**14. What is the memory size allocated by Android for each application**

Android program memory is generally limited to 16M, and some are 24M. In recent years, mobile phones have developed rapidly, and generally allocate about two hundred megabytes, which is related to specific models.
    
**15. Update UI mode**

- Activity.runOnUiThread(Runnable)
- View.post(Runnable), View.postDelay(Runnable, long)
- Handler
- AsyncTask
- Rxjava
- LiveData

**16.ContentProvider usage method. **

Cross-process communication to achieve data interaction and sharing between processes. The instance is obtained by getContentResolver() in the Context, and the data is added, deleted, and changed by Uri matching. The ContentProvider organizes the data in the form of a table. Regardless of the source of the data, ConentProvider considers it a table and then organizes the data into tables.

**17.Thread, AsyncTask, IntentService usage scenarios and features. **

1. Thread thread, running independently with Activity, when the Activity is finished, if it does not actively stop Thread or the run method is not executed, it will continue to execute.

2. AsyncTask encapsulates two thread pools and one Handler, (SerialExecutor is used for queuing, THREAD_POOL_EXECUTOR is the real execution task, Handler switches the worker thread to the main thread), it must be created in the UI thread, and the execute method must be in the UI thread. In execution, a task instance is only allowed to execute once, and multiple exceptions are thrown for network requests or simple data processing.

3. IntentService: handles asynchronous requests, implements multi-threading, and processes time-consuming operations in onHandleIntent. Multiple time-consuming tasks are executed in sequence, and the execution ends automatically.

**18. The role of Merge, ViewStub. **

Merge: Reduce the view hierarchy, remove redundant levels, and optimize the UI.

ViewStub: Load on demand, reduce memory usage, speed up rendering, does not support the merge tag
    
**19. What are the advantages and disadvantages of Json? **

advantage

- Lightweight data exchange format
- Easy reading and writing
- Easy machine parsing and generation
-
Disadvantage

- Poor semantics, not as good as xml
    
**20. What are the two types of animation, and what are the characteristics? **

Traditional animation: frame animation and tweened animation.

Property animation.

the difference

The property animation is the real realization of the movement of the view. The motion of the tween animation is more like drawing a shadow in different places. The actual object is still in the original place.
When the animation's repeatCount is set to an infinite loop, if the animation is not stopped in time when the Activity exits, the property animation will cause the Activity to be unreleased and cause a memory leak, while the tween animation is fine.
The tween animation implemented by the xml file has a very high reuse rate. In the Activity switch, the window pops up and other scenarios have a very good effect.
Be careful when using frame animations. Don't use too many extra large images, which can lead to insufficient memory.
    
**21. The difference between the Asset directory and the res directory. **

Assets: The corresponding tags will not be generated in the R file. The resources stored here will be packaged in the program installation package. (Access these files through the AssetManager class)

Res: The id tag will be generated in the R file. If the resource is used during packaging, it will be packaged into the installation package. If it is not used, it will not be inserted into the installation package.

Res/anim: store animation resources

Res/raw: When packaged like the file under asset, it will be directly into the program installation package (will be mapped to the R file)
    
** 22. How does Android speed up the activity? **

- Time-consuming operations are not performed in onCreate()
Subdivide the View displayed on the page and display it in the AsyncTask step by step. It is better to use Handler. In this way, the user sees the display of the Views with layers and steps, not the first to see a black screen, and then all the Views. It's best to make an animation and the effect is more natural.
- The goal of using multithreading is to reduce the time of onCreate() and onReume() as much as possible so that the user can see the page and manipulate the page as soon as possible.
- Reduce the main thread blocking time
- Improve the efficiency of Adapter and AdapterView
- Optimize layout files
    
**23.Handler mechanism**

The role of each part of the Handler:

1.MessageQueue: Reading will automatically delete messages, and there are advantages in singular list maintenance, insertion and deletion. In its next(), it will loop indefinitely, and if there is a message, it will return this message and remove it.

2.Looper: When the Looper is created, a MessageQueue will be called to call the loop() method. The loop is started. Loop() is also a loop. It will continue to call the next() of the messageQueue. If there is a message, it will be blocked in the messageQueue. Next(). When the Looper's qit() is called, it will call the quit() of the messageQueue. At this point, nxt() will return null, and then the loop() method will also exit.

3.Handler: Construct a Handler on the main thread, and then call sendMessage() on other lines. At this time, a message will be inserted into the MessageQueue of the main thread and then used by Looper.

4. The main thread of the system opens the thread in the main thread of ActivityThread, which defines the internal class Activity.H defines a series of message types, including the start and stop of the four major components.

Android's message mechanism is also the handler mechanism. When creating a handler, a looper is created (created by looper.prepare()). Looper is usually the main thread looper.
The handler sends a message (sendMessage) via send. Of course, the post method is finally implemented by send. In the send method, the handler inserts a message into the message queue MessageQueue through the enqueueMessage(msg,millis) in the enqueueMessage() method. Will pass its own handler through msg.target = this.

Looper is an infinite loop, constantly reading the message queue, the loop method will call the next method of MessageQueue to get a new message, the next operation is a blocking operation, when there is no message, the next method will block, which leads to loop Blocking all the time, when there is a message, Looper will process the message. Looper will start processing the message after receiving the message: msg.target.dispatchMessage(msg), of course, the msg.target here is the handler that sent the message from above. Object, so the message sent by the handler is finally handed over to his dispatchMessage method to handle it. The difference here is that the dispatchMessage method of the handler is executed in the Looper used when creating the Handler, so the code logic is successfully switched to The main thread.


The process of processing a Handler is: First, check whether the callback of Message is null. If it is not null, the message is processed by handleCallBack. The callback of Message is a Runnable object, which is actually the Runnable parameter passed by the handler's post method. Check if mCallback is null. If it is not null, call mCallback's handleMessage method to process the message.
    
The Android message loop diagram is as follows:

![image](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/native/process/android_handler_structure.png)
    
The main roles involved are as follows:

Message: A message that is divided into hardware-generated messages (eg, buttons, touches) and software-generated messages.

MessageQueue: Message queue, mainly used to add messages and take messages to the message pool.

Looper: A message circulator that is mainly used to distribute messages to the appropriate handlers.
Handler: A message processor that sends various messages to the message queue and processes various messages.

The looping process of the entire message is still relatively clear, specifically:

The Handler sends a message to the message queue MessageQueue via sendMessage().
Looper continuously extracts the Message of the trigger condition through loop(), and passes the Message to the corresponding target handler for processing.
The target handler calls its own handleMessage() method to handle the Message.
In fact, in the entire process of the message loop, not only the Java layer is involved, but many important tasks are done in the C++ layer. Let's take a look at the calling relationships of these classes.

![image](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/native/process/android_handler_class.png)

Note: The dotted line indicates the relationship, and the solid line indicates the calling relationship.

In these classes, MessageQueue is a bridge between Java layer and C++ layer. The related functions of MessageQueue and Looper are completed by the Native method of MessageQueue. Other classes connected by dashed lines have only associations, and there is no directly called relationship. They are associated. The bridge is a MessageQueue.
    
Https://www.zhihu.com/question/34652589
https://github.com/xitu/gold-miner/blob/master/TODO/android-handler-internals.md
Http://blog.csdn.net/guolin_blog/article/details/9991569
Http://droidyue.com/blog/2015/11/08/make-use-of-handlerthread
Http://blog.csdn.net/luoshengyang/article/deta6817933


**24. Can program A receive the broadcast of program B? How does WeChat communicate with the current program when accessing WeChat payment? **

**25. Why do you need to rewrite the hashcode method while rewriting the equals method, the former is the same as the latter, and vice versa? why? **

**26. Great changes between Android4.0~8.0, how to deal with it? **

**27. The level of processes in Android, and their respective differences. **

1, the foreground process

This process is required for what the user is currently doing. If one of the following pieces is met, a process is considered to be the foreground process:

1). This process has an Activity that is interacting with the user (this Ativity's onResume() method is called).

2). This process has a Service bound to activit that is interacting with the user.

3). This process has a service running in the foreground (service adjusts the method startForeground()).

4). This process has a Servie that is executing any of its lifecycle methods (onCreate(), onStart(), or onDestroy()).

5). This process has a BroadcatReceiver that is executing its onReceive() method.

Usually, at any point in time, only a few foreground processes exist. They will be killed when they reach a contradiction that cannot be blended - if the memory is too small to continue running. Usually, by this time, the device has reached an internal paging scheduling state, so it is necessary to kill some foreground processes to ensure the response of the user community.

2, visible process

A process does not have components running in the foreground, but it still affects what you see. The process is visible when the following conditions are met:

This process has an Activity that is not in the foreground but still visible (its onause() method is called). This happens when a foreground activity starts a pair of boxes.

3. Service process

A visible process is considered to be extremely important. And, unless you only kill it, you can guarantee that all foreground processes will run, otherwise you can't move it.

This process has a Service bound to the visible activity.

A process is not in the above two, but it runs a service started by startSevice().

Although a service process does not directly affect what the user sees, they usually have something that the user cares about (such as playing music or downloading data), and the system does not kill the foreground process and the visible process when it does not survive.

4, background process

A process has an activity that is currently invisible (the activity's ontop() method is called).

Such processes do not directly affect the user experience, so the system can kill them at any time to provide storage for the foreground, visible, and service processes. There are usually many background processes running. They are saved in an LRU (least recently used) list to ensure that the process with the activity that was recently seen is finally killed. If an activity correctly implements its lifecycle method and saves its current state, then the process that killed it will not affect the user's visual experience. Because when the user returns to this activity, this activity will restore all its visible state.
    
5, empty process

A process does not have any active components.

The only reason to keep such a process is the cache, which speeds up the startup of the next component when it is running. The system often kills the entire system between the process cache and the underlying kernel cache.

**28. Thread pool related knowledge. **

Thread pools in Android are thread pools that implement different features between ThreadPoolxecutor or indirectly. The most common classes in Android have thread pools of different characteristics: FixThreadPool, CachedhreadPool, SingleThreadPool, ScheduleThreadExecutr.

1).FixThreadPool

Only core threads, and a fixed number, will not be reclaimed. When all lines are active, because the queue has no limit size, the new task will wait for execution.

Advantages: Faster response to external requests.

2).SingleThreadPool

There is only one core thread, ensuring that all tasks are completed in order in the same thread. Therefore there is no need to deal with thread synchronization issues.

3).CachedThreadPool

Only non-core threads, the maximum number of threads is very large, all threads will create new threads for new tasks when they are active, otherwise they will use idle threads (60s idle time, will be recycled after the expiration, so there are 0 threads in the thread pool) Possible) to handle the task.
    
Advantages: Any task will be executed immediately (the task queue SynchronousQuue is equivalent to an empty set); it is more suitable for executing a large number of less time-consuming tasks.

4).ScheduledThreadPool

The number of core threads is fixed, and the non-core threads (the number of cores that are idle and will be reclaimed immediately is not limited.

Advantages: performing scheduled tasks and repetitive tasks with fixed cycles

**29. Memory leaks, how to find, how to generate memory leaks. **

1). Memory leak caused by resource object not closed

2). When constructing the Adapter, there is no cached convertView

3). The Bitmap object does not call recycle() to release the memory when it is not in use.

4). Try to use the context of the application instead of the context related to activiy

5). Registration does not cancel the memory leak caused by cancellation

6). Memory leak caused by objects not being cleaned up in the collection

Find memory leaks

To find memory leaks, you can use the AndroidProfiler tool that comes with Android Studio, or you can use the LeakCanary of the Square product.

**30. The initialization order of the class is (static variables, static code blocks) > (variables, code blocks) > constructor **

**31. How to achieve the sliding of Fragment**

**32.ViewPager use details, how to set up to initialize the current Fragment each time, the other does not initialize **

**33.What is the reuse of ListView**

**34. How to cancel AsyncTask**

**35. Why does Android introduce Parcelable**

**36. Have you tried to simplify the use of Parcelable**

**37.Bitmap What to pay attention to when using? **

**38.Can Oom try catch? **

**39. Have you seen a multi-process scenario? **

**40. Regarding the handler, where is the new handler anywhere?

**41.sqlite upgrade, increase the statement of the field**

**42. Database upgrades and delete tables do not involve data migration, but modifying the tables involves migrating legacy data. The upgrade method is as follows: **

Name the existing table as a temporary table.
Create a new table.
Import data from the temporary table into the new table.
Delete the temporary table.
Rewrite

If it is a cross-version database upgrade, there are two ways, as follows:

Upgrade step by step to determine the difference between the adjacent version and the current version, V1 upgrade to V2, V2 upgrade to V3, and so on.
Cross-level upgrades, determine the difference between each version and the current database, and write a special upgrade code for each case.

**43.bitmap recycler related**

**44. Strong reference is set to null, will it be recycled? **

**45. What is the cache used by glide? **

**46. How does Glide memory cache control size? **

**47. Please describe the relationship between the four components of Android, and say the design pattern of Android MVC. **

**48.ContentProvider permission management (read and write separation, permission control - accurate to the table level, URL control); **

**49.Fragment status save**

**50.startActivityForResult is the method of which class, under what circumstances, how to decouple if used in Adapter**

**51. How to calculate the size of a Bitmap occupies memory, how to ensure that loading Bitmap does not generate memory overflow? **

    Bitamp occupied memory size = width pixel x (inTargetDensity / inDensity) x height pixel x (inTargetDensity / inDensity)x memory occupied by one pixel

Note: here inDensity represents the dpi of the target image (under which resource folder), inTargetDensity represents the dpi of the target screen, so you can find that inDensity and inTargetDensity will stretch the width and height of the Bitmap, thus changing the size of the memory occupied by the Bitmap. .

There are two ways to get the memory footprint in the Bitmap.

getByteCount(): API12 join, which represents the minimum memory required to store the pixels of the Bitmap.
getAllocationByteCount(): API19 join, which represents the amount of memory allocated for Bitmap in memory, instead of the getByteCount() method.
getByteCount() and getAllocationByteCount return the same result when Bitmap is not reused. When decoding a picture by multiplexing Bitmap, then getByteCount() indicates the size of the memory occupied by the newly decoded picture, and getAllocationByteCount() indicates the size of the memory occupied by the multiplexed Bitmap (ie, the length of mBuffer).

In order to ensure that there is no memory overflow when loading the Bitmap, you can use the BitmapFactory for image compression, mainly with the following parameters:

BitmapFactory.Options.inPreferredConfig: Change ARGB_8888 to RGB_565 to change the encoding mode and save memory.
BitmapFactory.Options.inSampleSize: Scale, you can refer to the Luban library to calculate the appropriate scaling according to the width and height of the image.
BitmapFactory.Options.inPurgeable: Allows the system to reclaim memory when there is insufficient memory.

**52. How is this for the application update? (Grayscale, forced update, sub-region update)**

**53.Handler mechanism, please write a way and code to update the UI**

**54. Please explain Android as a signature mechanism. **

**55. How to convert hexadecimal data with decimal and binary**

**56. How to communicate between activty and Fragmengt, how to communicate with Fragmengt and Fragmengt**

**57. Is the custom view more efficient than the xml definition? Explain the reason. **

**58. There are generally several types of broadcast registrations, each with its own advantages and disadvantages**

**59. There are several kinds of service startups, how to communicate between services and activty, how to communicate between services and services**

**60. Knowledge of the database, including local database optimization points. **

**61.ddms and traceView difference;**

**62.Fragment life cycle; Fragment state save;**

**63..startActivityForResult is the method of which class, under what circumstances, how to decouple if used in Adapter; **

**64.AsyncTask principle and deficiency;**

**65.IntentService principle; **

**66.Activity how to bind to the Service, how to start their own corresponding Service in the Activity; **

**67.AstncTask + What is the difference between HttpClient and AsyncHttpClient;**

**68. How to ensure that a background service is not killed; what is the way to save power; **

**69. How to intercept and abort a message by broadcast; whether the broadcast can request the network; the broadcast causes anr time limit;

**70.BroadcastReceiver, LocalBroadcastReceiver difference;**

**71. Please introduce how ContentProvider implements data sharing; **

**72. How is the problem of misplaced pictures in ListView? **

**73. What is the relationship between Activity, Intent, and Service; **

**74. The difference between ApplicationContext and ActivityContext; **

**75.The difference between Serializable and Parcelable;**

**76. How to use AsyncTask;**

**77. How is this for the application update? (grayscale, forced update, sub-region update);**

**78. Which methods are inevitable when jumping between two activities? **

A: In general, for example, there are two activities, called A and B. When A is activated in A, A calls onPause(), then B calls onCreate(), onStart(), onResume(). .
At this time, B overwrites the form, and A calls the onStop() method. If B is transparent or is a dialog style, it will not call A.
onStop() method.
    
**79. How to choose a third party, from those aspects? **

**80. Simply say the process of access payment, whether you have access to the payment function**

**81. Say your understanding of multi-process, when to use multi-process, why use multi-process, why use process communication in the case of multi-process. **

**82. Say why the handler has a memory leak, why does the memory leak occur without inheriting Handle**

**83. Say your understanding of broadcasting**

**84. Say your understanding of the service, how to kill a service. **

**85. How to view SP and SQList files in the emulator. How to visually view the number of layout nesting levels and load time. **

**86. Process and review time for packaging and launching of major platforms, common problems (mainstream application market speaks 3-4)**

**87. How to collect anr information**

**88. Talk about the relationship between ContentProvider, ContentResolver, ContentObserver**

ContentProvider: Manage data, provide data addition, deletion, and change operations. The data source can be database, file, XML, network, etc. ContentProvider provides a unified interface for accessing these data, which can be used for data sharing between processes.
ContentResolver: ContentResolver can manipulate data in different ContentProviders with different URIs. External processes can interact with ContentProvider through ContentResolver.
ContentObserver: Observe the data changes in the ContentProvider and notify the outside world of the changes.

**89.AlertDialog, popupWindow, Activity difference **

**90.Android property animation feature**

If you only need to move, scale, rotate, and fade the View in your requirements, the tweened animation is really sound enough. But obviously, these functions are not enough to cover all the scenes. Once our needs exceed the four operations of View, such as moving, zooming, rotating and fading, the tween animation can no longer help us. That is to say, it has considerable limitations in terms of functionality and scalability, so let's take a look at the scenes that tween animation is not capable of.

Note that I used the description of "Operating on View" when I introduced the tween animation. Yes, the tween animation can only be applied to the View. That is, we can animate a Button, TextView, or even a LinearLayout, or any other component that inherits from View, but if we want to animate a non-View object, sorry, the tween animation will help Not busy. Some friends may feel incomprehensible. How do I need to animate a non-View object? Here I give a simple example, for example, we have a custom View, there is a Point object in this View to manage the coordinates, and then in the onDraw () method is based on the coordinates of the Point object to draw . That is to say, if we can animate the Point object, then the animation of the entire custom View will be there. Obviously, tween animation does not have this feature, which is its first flaw.

Then there is a flaw in the tween animation, that is, it can only implement the four animation operations of moving, scaling, rotating, and fading. If we want to dynamically change the background color of the View? Unfortunately, we can only achieve it on our own. To put it bluntly, the previous tween animation mechanism was done using hard-coded methods. The function-limited death is these, basically without any extensibility.

Finally, there is a fatal flaw in the tween animation, that is, it just changes the display of the View, and does not really change the properties of the View. What does that mean? For example, now there is a button in the upper left corner of the screen, then we move it to the lower right corner of the screen through the tween animation, now you can try to click this button, the click event will never trigger, because actually this The button still stays in the upper left corner of the screen, except that the tween animation draws the button to the bottom right corner of the screen.

**91. How to import an external database?**

Include the original database in the res/raw of the project source

The database under android should be stored in /data/data/com..(package name)/ directory, so what we need to do is to transfer the existing database to that directory. The operation method is to use FileInputStream to read the original database. Then use FileOutputStream to write the contents of the read to that directory.

**92.LinearLayout, RelativeLayout, FrameLayout features and contrast, and introduce the use of the scene. **

**93. Talk about the understanding of interfaces and callbacks**

**94. What are the processing techniques for screen adaptation?**

**95. The server only provides the data receiving interface. How to ensure the orderly arrival of data under multi-threaded or multi-process conditions? **

**96. Understanding of dynamic layout**

**97. How to remove duplicate code? **

**98.Recycleview and ListView difference **

**99.ListView image loading disorder principle and solution**

**100. Dynamic rights adaptation scheme, concept of permission group**

**101. Why does the Android system design ContentProvider? **

**102. Does the drop-down status bar affect the life cycle of the activity**

**103. If you make a network request on onStop, how to recover onResume? **

**104. What should I pay attention to when using Bitmap? **

**105.Bitmap's recycler**()

**106. The main steps to turn on the camera in Android**

**107.ViewPager use details, how to set up to initialize the current Fragment each time, the other is not initialized? **


**108.Debug and Release state are different**

**109.dp is what, sp, what is the difference **

**110. Custom View, ViewGroup pay attention to those callbacks? **

**111. Reasons for the interface and solutions **

**112.android storage type**

**113.service used, basic calling method**

**114.Handler mechanism**

**115.LinearLayout, FrameLayout, RelativeLayout performance comparison, why **

RelativeLayout will make the child View call 2 times onMeasure, and when the LinearLayout has weight, it will also call the child View2 times onMeasure.

The RelativeView's child View will cause efficiency problems if the height is different from the RelativeLayout. This problem will be more serious when the child View is complex. If possible, try to use padding instead of margin.
 
Use LinearLayout and FrameLayout instead of RelativeLayout without affecting the depth of the hierarchy.
Finally, think about the contradictory question at the beginning of the article. Why did Google create a new RelativeLayout for developers by default, but they used a LinearLayout in DecorView. Because DecorView's hierarchical depth is known and fixed, the top title bar, the next content bar. Using RelativeLayout does not reduce the depth of the hierarchy, so using LinearLayout on the root node is the most efficient. The reason for creating a new RelativeLayout for developers is that developers can use the least possible View level to express the layout to achieve optimal performance, because complex View nesting will have a greater impact on performance.

**116.Activity life cycle, other life cycles after the finish call will go? **

**117. How to check for memory leaks, MAT analysis methods and principles, what are the causes of various leaks, such as why Handler leaks **


**118.view drawing familiar, introduce **

**119.anr is because of what happened, how to check **

**120. What are the optimization measures on the interface? For example, the list display, etc., usually encountered memory problems, how to optimize? **

**121. How do you communicate between threads? **


**122. Have you ever done apk multi-channel packaging;**


**123.java thread, scene implementation, how to request multiple threads at the same time, how to return the result of waiting for all thread data to complete a composite data**

**124.What is the role in Manifest.xml**

**125. What is the difference between multi-process, process and thread, how to specify multiple processes for the four components. **

**126. How to choose a third party and consider from those aspects**

**127.scheme jump protocol**

The scheme in Android is an intra-page jump protocol. By defining your own scheme protocol, you can jump to each page in the app.

The server can be customized to tell the app which page to jump to.

App can jump to another App page

Jump page can be accessed through H5 page

**128.The difference between Service and Thread**

Service is a component of the system in Android. It runs in the main thread of a separate process and cannot perform time-consuming operations. Thread is the smallest unit of program execution, allocates the basic unit of the CPU, and can open sub-threads to perform time-consuming operations.

The Service can obtain its own instance in different activities, and can conveniently operate the Service. Thread is difficult to get its own instance in different activities. If the Activity is destroyed, the Thread instance is hard to get again.

**129.Bitmap recycle**

Before Android 3.0 Bitmap was stored in the heap, we just need to recycle the heap memory.
    
After Android 3.0 Bitmap is stored in memory, we need to reclaim the memory of the native layer and the Java layer.
    
The official recommendation is that we use the recycle method to recycle after 3.0. This method can also not be called actively, because the garbage collector will automatically collect unavailable Bitmap objects for recycling.
    
The recycle method will determine that Bitmap will send instructions to the garbage collector if it is not available, so that it can reclaim the memory of the native layer and the Java layer, then the Bitmap enters the dead state.
    
The recycle method is irreversible. If you call getPixels() again, you won't get the desired result.

**120. Handwritten an easy code combined with Retrofit + okhttp web request**

**121. The structure of JSON? **

Json is a lightweight data exchange format.
Json is simply an object and an array, so these two structures are two structures, an object and an array. These two structures can represent various complex structures.

1, the object: the object is represented as "{}" expanded content, the data structure is the structure of the key-value pairs of {key:value, key:value,...}, in the object-oriented language, the key is the object Attribute, value is the corresponding attribute value, so it is easy to understand, the value method is the object. Key to get the attribute value, the type of this attribute value can be number, string, array, object.

2, array: array in json is the content of the brackets "[]", the data structure is ["java", "javascript", "vb", ...], the value is the same as in all languages, With index acquisition, the value of a field value can be a number, a string, an array, or an object.
After two structures of objects and arrays, they can be combined into a complex data structure.

**122.xml has several ways to resolve, difference?**

There are three basic parsing methods: DOM, SAX, Pull
Dom parsing: The parser reads the entire document, builds a tree structure that resides in memory, and then manipulates the tree structure using the DOM interface. The advantage is that it is more convenient to add, delete and change documents, and the disadvantages occupy a relatively large memory.
Sax analysis: Based on the event-driven type, the advantage takes up less memory and the parsing speed is fast. The disadvantage is that it is only suitable for reading documents. It is not suitable for adding, deleting or modifying documents, and cannot stop midway.
Pull parsing: also based on event-driven, provided by the official android API, can be terminated at any time, call the next() method to extract them (actively extract events)

**123.json two ways of parsing **

1, SDK provides JSONArray, JSONObject

2, Gson provided by Google
Deserialize objects by fromJson() (ie convert json strings to object types)
Serialization of objects via toJson() (ie converting object types to json strings)

**124. What is the rule for defining JavaBeans when Gson is parsed by google? **

1). Implement serialization Serializable

2). Attribute privatization, and provide get, set methods

3). Provide a non-parametric structure

4). The property name must be the same as the property name in the json string (because Gson parses the json string using the Java reflection principle)

**125.Understanding the aar file, did you encounter any hurdles; **

**126. Data loading is more about pagination, how do you implement it; **

**127. Java static variables will not change in new objects;**

**128.equals and hashcode relationship;**

**129.activity startActivity and context startActivity difference**

(1) When starting a new Activity from Activity, you can directly mContext.startActivity(intent)

(2) If you start the Activity from other Contexts, you must set the Flag to the intent:

    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK) ;
    mContext.startActivity(intent);

How to create a Dialog dialog in Service

1. After we get the Dialog object, we need to set the type, ie:

    dialog.getWindow().setType(WindowManager.LayoutPaams.TYPE_SYSTEM_ALERT)

2. Add permissions to Manifest:

    <uses-permissionandroid:name="android.permission.SYSTEM_ALERT_WINOW" />
    
**130.HandlerThread**

1, HandlerThread role

When the system has multiple time-consuming tasks to execute, each task will open a new thread to perform time-consuming tasks, which will cause the system to create and destroy threads multiple times, thus affecting performance. In order to solve this problem, Google proposed HandlerThread, HandlerThread is to create a Looper circulator in the thread, let Looper poll the message queue, when there is a time-consuming task into the queue, you do not need to open a new thread, in the original thread Execute the task, otherwise the thread is blocked.
    
2, the advantages and disadvantages of HandlderThread

- HandlerThread is essentially a thread class that inherits Thread;

- HandlerThread has its own internal Looper object, which can be looped;

- Passing the HandlerThread's looper object to the Handler pair, you can execute the asynchronous task in the handleMessage() method.

- After creating the HandlerThread, you must first call HandlerThread.start (the method, Thread will first call the run method, create a Looper object.

- HandlerThread has the advantage that asynchronous will not block, reducing the consumption of performance

- The disadvantage of HandlerThread is that you can't continue multitasking at the same time, waiting for processing, and the processing efficiency is low.

- HandlerThread is different from thread pool. HandlerThread is a string queue with only one thread behind it.

**131.IntentService**

https://link.juejin.im/?target=http%3A%2F%2Fblog.csdn.net%2Fjavazejian%2Farticle%2Fdetails%2F52426425

**132.AsyncTask**

https://link.juejin.im/?target=http%3A%2F%2Fblog.csdn.net%2Fjavazejian%2Farticle%2Fdetails%2F52462830

**133. Have you encountered any screen and resource adaptation issues? **

Https://www.jianshu.com/p/46ce37b8553c

**134. What problems are encountered in the project, and how do you solve it in the end? **

Https://www.jianshu.com/p/69d9444e2a9a

** 135. How to set an Activity to the style of the window. **

Configured in <activity>:

    Android:theme="@android:style/Theme.Dialog"
    
In addition

    Android:theme="@android:style/Theme.Translucnt"
Is setting transparency


**136. Android Application object must master seven points**

Https://blog.csdn.net/lilu_leo/article/details/8649941

**137.listview image loading disorder principle and solution**

Https://blog.csdn.net/guolin_blog/article/details/45586553

**138. Database data migration, a few steps to put the elephant into the new refrigerator? **

- Change table name to temporary table ALTER TABLE Order RENAME TO _Order

- Create a new table CREATETABLE Test(Id VARCHAR(32) PRIMARY KEY ,
  CustomName VARCHAR(32) NOTNULL , Country VARCHAR(16) NOTNULL)

- Import data INSERTINTO Order SELECT id, "", Age FROM _Order

- Delete temporary table DROPTABLE _Order

**139. In-depth understanding of Java annotation types (@Annotation)**

Https://blog.csdn.net/javazejian/article/details/71860633

**140. How to open multi-process? Can the application open N processes? **

** 141.final modify an object, can call the object to modify the properties of the method **

** 142. Annotation how to get, why does the reflection consume performance? **

** 143. Int, long range of values ​​and BigDecimal, how to deal with the value out of bounds? **

** 144. How to view the recycling of an object in Android? **

**145. The relationship between ContentProvider, ContentResolver, and ContentObserver. **

**146.How to speed up the launch of Activity. **


**147.Invalidate and requestLayout difference and use. **

**148. How to decompile, reverse analysis of code; **



**149.Intent value has a size limit, why, how to deal with; **

**151. How to make a network request in the broadcast**

**152. Double thread prints 12121212 by thread synchronization.......**



**153.RemoteViews implementation and usage scenarios**


**154. Handling of many error codes on the server (there are tens of thousands of error codes)**


**155.adb common command line **

**156. Several ways of cross-process communication in Android**

1: Access activities of other applications
Such as calling the system call application

    Intent callIntent = new Intent(Intent.ACTION_CALL,Uri.parse("tel:12345678");
    startActivity(callIntent);

2: Content Provider
Such as accessing the system album

3: Broadcast (Broadcast)
Such as showing system time

4: AIDL service

**157. Show the difference between Intent and implicit Intent**

For an Intent that explicitly indicates the name of the target component, we call it an "explicit Intent."

For an Intent that does not explicitly indicate the name of the target component, it is called an "implicit Intent."

For implicit intents, when defining an Activity, specify an intent-filter. When an implicit intent object is matched by an intent filter, there are three aspects that will be referenced:

Action

Category (Category ['kætɪg(ə)rɪ] )

Data (Data)

    <activity android:name=".MainActivity" android:label="@string/app_name">
    <intent-filter>
    <action android:name="com.wpc.test" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="image/gif"/>
    </intent-filter>
    </activity>

**158. What is the thread pool, what is the role of the thread pool**

The basic idea of ​​the thread pool is still the idea of ​​an object pool. It opens up a memory space in which many (undead) threads are stored. The thread execution scheduling in the pool is handled by the pool manager. When there is a thread task, take one from the pool,After the execution is completed, the thread object is pooled, which avoids the performance overhead caused by repeatedly creating thread objects and saves system resources. It is like going to the canteen to eat rice is everyone who sees who wins, whoever grabs the first to eat first, after having the thread pool, is to line up the formation, today I have a good relationship with you, you come to eat first. For example, an application needs to interact with the network. There are many steps to access the network. In order not to block the main thread, each step creates a thread. In the thread and interacts with the network, the thread pool becomes simple. The thread pool is the thread. A kind of encapsulation makes the thread easier to use. It only needs to create a thread pool, put these steps into the thread pool like a task, and just call the thread pool's destruction function when the program is destroyed.

The disadvantages of a single thread: a. Every new Thread new object performance is poor b. Thread lacks unified management, may open new threads without limit, compete with each other, and may take up too much system resources to cause crashes or OOM, c. Lack of more Features such as scheduled execution, periodic execution, and thread interrupts.

The advantages of the four thread pools provided by java are: a. Reusing existing threads, reducing the overhead of object creation and extinction, and good performance. b. It can effectively control the maximum number of concurrent threads, improve the utilization rate of system resources, and avoid excessive resource competition and avoid congestion. c. Provide functions such as scheduled execution, regular execution, single thread, and concurrent number control.

Java thread pool

Java provides four thread pools through Executors, namely:

newCachedThreadPool creates a cacheable thread pool. If the thread pool length exceeds the processing requirements, it can flexibly reclaim idle threads. If there is no reclaimable, create a new thread.

newFixedThreadPool creates a fixed-length thread pool that controls the maximum number of concurrent threads, and the excess threads wait in the queue.

newScheduledThreadPool Creates a fixed-length thread pool that supports scheduled and periodic task execution.

newSingleThreadExecutor creates a single-threaded thread pool that only uses a single worker thread to perform tasks, ensuring that all tasks are executed in the specified order (FIFO, LIFO, priority).

(1). newCachedThreadPool

Create a cacheable thread pool. If the thread pool length exceeds the processing requirements, you can flexibly reclaim idle threads. If there is no reclaimable, create a new thread. The thread pool is infinite. When the second task is executed, the first task is completed, and the thread that executes the first task is reused instead of creating a new thread each time.

(2). newFixedThreadPool

Create a fixed-length thread pool that controls the maximum number of concurrent threads, and the excess threads wait in the queue.

(3) newScheduledThreadPool

Create a fixed-length thread pool that supports scheduled and periodic task execution. ScheduledExecutorService is more secure and powerful than Timer

(4), newSingleThreadExecutor

Create a single-threaded thread pool that only uses a single worker thread to perform tasks, ensuring that all tasks execute in the specified order (FIFO, LIFO, priority)

Http://yukai.space/2017/05/08/java%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E4%BD%BF%E7%94% A8/

Https://juejin.im/post/5906b6e78d6d810058dab1bf

How is the virtual machine implemented synchronized?

Http://www.infoq.com/cn/articles/java-se-16-synchronized

**159.IntentService usage**

First, Introduction to IntentService

IntentService is a subclass of Service that adds extra functionality to the regular Service. First look at the Service itself has two problems:

Service does not specifically start a separate process, Service is in the same process as its application;

Service is not a special new thread, so you should not directly deal with time-consuming tasks in the Service;

Second, the IntentService feature

Will create a separate worker thread to handle all Intent requests;

Will create a separate worker thread to handle the code implemented by the onHandleIntent() method, without having to deal with multithreading issues;

After all the requests are processed, the IntentService will stop automatically, without stopping the Service by calling the stopSelf() method.

Provides a default implementation for Service's onBind(), which returns null;

Provide a default implementation for the Service's onStartCommand, adding the request Intent to the queue;

IntentService does not block UI threads, while normal Serveice causes ANR exceptions.
If the Intentservice has not completed the previous task, it will not open a new thread. After waiting for the previous task to complete, execute the new task. After the task is completed, call stopSelf again.

Third, the role

Generates a default worker thread that is separate from the main thread to execute all Intetnt passed to the onStartCommand() method.

Generate a work queue to transfer the Intent object to your onHandleIntent() method, passing only one Intent object at a time, so you don't have to worry about multithreading. The service is automatically stopped after all the requests have been executed, so you don't need to call the stopSelf() method to stop it yourself.

The service provides a default implementation of the onBind() method, which returns null

Provides a default implementation of the onStartCommand() method, which passes the Intent to the work queue first, and then takes one from the work queue each time to the onHandleIntent() method, in which the Intent pairs are processed accordingly.

**160.Android Holo theme and MD theme concept, and your opinion**

Holo Theme

Holo Theme is the most basic rendering of Android Design. Because it is the most basic Android Design presentation, every Android 4.X mobile phone system has controls that integrate Holo Theme. That is, developers don't need to design their own controls, but call the corresponding controls directly from the system. There are no highlights in the UI, and the visuals of Android4.X's settings/phones are extremely uniform. The benefits are obvious, the app is extremely recognizable as an Android app, and it's completely impossible to conflict with the system style.

Material Design

Material design is actually a simple design language, which includes system interface style, interaction, UI, more focus on simulation, more bold and rich color, richer interactive forms, more flexible layout form.

1. A clear, visual interface style,

2. Color matching makes the app look very bold, full of color, highlighting the content

3.Material design attaches great importance to the layout of the interface

4. Material design's interaction design uses responsive interaction. This interaction design can enhance an application from simply presenting the information requested by the user to a tool that can generate more intense and concrete interaction with the user.

**161.String, StringBuffer, StringBuilder What are the differences**

Comparison of the execution speed of the three: StringBuilder > StringBuffer > String

String changes a value each time it opens up a new memory space

StringBuilder: thread is not safe

StringBuffer: thread safe

A summary of the use of the three:

1. If you want to manipulate a small amount of data with = String

2. Single-threaded operation string buffer operation under large amount of data = StringBuilder

3. Multi-threaded operation string buffer Under operation of large amount of data = StringBuffer

**162. Accurate calculation of floating point numbers**

BigDecimal class for business computing, Float and Double can only be used for scientific computing or engineering computing

**163.Android system provides those animation mechanisms**

Frame by frame animation

The principle of frame-by-frame animation is very simple. It is actually splitting a complete animation into separate images, and then coordinating them to play, similar to how cartoons work.

2. Tween animation

The tween animation can perform a series of animation operations on the View, including fade, zoom, pan, and rotate.

Defect: The tween animation can only be applied to the View. It can only move, zoom, rotate and fade in. The tween animation has a fatal flaw, that is, it only changes the display of the View. Only, without actually changing the properties of the View

Frame-by-frame animation and tweened animation are old and have a lot of online information.

3. Property animation

ValueAnimator

ValueAnimator is the core class of the entire property animation mechanism.

ObjectAnimator

Compared to ValueAnimator, ObjectAnimator may be the class we are most exposed to, because ValueAnimator is just a smooth animation transition to the value, but we don't seem to have a lot of scenarios. The ObjectAnimator is different. It can directly animate any property of any object, such as the alpha property of the View.

**164.java why cross-platform**

Because the code after the Java program is compiled is not a code that can be directly run by the hardware system, but a "media code" - bytecode. Then different Java virtual machines (JVMs) are installed on different hardware platforms, and the JVM is used to "translate" the bytecode into code that can be executed by the corresponding hardware platform. So for Java programmers, there is no need to consider what the hardware platform is. So Java can be cross-platform.


**165.[Lifecycle under normal and abnormal conditions] (http://blog.csdn.net/geekerhw/article/details/48749935)**

**166.[IntentService is better than Service] (https://link.zhihu.com/?target=http%3A//blog.qiji.tech/archives/2693)**



**167.[The difference between Thread and HandlerThread](https://www.jianshu.com/p/5b6c71a7e8d7)**

**168.[About <include>< merge ><stub> usage scenarios] (http://www.trinea.cn/android/layout-performance/)**


**169.[Understanding the Android message mechanism](https://zhuanlan.zhihu.com/p/25222485)**

**170.[Which situations can lead to OOM? ](http://note.youdao.com/)**

**171.[What is the principle of monitoring memory leaks with the leak tool? ](https://www.jianshu.com/p/5ee6b471970e)**

**172.[ThreadLocal principle, implementation and how to guarantee Local property] () **

**173.[What is the new container class that Android introduced after optimizing HashMap? ](http://blog.csdn.net/u010687392/article/details/47809295)**

**174.[Thread pool implementation mechanism](http://www.cnblogs.com/dolphin0520/p/3932921.html)**

**175.[Integer class optimization for int] (http://denverj.iteye.com/blog/745422)**

**176.[What are the advantages of implementing singleton mode through static inner classes] (http://blog.csdn.net/yingpaixiaochuan/article/details/63260514)**

**177. Singleton implementation thread synchronization requirements: **

1. The singleton class ensures that it has only one instance (the constructor is private: it is not instantiated externally and is not inherited).

2. Singleton classes must create their own instances.

3. The singleton class must provide a unique instance for other objects.

**178.[What are the reasons for the interface? ](https://www.jianshu.com/p/1fb065c806e6)**

**179.[The cause of OOM/ANR? ](http://www.cnblogs.com/purediy/p/3276545.html)**

**180.[How to communicate between Activity and Fragment? ](http://blog.csdn.net/u012702547/article/details/49786417)**

**181.[What are the methods for inter-process communication between operating systems] (http://blog.csdn.net/shinehoo/article/details/5818843/)**

**182. How to ensure that Service is not killed**

The Android process does not die from three levels:

A. Provide process priority and reduce the probability of the process being killed

Method 1: Monitor the lock screen unlocking event of the mobile phone, activate a 1-pixel activity when the screen is locked, and destroy the activity when the user unlocks.

Method 2: Start the foreground service.

Method 3: Improve service priority:

In the AndroidManifest.xml file for intent-filter you can set the highest priority by android:priority = "1000" attribute, 1000 is the highest value, if the number is smaller, the lower the priority, and it is suitable for broadcast.

B. After the process is killed, pull it

Method 1: Register a high frequency broadcast receiver to evoke the process. Such as network changes, unlock screens, boot, etc.

Method 2: The two processes evoke each other.

Method 3: Rely on the system to evoke.

Method 4: Restart the service:service +broadcast mode in the onDestroy method, that is, when the service goes to ondestory, send a custom broadcast, and when the broadcast is received, restart the service;

C. Relying on third parties

According to different terminals, Xiaomi mobile phone (including MIUI) access to Xiaomi push, Huawei mobile phone access Huawei push; other mobile phones can consider accessing Tencent pigeon or Aurora push and Xiaomi push to do A/B Test.

**183. Cause of memory leak**

- For the use of BraodcastReceiver, ContentObserver, File, Cursor, Stream, Bitmap and other resources, you should close or log out when the Activity is destroyed.
- Static inner classes hold external member variables (or context): you can use weak references or use ApplicationContext.
- An inner class holds an external class reference, and an asynchronous task holds an external member variable.
- Unusable objects in the collection are not removed in time.
- Unused objects are released in time. If you use Bitmap, use recycle() and then assign null.
- Memory leak caused by handler. If the message in MessageQueue is not processed when the activity is destroyed, it will cause memory leak and can be solved by weak reference.
- Removed when the set monitor is not in use. Remove in time as in Destroy. Especially starting with addListener, you need to remove in Destroy.
- Activity leaks can be used with LeakCanary.
- Do some processing on memory references, commonly used soft references, weak references
- When processing images in memory, they are processed directly in memory, such as: boundary compression
- Dynamically reclaim memory
- Optimize heap memory allocation for Dalvik virtual machines
- Custom heap memory size

**184.Handler mechanism**

Andriod provides Handler and Looper to satisfy the communication between threads. Handler FIFO principle. The Looper class is used to manage message exchanges between objects within a particular thread (MessageExchange).

Looper: A thread can generate a Looper object that manages the MessageQueue in this thread.

Handler: You can construct a Handler object to communicate with the Looper to push a new message into the MessageQueue, or receive a message from the Looper that is retrieved from the Message Queue.

Message Queue: Used to store messages placed by threads.

Thread: UIthread is usually the main thread, and Android will create a MessageQueue for it when it starts the program.
    
**185.ListView Kardon reason**

Adapter's getView method inside convertView does not use setTag and getTag methods;

In the getView method, the assignment of the ViewHolder after initialization or the display state of the multiple controls and the display of the background are not optimized, or it contains complex calculations and time-consuming operations;

In the getView method, the inflate's row nesting is too deep (the layout is too complicated) or the layout has a large image or background;

Adapter is redundant or unreasonable notifySetDataChanged;

The listview is nested in multiple layers, and the multiple onMessure causes the card to be stuck. If the multi-level nesting cannot be avoided, it is recommended to set the height and width of the listview to fill_parent. If it is a listview inherited by the code, please don't forget it for you. Inherited classes are added to LayoutPrams, note that the height and width are both fill_parent;

**186.What are the advantages and disadvantages of Json**

1.JSON is much faster than XML

2. JSON is small in size relative to XML.

3. JSON's descriptiveness of data is worse than XML

**187. The role and understanding of AndroidManifest**

**188. The main steps to turn on the camera in Android**

**189.AlertDialog, popupWindow, Activity difference**

**190.LaunchMode application scenario**

Standard, create a new Activity.

singleTop, the top of the stack is not the Activity of this type, create a new Activity. Otherwise, onNewIntent.

singleTask, there is no Activity of this type in the fallback stack, create Activity, otherwise, onNewIntent+ClearTop.

note:

Set the activity of the "singleTask" startup mode. When it starts, it will first find the existence of the task whose attribute value affinity is equal to its attribute value taskAffinity; if there is such a task, it will start in this task. Otherwise it will start in the new task stack. Therefore, if we want to set the "singleTask" startup mode activity to start in a new task, we must set a separate taskAffinity attribute value for it.

If the Activity of the "singleTask" startup mode is not started in the new task, it will check whether the corresponding Activity instance already exists in the existing task. If it exists, the Activity located above the Activity instance will be all. End, that is, the event instance will eventually be in the top of the task's Stack.

There is only one "singleTask" startup mode activity in a task stack. There can be other activities on his top. This is different from singleInstance.

singleInstance, back in the stack, only this one Activity, no other Activity.

singleTop is suitable for receiving content display pages that are initiated by notifications.

For example, if a news content page of a news client receives 10 news feeds, it is annoying to open a news content page each time.

singleTask is suitable as a program entry point.

For example, the main interface of the browser. No matter how many applications start the browser, only the main interface will be launched once, the rest will go onNewIntent, and will clear other pages on the main interface.

singleInstance application scenario:

The bell interface of the alarm. You have previously set an alarm: 6 am. At 5:58 am, you activate the alarm setting interface and press the Home button to return to the desktop. At 5:59 am, you chat with your friends on WeChat; at 6 o'clock, the alarm rings and pops up. A dialog-style Activity (named AlarmAlertActivity) prompts you to 6 o'clock (this Activity is opened in SingleInstance loading mode), you press the return key, back to the WeChat chat interface, this is because the TaskAlertActivity is located in the Task The stack has only one element of his, so the stack of the Task is empty after exiting. If AlarmAlertActivity is opened with SingleTask, when the alarm sounds, press the return key to enter the alarm setting interface.

**191. How to use AsyncTask?**

**192.SpareArray principle**

** 193. Please introduce how ContentProvider implements data sharing? **

**194. Several ways to communicate between Android Service and Activity**

- Passing Binder objects

- by means of broadcast (broadcast)

**195.What is the principle and function of IntentService? **

**196. What is the relationship between Activity, Intent, and Service?

**197. The difference between ApplicationContext and ActivityContext**

**198.SP is the process synchronization? Is there any way to synchronize? **

**199. Process and Application life cycle**

**200. How to know the size of the view when encapsulating the View**

**201. The role and understanding of AndroidManifest**

**202.Handler, Thread and HandlerThread differences**

**203.handler sends a message to the child thread, how does the looper start? **

**204. Regarding Handler, what thread is new Handler anywhere?**

**205.The principle of ThreadLocal**

ThreadLocal is a class about creating thread-local variables. The usage scenario is as follows:

Implement a single thread singleton and a single thread context information store, such as transaction ids.

Thread-safe, non-thread-safe objects become thread-safe after using ThreadLocal, because each thread will have a corresponding instance.
Host some thread-related data to avoid passing parameters back and forth in the method.

**206. Please explain the relationship between Message, Handler, Message Queue, and Looper in the single-threaded model**

**207.ANR Positioning and Correction**

If there is a problem on the development machine, we can check the /data/anr/traces.txt, the latest ANR information in the very beginning.

The main thread is blocked by IO operations (network IO is not allowed in the main thread since 4.0).
Time-consuming calculations in the main thread
The wrong operation in the main thread, such as Thread.wait or Thread.sleep, will monitor the response of the program. Once the following two conditions occur, the ANR dialog box will pop up.
The application does not respond to user input events (such as buttons or touches) within 5 seconds.
BroadcastReceiver did not complete the relevant processing within 10 seconds
Service cannot be processed for 20 seconds in a specific time.

Use AsyncTask to handle time-consuming IO operations.

When using Thread or HandlerThread, call Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND) to set the priority. Otherwise, the program response will be reduced because the default Thread has the same priority as the main thread.

Use Handler to handle worker thread results instead of using Thread.wait() or Thread.sleep() to block the main thread.

Avoid time-consuming code in Activity's onCreate and onResume callbacks
The onReceive code in BroadcastReceiver should also minimize the time consuming. It is recommended to use IntentService.

** What is 208.oom? What causes oom? **

Http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0920/3478.html

1) Use a lighter data structure

2) Use Enum in Android

3) Bitmap object memory usage

4) Larger picture

5) The creation of the object in the onDraw method

6) StringBuilder

**209. Is there any workaround to avoid OOM? **

**210.Oom Can you try catch? why? **

**211. What caused a memory leak? **

1. Memory leak caused by resource object not closed

Description: Resource objects such as (Cursor, File, etc.) often use some buffers. When not in use, we should close them in time so that their buffers can reclaim memory in time. Their buffers exist not only in the java virtual machine, but also outside the java virtual machine. If we just set its reference to null without closing them, it will often cause a memory leak. Because some resource objects, such as SQLiteCursor (in the destructor finalize(), if we don't close it, it will close close() itself), if we don't close it, the system will close it when it recycles it, but This efficiency is too low. Therefore, when a resource object is not in use, its close() function should be called, turned off, and then set to null. Make sure our resource object is closed when our program exits.

The query database is often used in the program, but it is often not closed after the Cursor is used. If our query result set is small, the memory consumption is not easy to be discovered, and the memory problem will only be reproduced in the case of a large number of operations at a constant time, which will bring difficulties and risks to future tests and troubleshooting.

2. When constructing Adapter, there is no cached convertView

Description: Take the BaseAdapter that constructs the ListView as an example. The method is provided in the BaseAdapter: public View getView(int position, ViewconvertView, ViewGroup parent) to provide the ListView with the view object required by each item. Initially, the ListView will instantiate a certain number of view objects from the BaseAdapter based on the current screen layout, and the ListView will cache these view objects. When the ListView is scrolled up, the view object of the list item that was originally at the top is recycled and then used to construct the newest list item. This construction process is done by the getView() method. The second parameter of the getView() is the view object of the list item that is cached. (The initialized cache object has no view object and the convertView is null). It can be seen that if we do not use convertView, but re-instantiate a View object in getView() every time, it will waste time and waste memory. The process of ListView recycling the list object's view object can be viewed: android.widget.AbsListView.java --> voidaddScrapView(View scrap) method. Sample code:

		Public View getView(int position, ViewconvertView, ViewGroup parent) {
				View view = new Xxx(...);
				...
				Return view;
		}
    
Fix the sample code:

		Public View getView(int position, ViewconvertView, ViewGroup parent) {
				View view = null;
						If (convertView != null) {
								View = convertView;
								Populate(view, getItem(position));
								...
						} else {
								View = new Xxx(...);
								...
						}
				Return view;
		}

3.Bitmap object does not call recycle() to release memory when it is used.

Description: Sometimes we will manually manipulate the Bitmap object. If a Bitmap object is compared to memory, when it is not being used, you can call the Bitmap.recycle() method to reclaim the memory occupied by the object's pixels, but this is not required. , depending on the situation. Take a look at the comments in the code:

/* •Free up the memory associated with thisbitmap's pixels, and mark the •bitmap as "dead", meaning itwill throw an exception if getPixels() or •setPixels() is called, and will drawnothing. This operation cannot be •reversed, So it should only be called ifyou are sure there are no •further uses for the bitmap. This is anadvanced call, and normally need •not be called, since the normal GCprocess will free up this memory when •there are no more references to thisbitmap . /

4. Try to use the context of the application instead of the context associated with the activity.

This is a very hidden memory leak. There is an easy way to avoid context-related memory leaks. The most notable one is to avoid the context from getting out of his own range. Use the Application context. The life cycle of this context is as long as the life cycle of your application, not the life cycle of the activity. If you want to keep a long-lived object, and this object needs a context, remember to use

Application object. You can get it by calling Context.getApplicationContext() or Activity.getApplication() . More please see this article how to avoid Android memory leaks.

5. Registration does not cancel the memory leak caused by cancellation

Some Android programs may reference objects of our Anroid program (such as registration mechanisms). Even though our Android app is over, other reference programs still have references to an object in our Android app, and the leaked memory still can't be garbage collected. UnregisterReceiver is not called after registerReceiver is called. For example, suppose we want to listen to the phone service in the system to obtain some information (such as signal strength, etc.) in the lock screen interface (LockScreen), then define a PhoneStateListener object in LockScreen and register it in the TelephonyManager service. . For the LockScreen object, a LockScreen object is created when the lock screen interface needs to be displayed, and the LockScreen object is released when the lock screen interface disappears. However, if you forget to cancel the PhoneStateListener object we registered before releasing the LockScreen object, it will cause LockScreen to be garbage collected. If the lock screen interface is continuously displayed and disappeared, then OutOfMemory will eventually be caused by a large number of LockScreen objects being reclaimed, causing the system_process process to hang. Although some system programs seem to be automatically deregistered (of course not timely), we should explicitly cancel the registration in our program, and all registrations should be cancelled at the end of the program.

6. Memory leak caused by objects not being cleaned up in the collection

We usually add references to some objects to the collection. When we don't need the object, we don't clean up its references from the collection, so the collection will get bigger and bigger. If the collection is static, then the situation is even worse.

**212.ContentProvider permission management (answer: read and write separation, permission control - accurate to the table level, URL control)**

**213. How to intercept and abort a text message via broadcast? **

**214. Can the broadcast request the network? **

**215. Calculate the nesting level of a view**

**216. Is there an upper limit for Android threads? **

**217. Is there a limit to the thread pool? **

**218. What is the reuse of ListView? **

**219. Why did Android introduce Parcelable? **

The so-called serialization is to turn the object into a binary stream, which is convenient for storage and transmission.

Serializable is a set of serialization methods implemented by Java. It may trigger frequent IO operations and is less efficient. It is suitable for storing objects on disk.
Parcelable is a set of serialization mechanism provided by Android. It writes the serialized byte stream into a common memory. Other objects can read the byte stream from the shared memory and deserialize it into an object. Therefore, the efficiency is relatively high, and it is suitable for transmitting information between objects or between processes.

**220. Have you tried to simplify the use of Parcelable? **

**221.Android process classification? **

The Android process is mainly divided into the following types:

Foreground process

The process required by the user for the current operation. A process is considered a foreground process if any of the following conditions are met:

The Activity that the managed user is interacting with (the activity's onResume() method has been called)
Host a Service that is bound to the activity that the user is interacting with
Host the service that is running in the "foreground" (the service has called startForeground())
Hosting a service that is executing a lifecycle callback (onCreate(), onStart(), or onDestroy())
Hosting a BroadcastReceiver that is executing its onReceive() method
Usually, there are not many foreground processes at any given time. They will only be terminated if there is not enough memory to support them while continuing to run. At this point, the device has often reached the memory paging state, so some foreground processes need to be terminated to ensure that the user interface responds normally.

Visible process

There is no foreground component, but it still affects the progress of what users see on the screen. A process is considered visible if it meets any of the following conditions:

An activity that is not in the foreground but is still visible to the user (its onPause() method has been called). For example, this can happen if the foreground activity launches a dialog that allows the previous activity to be displayed afterwards.
Host a Service that is bound to a visible (or foreground) Activity.
Visible processes are considered to be extremely important processes, and unless they must be terminated in order to keep all foreground processes running at the same time, the system will not terminate them.

Service process

A process that is running a service that has started with the startService() method and that does not belong to the two higher class processes described above. Although the service process is not directly related to what the user sees, they are usually performing some user-focused operations (for example, playing music in the background or downloading data from the network). Therefore, the system keeps the service process running unless there is not enough memory to keep all foreground and visible processes running at the same time.

backstage process

A process that contains an Activity that is currently invisible to the user (the activity's onStop() method has been called). These processes have no direct impact on the user experience, and the system may terminate them at any time to reclaim memory for use by foreground processes, visible processes, or service processes. There are usually many background processes running, so they are saved in the LRU (least recently used) list to ensure that the last process containing the activity that the user has recently viewed is terminated. If an activity correctly implements the lifecycle method and saves its current state, terminating its process does not have a significant impact on the user experience, because when the user navigates back to the activity, the activity resumes all its visible state.

Empty process

A process that does not contain any active application components. The only purpose of retaining this process is to act as a cache to reduce the startup time required to run the component in it the next time. In order to balance the overall system resources between the process cache and the underlying kernel cache, the system often terminates these processes.

The ActivityManagerService is responsible for calculating the adj value of the process according to various policy algorithms, and then handing it over to the system kernel for process management.

SharePreference performance optimization, can you do process synchronization?
In Android, SharePreferences is a lightweight storage class that is especially useful for saving software configuration parameters. Use SharedPreferences to save the data, behind which is to store the data in xml file, the file is stored in /data/data/ < package name > /shared_prefs directory.

The reason why SharedPreference is a lightweight storage method is that it will load the entire file into memory when it is created. If the SharedPreference file is large, it will bring the following problems:

The first time you get a value from sp, it is possible to block the main thread and make the interface jam and drop frames.
When parsing sp, a large number of temporary objects are generated, resulting in frequent GCs, causing interface jams.
These keys and values ​​will always exist in memory and take up a lot of memory.
Optimization suggestion

Do not store large keys and values, which will cause interface cards, frequent GCs, memory usage, and more.
Do not put together unrelated configuration items. The larger the file, the slower the read.
Read frequent keys and keys that are not easy to change. Try not to put them together, affecting the speed. If the whole file is small, ignore it. Adding maintenance cost for this performance is not worth the cost.
Don't mess with edit and apply, try to modify the commit in batches, and apply will block the main thread multiple times.
Try not to store JSON and HTML. Please use JSON directly in this scenario.
SharedPreference can't communicate across processes. MODE_MULTI_PROCESS only guarantees that on the system before API 11, if sp has been read into memory and gets the SharedPreference again, if there is this flag, it will read the file again, and that's it.

** 222.Fragment difference between replace and add? **

**223.MVP, MVVM, MVC interpretation and practice**

**224. Outside the project, insights into technology, expanding knowledge**

** 225.What is the difference between the application of ApplyPrefrences and commit**

**226.MD5 is the encryption method, Base64?**

**227. Have a blog and github, mainly what to write? What are the concerns**

**228. The difference between HttpClient and HttpConnection**

**229. In addition to daily development, what else has you done? Such as continuous integration, automated testing, etc.**

**230.ActivityA jumps to ActivityB and then B presses back to return A, their respective life cycle order, A and B are opaque. **

**231. Where is the main method entry in Android**

**232. Write the lazy singleton mode that you think is optimal**

**233.activity storage and recovery of information when exiting unexpectedly, onCreate is judged when it enters normally. **

**234. Can the abstract class be instantiated, what is the theoretical basis? **

**235. How to configure a multi-channel package with a large difference (20% difference) through Gradle
How to convert class files into dex**

** 236.Service start and then bind how to close the service, why bindService can be linked with the Activity life cycle? **

**237. The difference between creating a thread directly in the Activity and creating a thread in the service**

**238. How is ListView's cache for multiple items?

**239.Android draws the difference between 2D and 3D View**

**240. Do you know about hardware acceleration**

** 241. How does ListView implement the management of items of different types?

**242. Why should Android design two classloaders, why not use one to distinguish by type**

**243.Bundle pass data why serialization is required**

**244. Is there any limit to the data transmitted by the broadcast, and why? **

**245. About reflection confusion, the solution to the performance **

**246. How does RecyclerView's itemdecoration handle click events**

**247. Compile-time annotations and runtime annotations**

**248.Canvas.save() and Canvas.restore() call timing**
