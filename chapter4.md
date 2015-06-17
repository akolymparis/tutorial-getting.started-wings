<a name="Chapter4"></a>
# Chapter 4 – The Client API

  - [4.1 – A Basic Client class layout](chapter4.md#Step1)
  - [4.2 – The SignIn Method](chapter4.md#Step2)
  - [4.3 – Starting a Registered Trial](chapter4.md#Step3)
  - [4.4 – Starting an Anonymous Trial](chapter4.md#Step4)
  - [4.5 – Activating a Subscription](chapter4.md#Step5)

In the previous chapter we explored the web-console and prepared the ground for what is to come by creating an application and within it some service plans. Now its time to get our handy dirty with a bit of code. This tutorial assumes you are familiar with the Visual Studio environment and the C# programming language. So let's begin!

![Create Solution](img/Image4.1.png)

The first step is to setup our environment. Open Visual Studio and create a new Visual C# Class Library Project with a suitable name. The interface might ask you to name the solution or it might name it automatically with the same name you gave to the project. Make sure you have “Create directory for solution” checked. Hit OK and the project is created with a sample Class1.cs file. You can delete that if you wish.

![Add Reference](img/Image4.2.png)

Now we need to add a reference to the “WorkstationClient.dll” to our project. Expand the project's References and select Add Reference..., then Browse to where the dll resides on your disk and select it, click Add, then make sure WorkstationClient.dll has a checked checkbox and click OK.

![Select Dll](img/Image4.3.png)
![References](img/Image4.4.png)

  <a name="Step1"></a>
## 4.1 – A Basic Client class layout

Now you need to create a C# class, name it something like “SampleClient” and follow the next steps:
* add the “public” keyword in front of the class' declaration
* add “using Plexscape.WebServices.Core;”
* add “using Plexscape.WebServices.WorkstationClient;”
* derive from IDisposable.
  
Then proceed to modify the implementation of the class likewise:

```
public class SampleClient : IDisposable
{
	/// <summary>
	/// Use a single client object per session.
	/// Created during construction by Factory.
	/// </summary>
	private IWorkstationClient Client;

	public void Dispose()
	{
		if (Client != null)
		{
			Client.UnloadClient();
		}
	}
}

```
Two things happen here. First we add a private field called Client of type IWorkstationClient. IWorkstationClient is an interface describing all the public methods available to the client. In essence, our “SampleClient” class acts as a wrapper to the IWorkstationClient in order to expose methods that are closer to our needs. Next we implement the Dispose method from IDisposable. IWorkstationClient objects need to implement an UnloadClient method which should be called during disposal of any object that contains such a client reference.

Up to this point we haven't seen how we would obtain an object that implements IWorkstationClient though. The good part is, we don't need to implement this interface. In fact we don't need to know much about the inner workings of such an object and how it communicates with the server, and we don't need to know anything at all about the server. We only need to receive an object and be able to use its implementations of the IWorkstationClient methods. And the library provides us this object through a Factory. Let's take a look at the constructor of our class:

```
public PluginClient()
{
	// Get an instance of the workstation client
	Client = WorkstationClientFactory.CreateClient();

	// Populate a dictionary with (client-side) environment values
	Dictionary<string, string> environmentData = new Dictionary<string, string>() {
		{ PlexDtoKeys.Product, "SamplePlugin" },         // Required
		{ PlexDtoKeys.Version, "1.0.0.0" },             // Required
		{ PlexDtoKeys.StationName, "sample-test-station" },     // Optional
		{ PlexDtoKeys.ApplicationReference, "E2F7911DB8184749A74BD2BD97E3369C" }  // Required
	};

	if (!Client.LoadClient(environmentData))
	{
		foreach (PlexError error in Client.GetErrors())
		{
			string output = string.Format(" Error {0} for action '{1}', Message: {2}", error.Code, "LOAD", error.Message);
			Console.WriteLine(output);
		}
		Console.WriteLine();
	}
}
```

WorkstationClientFactory is a class provided by the WorkstationClient API that allows the creation of objects conforming to the IWorkstationClient interface without the developer having to implement it himself. Thus the heavy-lifting of the client-server communication is taken from the developer's hands and placed on the API itself. But not everything is done magically by the workstation client, however nice that would've been. The developer needs to have an understanding on what data needs to be sent to the server, depending on what the client requests, and in what form these data should be.

This is where the next declaration comes into play:

```c#
Dictionary<string, string> environmentData = new Dictionary<string, string>() {
		{ PlexDtoKeys.Product, "SamplePlugin" },         // Required
		{ PlexDtoKeys.Version, "1.0.0.0" },             // Required
		{ PlexDtoKeys.StationName, "sample-test-station" },     // Optional
		{ PlexDtoKeys.ApplicationReference, "E2F7911DB8184749A74BD2BD97E3369C" }  // Required
	};
```
Here we see three things:
* First, the data will be organized in a Dictionary format, where both the key and the value needs to be a string.
* Second, the Keys of the dictionary are predefined constant values, defined in the PlexDtoKeys class. For the dictionary to be considered valid, the keys must be taken from these values.
* Third, the dictionary can have as many records as the developer needs, but some entries are REQUIRED as denoted by the comments next to them in the above code.

There is something else we see here, invoking memories from part 1 of our tutorial! In the required entry that specifies the application which we want to reference, the value is the reference key that was generated for us when we created our Tutorial Application in the web console in part 1. It is assumed that the developer knows this identifier at the time he starts developing his client. (Typically, he will either take the key himself from the web console like we did, or it will be communicated to him from Plexscape).

The next step is to try and Load the client. The client's LoadClient method takes the environment data we specified in the previous step as a parameter and returns a bool value indicating whether the action was successful or not. It is apparent then why we call this directly in the “if” statement:
we check directly if the call returned a false value in which case we proceed to manage the errors.
The next few lines of code use the client's GetErrors method and prints each of them to the console.
Note that errors are returned in the form of a class named PlexError, which provides us with an Error Code and a Message to gives more information about what occurred.

Before we proceed with implementing more functionality to our SampleClient class we should create some helper classes and some constants just to make our life easier. Go ahead and add a new C# class item under your project and name it SampleConstants. Add the next few values as static fields in the class. We will add more values as the tutorial progresses.

```
public static class SampleConstants
{
	/// <summary>
	/// An reference to your own application that is provided by Plexscape.
	/// </summary>
	internal static readonly string PX_SAMPLE1_APP_REFERENCE = "EDADB7CEE1F6434B848A827CD6088A91";
	/// <summary>
	/// Product info.
	/// </summary>
	internal static readonly string Product_Name = "SamplePlugin";
	internal static readonly string Product_Version = "1.0.0.0";
	internal static readonly string Product_StationName = "sample-tutorial-station";
}
```

  <a name="Step2"></a>
## 4.2 – The SignIn Method

Now everything is in place for us to start implementing the actual client functionality. The first thing we are going to need is to take a look at the methods exposed by the *IWorkstationClient* interface. Among the many methods first we must discuss **InitWorkstation** and **GetServiceStatus**.
The first is a very important call and it is actually the first call we need to make when beginning our session. In essence it asks the server to check if the workstation is activated with a license and everything is OK to proceed. Besides the start of the session there are a few more points at which we have to make this call, like after attempting a subscription activation with a positive response, in order to complete the activation process. The return value is boolean to define success or failure. If the call fails it means there are errors, which we can retrieve with **Client.GetErrors()** and we can show them to the user if we want.
**GetServiceStatus** on the other hand can be called whenever we need to check with the status of the server. Progress of requests, errors, failures in requests or successful calls are all described by an arithmetic number returned by this method. It also has an out parameter that returns more specific info in the well known by now dictionary format. The status is returned as an *int* code and it is a best practice to keep their handling within the scope of the client class and hidden from the UI and the final user.

Let's create a method that will be called to begin the session when the client starts. In case that there are errors we proceed to display them via the displayer and return false. The call to InitWorkstation is the bread and butter here. If it is successful it means that the server is ready and the workstation has been activated. If not, then either there is some internal server error or the client is not activated yet and needs activation.

```
public bool StartClient()
{
	// Initialize the workstation
	if (!Client.InitWorkstation())
	{
		Display.ShowErrors(Client.GetErrors(), "INIT");
		return false;
	}
	return true;
}
```

Then go back to the SampleClient class and create the method that will handle server status codes. We'll call it CheckoutWorkstation, and it's purpose will be to ask the servers for its current status and to distinguish between status codes, take proper action if we want and return the appropriate value from the enumerator. This will actually be the most central call of the client. It should be called after a request to the server that has a chance to fail and it should be called whenever we want to check if the workstation checks out. What does that mean? It means that the workstation has asked the server “Do I have an active license in your records?” and the server responded “yes”.
In any other case, we will see some soon, or if the server has faced some internal problem then the workstation doesn't or cannot checkout and the method handles the status and returns accordingly.

```
public SampleStates CheckoutWorkstation(
            bool showErrors = true,
            bool showResponse = true)
{
	// Get current status to provide info to caller
	Dictionary<string, string> statusData;
	if (!Client.GetServiceStatus(out statusData))
	{
		if (showErrors)
			Display.ShowErrors(Client.GetErrors(), "STATUS");
	}
	else
	{
		if (showResponse) // Display status data
			Display.ShowResponseData("  Status values received:", statusData);
	}

	PlexError error = Client.GetLastError();
	int status = (error != null) ? error.Code : PlexResponseCode.OK;

	if (status == PlexResponseCode.SERVER_ERROR_BASE)
		return SampleStates.SERVER_ERROR;
	else if (status > PlexResponseCode.SERVER_ERROR_BASE)
		return SampleStates.FAILURE;
	else
		return SampleStates.SUCCESS;
}
```
We can create and start the client and see if the workstation is activated or not. Now we should take care of what can be done if it is not.

  <a name="Step3"></a>
## 4.3 – Starting a Registered Trial

  <a name="Step4"></a>
## 4.4 – Starting an Anonymous Trial

  <a name="Step5"></a>
## 4.5 – Activating a Subscription
=========================
[Next](chapter2.md#Chapter2) -
[Home](README.md)