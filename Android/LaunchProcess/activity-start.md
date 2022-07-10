# Activity启动流程

## startAcitityForResult
Launch an activity for which you would like a result when it finished. When this activity exits, your onActivityResult() method will be called with the given requestCode. Using a negative requestCode is the same as calling startActivity (the activity is not launched as a sub-activity).
Note that this method should only be used with Intent protocols that are defined to return a result. In other protocols (such as Intent.ACTION_MAIN or Intent.ACTION_VIEW), you may not get the result when you expect. For example, if the activity you are launching uses Intent.FLAG_ACTIVITY_NEW_TASK, it will not run in your task and thus you will immediately receive a cancel result.
As a special case, if you call startActivityForResult() with a requestCode >= 0 during the initial onCreate(Bundle savedInstanceState)/onResume() of your activity, then your window will not be displayed until a result is returned back from the started activity. This is to avoid visible flickering when redirecting to another activity.
This method throws android.content.ActivityNotFoundException if there was no Activity found to run the given Intent.

```java
public void startActivityForResult(@RequiresPermission Intent intent, int requestCode, @Nullable Bundle options) {
    if (mParent == null) {
        options = transferSpringboardActivityOptions(options);
        Instrumentation.ActivityResult ar =
            mInstrumentation.execStartActivity(
                this, mMainThread.getApplicationThread(), mToken, this,
                intent, requestCode, options);
        if (ar != null) {
            mMainThread.sendActivityResult(
                mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                ar.getResultData());
        }
        if (requestCode >= 0) {
            // If this start is requesting a result, we can avoid making
            // the activity visible until the result is received.  Setting
            // this code during onCreate(Bundle savedInstanceState) or onResume() will keep the
            // activity hidden during this time, to avoid flickering.
            // This can only be done when a result is requested because
            // that guarantees we will get information back when the
            // activity is finished, no matter what happens to it.
            mStartedActivity = true;
        }

        cancelInputsAndStartExitTransition(options);
    } else {
        ...
    }
```

## Instrumentation.execStartActivity
Execute a startActivity call made by the application. The default implementation takes care of updating any active Instrumentation.ActivityMonitor objects and dispatches this call to the system activity manager; you can override this to watch for the application to start an activity, and modify what happens when it does.
This method returns an Instrumentation.ActivityResult object, which you can use when intercepting application calls to avoid performing the start activity action but still return the result the application is expecting. To do this, override this method to catch the call to start activity so that it returns a new ActivityResult containing the results you would like the application to see, and don't call up to the super class. Note that an application is only expecting a result if requestCode is >= 0.
This method throws ActivityNotFoundException if there was no Activity found to run the given Intent.
```java
public ActivityResult execStartActivity(
            Context who, IBinder contextThread, IBinder token, Activity target,
            Intent intent, int requestCode, Bundle options) {
    ...
    try {
        intent.migrateExtraStreamToClipData(who);
        intent.prepareToLeaveProcess(who);
        int result = ActivityTaskManager.getService().startActivity(whoThread,
                who.getOpPackageName(), who.getAttributionTag(), intent,
                intent.resolveTypeIfNeeded(who.getContentResolver()), token,
                target != null ? target.mEmbeddedID : null, requestCode, 0, null, options);
        // 检查启动结果，如果无法正确启动Activity，就会抛出异常信息
        // 比如Unable to find explicit activity class; have you declared this activity in your AndroidManifest.xml?
        checkStartActivityResult(result, intent);
    } catch (RemoteException e) {
        throw new RuntimeException("Failure from system", e);
    }
    return null;
}
```

## ActivityTaskManager.getService().startActivity即ActivityTaskManagerService.startActivity
```java
@Override
    public final int startActivity(IApplicationThread caller, String callingPackage,
            String callingFeatureId, Intent intent, String resolvedType, IBinder resultTo,
            String resultWho, int requestCode, int startFlags, ProfilerInfo profilerInfo,
            Bundle bOptions) {
    return startActivityAsUser(caller, callingPackage, callingFeatureId, intent, resolvedType,
            resultTo, resultWho, requestCode, startFlags, profilerInfo, bOptions,
            UserHandle.getCallingUserId());
}

private int startActivityAsUser(IApplicationThread caller, String callingPackage,
        @Nullable String callingFeatureId, Intent intent, String resolvedType,
        IBinder resultTo, String resultWho, int requestCode, int startFlags,
        ProfilerInfo profilerInfo, Bundle bOptions, int userId, boolean validateIncomingUser) {
    assertPackageMatchesCallingUid(callingPackage);
    enforceNotIsolatedCaller("startActivityAsUser");

    userId = getActivityStartController().checkTargetUser(userId, validateIncomingUser,
            Binder.getCallingPid(), Binder.getCallingUid(), "startActivityAsUser");

    // TODO: Switch to user app stacks here.
    return getActivityStartController().obtainStarter(intent, "startActivityAsUser")
            .setCaller(caller)
            .setCallingPackage(callingPackage)
            .setCallingFeatureId(callingFeatureId)
            .setResolvedType(resolvedType)
            .setResultTo(resultTo)
            .setResultWho(resultWho)
            .setRequestCode(requestCode)
            .setStartFlags(startFlags)
            .setProfilerInfo(profilerInfo)
            .setActivityOptions(bOptions)
            .setUserId(userId)
            .execute();

}
```

## ActivityStarter.obtainStarter
A starter to configure and execute starting an activity. It is valid until after ActivityStarter.execute is invoked. At that point, the starter should be considered invalid and no longer modified or used.
```java
ActivityStarter obtainStarter(Intent intent, String reason) {
    return mFactory.obtain().setIntent(intent).setReason(reason);
}
```
Controller for interpreting how and then launching an activity. This class collects all the logic for determining how an intent and flags should be turned into an activity and associated task and root task.
```java
class ActivityStarter
```

## ActivityStarter.execute()
Resolve necessary information according the request parameters provided earlier, and execute the request which begin the journey of starting an activity.
Returns:
The starter result.
```java
int execute() {
    try {
        ...
        final LaunchingState launchingState;
        synchronized (mService.mGlobalLock) {
            final ActivityRecord caller = ActivityRecord.forTokenLocked(mRequest.resultTo);
            final int callingUid = mRequest.realCallingUid == Request.DEFAULT_REAL_CALLING_UID
                    ?  Binder.getCallingUid() : mRequest.realCallingUid;
            launchingState = mSupervisor.getActivityMetricsLogger().notifyActivityLaunching(
                    mRequest.intent, caller, callingUid);
        }
        ...
        int res;
        synchronized (mService.mGlobalLock) {
            ...
            res = executeRequest(mRequest);
            ...
            // Notify ActivityMetricsLogger that the activity has launched.
            // ActivityMetricsLogger will then wait for the windows to be drawn and populate
            // WaitResult.
            mSupervisor.getActivityMetricsLogger().notifyActivityLaunched(launchingState, res,
                    newActivityCreated, mLastStartActivityRecord, originalOptions);
            if (mRequest.waitResult != null) {
                mRequest.waitResult.result = res;
                res = waitResultIfNeeded(mRequest.waitResult, mLastStartActivityRecord,
                        launchingState);
            }
            return getExternalResult(res);
        }
    } finally {
        // Called when execution is complete. Sets state indicating completion and proceeds with recycling if appropriate.
        onExecutionComplete();
    }
}
```
Executing activity start request and starts the journey of starting an activity. Here begins with performing several preliminary checks. The normally activity launch flow will go through startActivityUnchecked to startActivityInner.
```java
private int executeRequest(Request request) {
    ...
    mLastStartActivityResult = startActivityUnchecked(r, sourceRecord, voiceSession,
            request.voiceInteractor, startFlags, true /* doResume */, checkedOptions, inTask,
            restrictedBgActivity, intentGrants);
    ...
    return mLastStartActivityResult;
}
```
Start an activity while most of preliminary checks has been done and caller has been confirmed that holds necessary permissions to do so. Here also ensures that the starting activity is removed if the start wasn't successful.
```java
private int startActivityUnchecked(final ActivityRecord r, ActivityRecord sourceRecord,
            IVoiceInteractionSession voiceSession, IVoiceInteractor voiceInteractor,
            int startFlags, boolean doResume, ActivityOptions options, Task inTask,
            boolean restrictedBgActivity, NeededUriGrants intentGrants) {
    ...
    try {
        ...
        result = startActivityInner(r, sourceRecord, voiceSession, voiceInteractor,
                startFlags, doResume, options, inTask, restrictedBgActivity, intentGrants);
    } finally {
        ...
    }

    ...

    return result;
}
```
Start an activity and determine if the activity should be adding to the top of an existing task or delivered new intent to an existing activity. Also manipulating the activity task onto requested or valid root-task/display. Note: This method should only be called from startActivityUnchecked.
```java
int startActivityInner(final ActivityRecord r, ActivityRecord sourceRecord,
    IVoiceInteractionSession voiceSession, IVoiceInteractor voiceInteractor,
    int startFlags, boolean doResume, ActivityOptions options, Task inTask,
    boolean restrictedBgActivity, NeededUriGrants intentGrants) {
    ...
    mTargetRootTask.startActivityLocked(mStartActivity,
        topRootTask != null ? topRootTask.getTopNonFinishingActivity() : null, newTask,
        mKeepCurTransition, mOptions, sourceRecord);
    ...
    return START_SUCCESS;
}
```
