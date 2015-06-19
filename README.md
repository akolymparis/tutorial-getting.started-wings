# PlexScape Wings - Web Console and API tutorial

* Introduction
  - [Audience](#Audience)
  - [What is Wings?](#WhatIsWings)
  - [What do you need for your project?](#WhatDoYouNeed)
  - [What are you going to achieve in this workshop?](#WhatAreYouGoingToAchieve)
  - [Create an Account](#AccountCreate)

* [Chapter 1 – The Application Application](chapter1.md#Chapter1)
  - [Meet the Web Console](chapter1.md#WebConsole)
  - [Create a new application](chapter1.md#CrateApplication)
  - [Add commands to the application](chapter1.md#CreateCommands)

* [Chapter 2 – Free Trials](chapter2.md#Chapter2)
  - [Creating a Registered Trial](chapter2.md#Registered)
  - [Creating an Anonymous Trial](chapter2a.md#Anonymous)

* [Chapter 3 – Subscriptions](chapter3.md#Chapter3)
  - [3.1 – Creating a subscription Service Plan](chapter3.md#Step1)
  - [3.2 – Creating a subscription](chapter3.md#Step2)
  
* [Chapter 4 – The Client API](chapter4.md#Chapter4)
  - [4.1 – A Basic Client class layout](chapter4.md#Step1)
  - [4.2 – The SignIn Method](chapter4.md#Step2)
  - [4.3 – Starting a Registered Trial](chapter4.md#Step3)
  - [4.4 – Starting an Anonymous Trial](chapter4.md#Step4)
  - [4.5 – Activating a Subscription](chapter4.md#Step5)

* [Chapter 5 – The Plugin](chapter5.md#Chapter5)
  - [5.1 – Setting up the environment](chapter5.md#Step1)
  - [5.2 – Creating the AutoCad Command class](chapter5.md#Step2)
  - [5.3 – Adding Wings functionality to the Plugin](chapter5.md#Step3)
  
* [Appendix A – More sample and demos](appendixa.md)


<a name="Audience"></a>
## Audience

You have developed a great tool, application or plugin. You have found the best way in which to deploy this new creation to customers and you have even decided on the best way to monetize your product. But here's the catch. Suppose you had already have an older product, which is successful and it provides income through some sort of payment model that is appropriate for it. Now, your new product, very likely needs a different model more fitting to its functionality and use. This introduces some interesting and perhaps choices as well as some extra work depending on these choices. Do you make changes to your new payment model so can keep your extra work at a minimum by utilizing parts or whole of the older one? Do you implement a new system that utilizes the new model in its entirety? If you did not implement the payment system for your previous work but had signed an agreement with a third party, do you now search for another third party that can support your new model and sign different agreements?
Wouldn't it be extremely helpful if there was a platform that allowed you to manage payment plans and models, however different they might be, for all your applications?
Welcome to [Wings](#WhatIsWings)!

This tutorial targets developers that have at least a basic understanding of C# .Net and have some experience at creating plugins for AutoCad, although
if you do not want your first application to be an autocad plugin you are good to go with just C# knowledge!

<a name="WhatIsWings"></a>
## What is Wings?

Simply put, Wings is a Licensing platform that allows developers to create and manage payment models, service plans and subscriptions. But it is not only that.
By defining commands, a developer can set  rules and limitations that apply to specific subscriptions or service plans and make behaviour variations. Furthermore
it is possible to even upload custom code that can run on the server and even store and collaboratively modify files!

<a name="WhatDoYouNeed"></a>
## What do you need for your project?

For the purposes of this tutorial you are going to need first and foremost an IDE compatible with .NET C#, preferably Visual Studio 2012+.
For creating a client that can communicate with the platform and do actions like activating a subscription for the workstation your application runs on
you need the Wings' WorkstationClient API, which can be downloaded [here]().

Finally, since in this tutorial we are creating an AutoCad plugin as a sample application, you will need the  autocad .net api libraries that come
with an autocad installation. If you don't want your application to be an autocad plugin feel free to modify the examples accordingly to meet your needs.

<a name="WhatAreYouGoingToAchieve"></a>
## What are you going to achieve in this workshop?

In this tutorial you will learn how to create a complete plugin for Auto Cad, register it to the {plexscapeplatform} and create payment models (aka service plans) for it and manage subscriptions.
In chapters 1, 2 and 3 you will discover the platform itself, learn it's terminology and functions and learn how to operate it through the web console. You will create a new application, add service plans to it, create subscriptions as needed and add command rules accordingly.
In chapter 4 you will go through the process of creating a client that can communicate with the platform's licensing server and either check if the workstation using the plugin has an active subscription or initiate one subscription for it based on the service plans you will have had created in the previous chapters.
Finally, in chapter 5, you will be creating a simple plugin with some functionality for Auto Cad, which will serve as the application for which we want to manage licensing for. This plugin will define two commands, an “Export Points” command and an “Import Points” command that will, as their names suggest, allow users to export selected points from a document to a text file and import points from such a file to a document. The plugin will
deploy the client you will have created earlier to determine whether the workstation it is running on is activated or not, and do the proper steps to activate a service plan if needed.

<a name="AccountCreate"></a>
## Create an Account

(Coming soon)


=========================
[Start](chapter1.md)
