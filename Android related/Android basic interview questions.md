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
