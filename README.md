
The main goal is to define a reference architecture that can be used as the foundation for Flutter app that require the usage of Firebase and eventually:
To reduce developer time in re-writing common code and project structure from scratch
To have a common folder and package structure that can be further extended should it require
Clearing define the app layer — UI layer, business logic layer and service layer
The 3 main layers:
UI layer — all UI widgets files
Business logic layer — contains all app business and presentation logic
Service layer — contains services needed to interact with between the app and 3rd party services such as APIs
This project bundled with a demo app as a practical example of this architecture. This is very important and useful for anyone to jump start right into the code and working app. From there, we can make modification as require for easily, faster and convenient.
The demo app is a working Todo app that comes with Firebase authentication using email and password and full CRUD capabilities.
The app demonstrates a simple but a comprehensive enough to capture the various challenges and use cases of state management across multiple screens and different modules.

Folder Structure

 
What inside constants?
Basically, all needed constant files for the application to work will be here.

constants/
|- app_themes.dart – the theme file for the app
|- app_font_family.dart – the app global supported font family
|- app_strings.dart – the strings value used by the app

What inside cache?
As of now, only shared preferences files. In future we may have others such as sqlite features and it is good to keep things separated.
caches/
|- sharedpref/shared_preference_helper.dart

What inside models?
All plain data models file will be here. Depending on the number of the model files, if it is more than 10 files, we suggest separating the files by sub folder such as models/auth, models/todo etc. Else, keep it simple with every data model file is within this folder.
models/
|- user_model.dart
|- todo_model.dart

What inside services?
As we have backend firestore and firebase for application data such as user and todo data, we will need a dart classes that represent the CRUD services to handle any communication between the UI code and the backend.
services/
|- firestore_database.dart
|- firestore_service.dart
|- firestore_path.dart

What inside providers?
Contains all provider models needed for the app. The idea is like those in models and services in which, different provider class will be created for different key modules.
providers/
|- auth_provider.dart
|- theme_provider.dart

What inside UI?
This directory contains all the UI files for the application. As most of the times, there will be more than 1 files representing a screen, thus is always good to separate it by sub-folder and grouping it based on their key features or modules.
ui/
|- auth
        |- register_screen.dart
        |- sign_in_screen.dart
|- splash
        |- splash_screen.dart
|- home
        |- home.dart
|- todo
        |- todos_screen.dart
        |- create_edit_todo_screen.dart

Use Case: Firestore Service
Any widgets module can subsribe to updates from Firebase Firestore through streams, as well as write operation such as creation and deletion of data using Future-based APIs.
For convenient, all Firestore related CRUD services for the demo app is all in 1 place:
class FirestoreDatabase { // implementation omitted for brevity
  Future<void> setTodo(TodoModel todo); // create / update
  Future<void> deleteTodo(TodoModel todo); // delete
  Stream<List<TodoModel>> todosStream(); // read
  Stream<Job> todoStream({@required String todoId}); // read
}

With this setup, creating a widget that shows a list of jobs becomes simple:
@override
Widget build(BuildContext context) {
  final database = Provider.of<FirestoreDatabase>(context, listen: false);
  return StreamBuilder<List<Job>>(
    stream: database.todosStream(),
    builder: (context, snapshot) {
      // TODO: return widget based on snapshot
    },
  );
}
Domain-level model classes are defined, along with fromMap() and toMap() methods for serialization.

Take note on stream-dependent services
When using Firestore, is quite common to organise user data inside documents or collections that depend on the unique user uid.
When reading or writing data, the app need to access to the user uid. This can change at runtime, as user can log out and sign back in with a different account, which eventually a different uid.
This, the FirestoreDatabase takes the uid as a constructor parameter. So, the moment we have FirebaseUser upon user logged in, we can just pass the uid to the Firestore database when performing CRUD operations.
To achieve this, FirestoreDatabase will be re-created everytime onAuthStateChanged changed.


Step1:
open the android/app/build.gradle file and change the applicationId to your own application ID. For example, com.siteworx.create_flutter_provider_app

Step2:
Go to the project root and execute the following command to get all dependencies packages: flutter pub get

Step3:
Go to your Firebase console(https://console.firebase.google.com/). Create a new Firebase project. Once created without any issue, register your app by clicking the icon – for Android, click on the Android icon and for iOS, click on the iOS icon. Complete the rest of the step require.

Step4:
Ensure the Android package name is the same value as your application ID setup in step 1.

Step5: 
Download the google-services.json file provided by Firebase to the android/app directory. Then, update the Gradle build files under android/build.gradle to register the app with Google services.
 

Step6:
Check/Update the android/app/build.gradle file by adding the following highlighted code:
 

Step7:
Go back to your Firebase console to create the database Cloud Firestore. Choose the test mode option. You can change it later, just make sure to choose the nearer Cloud Firestore location nearer to your location.
 

Choose the test mode option and click next
 

choose the nearer Cloud Firestore location nearer to your location and enable it
 

Result:
 


Step8:

Still within Fiebase console, go to Authentication and turn on the sign-in method. In our case, that would be email/password have to turn on by setting the status to enabled. Other sign-in method will be added soon.

 
email/password have to turn on by setting the status to enabled
 

Result:
 
Possible Errors:
Pssibility of getting srror with
def localProperties = new Properties()

throw  new GradleException
 ("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")

in app\build.gradle file
Solution:
1. File > Project Structure (Command + ;) > Under Project Settings/Project > Set Project SDK to Android API 29 Platform
 
 
2. There should be a notification stating there is an invalid item in the dependencies list under Project Structure/Problems. If there is, go to Project Structure > Under Project Settings/Modules > select app_name_android > Dependencies tab > choose the latest "Android API 29 Platform" in the Module SDK box.
 
 
 
3. Update GradleException() to FileNotFoundException() under android/app/build_gradle since it's not supported in the Java version of Android API 29

That's it! You should notice the Android API 29 Platform is now visible under External Libraries.
 
If you still have issue, another possible solution could be,….
Just restart and kill the chache in your Android Studio (File -> Invalidate Chache / Restart).
Step9:
Done. Try launch the app using emulator or your preferred physical test device. For first time user, you need to create a new account from the app.

Possible Errors 1:
Exception: Gradle build failed to produce an .apk file. It's likely that this file was generated under D:\PrasadRay\Siteworx\create_flutter_provider_app\build, but the tool couldn't find it.
Solution:
Add below code in App label build.gradle file after buildTypes:
 
And Modify your Edit Configurations like below Image :
 















