# View的绘制原理

## 从 `ActivityThread`的`performLaunchActivity()`方法开始
```java
private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
    ...

    ContextImpl appContext = createBaseContextForActivity(r);
    Activity activity = null;
    try {
        java.lang.ClassLoader cl = appContext.getClassLoader();
        activity = mInstrumentation.newActivity(
                cl, component.getClassName(), r.intent);
        
        ...
    } catch (Exception e) {
        ...
    }

    try {
        ...

        if (activity != null) {
            ...
            Window window = null;
            if (r.mPendingRemoveWindow != null && r.mPreserveWindow) {
                window = r.mPendingRemoveWindow;
                ...
            }

            ...
            activity.attach(appContext, this, getInstrumentation(), r.token,
                    r.ident, app, r.intent, r.activityInfo, title, r.parent,
                    r.embeddedID, r.lastNonConfigurationInstances, config,
                    r.referrer, r.voiceInteractor, window, r.configCallback,
                    r.assistToken, r.shareableActivityToken);

            ...

    } catch (SuperNotCalledException e) {
        ...
    } catch (Exception e) {
        ...
    }

    return activity;
}
```
此处会创建`Activity`并且会调用`attach()`方法, 这里可以看到`Activity`中的`mWindowManager`具体是由哪个类实现的
```java
final void attach(...) {
    ...

    mWindow = new PhoneWindow(this, window, activityConfigCallback);
    ...

    mWindow.setWindowManager(
            (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
            mToken, mComponent.flattenToString(),
            (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
    ...
    // 从Window的setWindowManager方法中可以知道
    // mWindowManager为WindowManagerImpl
    mWindowManager = mWindow.getWindowManager();
    ...
}
```

## 接着`ActivityThread`调用`handleResumeActivity()`方法
```java
public void handleResumeActivity(...) {
    ...

    final Activity a = r.activity;
    ...
    if (r.window == null && !a.mFinished && willBeVisible) {
        r.window = r.activity.getWindow();
        View decor = r.window.getDecorView();
        decor.setVisibility(View.INVISIBLE);
        // 得到了WindowManager，WindowManager是一个接口
        // 并且继承了接口ViewManager
        ViewManager wm = a.getWindowManager();
        ...
        if (a.mVisibleFromClient) {
            if (!a.mWindowAdded) {
                a.mWindowAdded = true;
                // WindowManager的实现类是WindowManagerImpl，
                // 所以实际调用的是WindowManagerImpl的addView方法
                wm.addView(decor, l);
            } else {
                ...
            }
        }
        ...
    } else if (!willBeVisible) {
        ...
    }

    ...
}
```
再看`WindowManagerImpl`中的`addView`方法, 此处不能直接搜AndroidStudio的`WindowManagerImpl.addView()`，该类被隐藏了，无法看到对应实现。
```java
public final class WindowManagerImpl implements WindowManager {
    @UnsupportedAppUsage
    private final WindowManagerGlobal mGlobal = WindowManagerGlobal.getInstance();

    ...

    @Override
    public void addView(@NonNull View view, @NonNull ViewGroup.LayoutParams params) {
        applyTokens(params);
        mGlobal.addView(view, params, mContext.getDisplayNoVerify(), mParentWindow,
                mContext.getUserId());
    }

    ...
}
```
可以看到此处调用的是`WindowManagerGlobal`的`addView()`方法
```java
public void addView(View view, ViewGroup.LayoutParams params,
            Display display, Window parentWindow, int userId) {
    ...

    ViewRootImpl root;

    synchronized (mLock) {
        ...

        root = new ViewRootImpl(view.getContext(), display);

        ...
        try {
            root.setView(view, wparams, panelParentView, userId);
        } catch (RuntimeException e) {
            ...
        }
    }
}
```
可以看到这里使用的是`ViewRootImpl`将`view`进行设置的，在看`ViewRootImpl.setView()`方法前，先看看`ViewRootImpl`类的注释说明。
```
The top of a view hierarchy, implementing the needed protocol between View
and the WindowManager.  This is for the most part an internal implementation detail of WindowManagerGlobal
```
可以看出，`ViewRootImpl`是用来连接`View`和`WindowManager`的纽带。再来看看`ViewRootImpl.setView()`方法
```java
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView,
            int userId) {
    synchronized (this) {
        if (mView == null) {
            ...

            // Schedule the first layout -before- adding to the window
            // manager, to make sure we do the relayout before receiving
            // any other events from the system.
            requestLayout();
            ...
        }
    }
}
    
```
可以看到调用了`requestLayout()`方法，从这点下去，会发现调用`performTraversals()`方法,从这开始就是正式的绘制流程了。