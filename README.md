# ionic-babylon
## A brief example showing how to use BabylonJS in an Ionic 2 application ##

For anyone interested, it is posible to integrate BabylonJS in an Ionic/Cordova app in just a few easy steps. The following is just a narrative of how I did it, since it's been a trial and error thing probably there'll be a better way of doing it, when in doubt I've gone for simplicity rather than optimisation. 

## What you need

First, you need node/npm installed. 

Second, and quite obviously, a working Ionic environment. How to do this is very well documented by the Ionic people so, if you haven't done it before have a look [there](https://ionicframework.com/docs/intro/tutorial/).

## The process

1) Start a new Ionic project, I used the blank template, with the most basic scaffolding, and removed all unnecessary stuff.

2) Install BabylonJS within the project:
```bash
npm install babylonjs@2.5.0 --save
```
>A couple things to remember: 
> * Be careful not to write babylon (different package). 
> * Specify version, otherwise npm will install the preview branch and things will fall apart at runtime!

3) Go to the last line of babylon.d.ts (in node_modules/babylonjs/dist) and type:

```typescript
export = BABYLON;
```  
>These last two steps will create the proper entry in the node_modules folder and will let you do:
> ```typescript
>import BABYLON from 'babylonjs'
>```  
>wherever you need to.

4) Depending of what you intend to do in your app, other dependencies might be needed (e.g. cannon.js or hand.js) 
You could install them the same way as BabylonJS, but I just downloaded the javascript files, saved them in the www folder and used script tags in `index.html`. However, this has a downside, the www folder is generated after transpiling, so you will need to add those javascript files **manually**.


5) After this I generated an Angular [Provider](https://docs.angularjs.org/guide/providers), this is a singleton class that will wrap our BabylonJS Engine: 
```bash
ionic g provider BabylonJS
```  
>Besides holding our Engine instance, being in a provider allows for different pages to access the same Engine, e.g. you define different scenes in different pages and use the same Engine provider for all.

6) Another provider (ConfigProvider) is created to handle all necessary configuration variables. In this case it just contains the dice colors and texture for both dice.

7) The very basic configuration interface is an Ionic page showing the available options. Note the header, a basic Ionic component that handles the back button automatically.  
All the values are stored in the Config service, that makes them available to any page that declares a dependency to the Config provider.

8) The Home page will contain the BabylonJS canvas. An Ionic page is basically an Angular component with some sugar added, mainly in the form of a few helpful life cycle hooks.  
The whole scene is created here, note that we are using the two providers that were previously generated.  
The ViewChild decorator is needed in order to have access to the canvas element. 

## A few notes on the code

The app itself is a very basic dice roller, but there are a few things worth mentioning:
* We are using Ionic components to provide a basic user interface, with a header and a couple FAB buttons to access the configuration screen and roll the dice. See the methods handling the button events.
* The Home page code makes use of `ionViewDidLoad` and `ionViewWillEnter`, both are hooks to the page life cycle. The first fires only once, when the view loads, so it is ideal to initialize variables and generate the scene proper.  
The second fires every time the view is going to become active, it checks for possible changes in the configuration and modifies the vertex data to point to the correct coordinates if necessary.  
* Ionic, by default, caches the views. so anything included in the `ionViewDidLoad` hook will run only once, so the methods createScene() and animate() are called only once.

## Conclusion

Integrating BabylonJS in an Ionic application was easier than it seemed at first, once you get to know a couple of thing of how Ionic/Angular 2 works. In fact, doing the same in an Ionic 1 app was easy too, but overall everything looks cleaner, more streamlined in the latest version.

However, despite the improvements introduced to Angular, the overhead of having both Ionic and BabylonJS together might make performance a bit sluggish. Maybe it's not the best way of implementing a pure 3d action packed game.  

On the other hand, with Ionic you get things, like a very simple way of handling the user interface, navigation and storage out of the box, so it might be the way to go for less demanding apps (I'm actually considering a 2D platform game to test this).

Finally, this is how the app looks running on an actual Android 6.0 device:
![](screenshot1.jpg)
![](screenshot2.jpg)
![](screenshot3.jpg)
