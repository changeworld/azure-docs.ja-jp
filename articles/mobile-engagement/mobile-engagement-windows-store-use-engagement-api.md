<properties 
    pageTitle="How to Use the Engagement API on Windows Universal" 
    description="How to Use the Engagement API on Windows Universal"            
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-windows-universal"></a>How to Use the Engagement API on Windows Universal

This document is an add-on to the document [How to Integrate Engagement on Windows Universal](mobile-engagement-windows-store-integrate-engagement.md): it provides in depth details about how to use the Engagement API to report your application statistics.

Keep in mind that if you only want Engagement to report your application's sessions, activities, crashes and technical information, then the simplest way is to make all your `Page` sub-classes inherit from the `EngagementPage` class.

If you want to do more, for example if you need to report application specific events, errors and jobs, or if you have to report your application's activities in a different way than the one implemented in the `EngagementPage` classes, then you need to use the Engagement API.

The Engagement API is provided by the `EngagementAgent` class. You can access to those methods through `EngagementAgent.Instance`.

Even if the agent module has not been initialized, each call to the API is deferred, and will be executed again when the agent is available.

##<a name="engagement-concepts"></a>Engagement concepts

The following parts refine the common [Mobile Engagement Concepts](mobile-engagement-concepts.md) for the Windows Universal platform.

### <a name="`session`-and-`activity`"></a>`Session` and `Activity`

An *activity* is usually associated with one page of the application, that is to say the *activity* starts when the page is displayed and stops when the page is closed: this is the case when the Engagement SDK is integrated by using the `EngagementPage` class.

But *activities* can also be controlled manually by using the Engagement API. This allows you to split a given page in several sub parts to get more details about the usage of this page (for example to know how often and how long dialogs are used inside this page).

##<a name="reporting-activities"></a>Reporting Activities

### <a name="user-starts-a-new-activity"></a>User starts a new Activity

#### <a name="reference"></a>Reference

            void StartActivity(string name, Dictionary<object, object> extras = null)

You need to call `StartActivity()` each time the user activity changes. The first call to this function starts a new user session.

> [AZURE.IMPORTANT] The SDK automatically calls the EndActivity method when the application is closed. Thus, it is HIGHLY recommended to call the StartActivity method whenever the activity of the user changes, and to NEVER call the EndActivity method, since calling this method forces the current session to be ended.

#### <a name="example"></a>Example

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>User ends his current Activity

#### <a name="reference"></a>Reference

            void EndActivity()

This ends the activity and the session. You should not call this method unless you really know what you're doing.

#### <a name="example"></a>Example

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Reporting Jobs

### <a name="start-a-job"></a>Start a job

#### <a name="reference"></a>Reference

            void StartJob(string name, Dictionary<object, object> extras = null)

You can use the job to track certains tasks over a period of time.

#### <a name="example"></a>Example

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>End a job

#### <a name="reference"></a>Reference

            void EndJob(string name)

As soon as a task tracked by a job has been terminated, you should call the EndJob method for this job, by supplying the job name.

#### <a name="example"></a>Example

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Reporting Events

There is three types of events :

-   Standalone events
-   Session events
-   Job events

### <a name="standalone-events"></a>Standalone Events

#### <a name="reference"></a>Reference

            void SendEvent(string name, Dictionary<object, object> extras = null)

Standalone events can occur outside of the context of a session.

#### <a name="example"></a>Example

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Session events

#### <a name="reference"></a>Reference

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Session events are usually used to report the actions performed by a user during his session.

#### <a name="example"></a>Example

**Without data :**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**With data :**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Job Events

#### <a name="reference"></a>Reference

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Job events are usually used to report the actions performed by a user during a Job.

#### <a name="example"></a>Example

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Reporting Errors

There are three types of errors :

-   Standalone errors
-   Session errors
-   Job errors

### <a name="standalone-errors"></a>Standalone errors

#### <a name="reference"></a>Reference

            void SendError(string name, Dictionary<object, object> extras = null)

Contrary to session errors, standalone errors can occur outside of the context of a session.

#### <a name="example"></a>Example

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Session errors

#### <a name="reference"></a>Reference

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Session errors are usually used to report the errors impacting the user during his session.

#### <a name="example"></a>Example

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Job Errors

#### <a name="reference"></a>Reference

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Errors can be related to a running job instead of being related to the current user session.

#### <a name="example"></a>Example

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Reporting Crashes

The agent provides two methods to deal with crashes.

### <a name="send-an-exception"></a>Send an exception

#### <a name="reference"></a>Reference

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Example

You can send an exception at any time by calling :

            EngagementAgent.Instance.SendCrash(aCatchedException);

You can also use an optional parameter to terminate the engagement session at the same time than sending the crash. To do so, call :

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

If you do that, the session and jobs will be closed just after sending the crash.

### <a name="send-an-unhandled-exception"></a>Send an unhandled exception

#### <a name="reference"></a>Reference

            void SendCrash(Exception e)

Engagement also provides a method to send unhandled exceptions if you have **DISABLED** Engagement automatic **crash** reporting. This is especially useful when used inside the application UnhandledException event handler.

This method will **ALWAYS** terminate the engagement session and jobs after being called.

#### <a name="example"></a>Example

You can use it to implement your own UnhandledExceptionEventArgs handler. For example, add the `Current_UnhandledException` method of the `App.xaml.cs` file :

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

In App.xaml.cs in "Public App(){}" add:

            Application.Current.UnhandledException += Current_UnhandledException;

##<a name="device-id"></a>Device Id

            String EngagementAgent.Instance.GetDeviceId()

You can get the engagement device id by calling this method.

##<a name="extras-parameters"></a>Extras parameters

Arbitrary data can be attached to an event, an error, an activity or a job. These data can be structured using a dictionary. Keys and values can be of any type.

Extras data are serialized so if you want to insert your own type in extras you have to add a data contract for this type.

### <a name="example"></a>Example

We create a new class "Person".

            using System.Runtime.Serialization;
            
            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Then, we will add a `Person` instance to an extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] If you put other types of objects, make sure their ToString() method is implemented to return a human readable string.

### <a name="limits"></a>Limits

#### <a name="keys"></a>Keys

Each key in the object must match the following regular expression:

`^[a-zA-Z][a-zA-Z_0-9]*$`

It means that keys must start with at least one letter, followed by letters, digits or underscores (\_).

#### <a name="size"></a>Size

Extras are limited to **1024** characters per call.

##<a name="reporting-application-information"></a>Reporting Application Information

### <a name="reference"></a>Reference

            void SendAppInfo(Dictionary<object, object> appInfos)

You can manually report tracking information (or any other application specific information) using the SendAppInfo() function.

Note that this data can be sent incrementally: only the latest value for a given key will be kept for a given device. Like event extras, use a Dictionary\<object, object\> to attach data.

### <a name="example"></a>Example

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limits

#### <a name="keys"></a>Keys

Each key in the object must match the following regular expression:

`^[a-zA-Z][a-zA-Z_0-9]*$`

It means that keys must start with at least one letter, followed by letters, digits or underscores (\_).

#### <a name="size"></a>Size

Application information is limited to **1024** characters per call.

In the previous example, the JSON sent to the server is 44 characters long:

            {"birthdate":"1983-12-07","gender":"female"}

##<a name="logging"></a>Logging
###<a name="enable-logging"></a>Enable Logging

The SDK can be configured to produce test logs in the IDE console.
These logs are not activated by default. To customize this, update the property `EngagementAgent.Instance.TestLogEnabled` to one of the value available from the `EngagementTestLogLevel` enumeration, for instance:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
 



<!--HONumber=Oct16_HO2-->


