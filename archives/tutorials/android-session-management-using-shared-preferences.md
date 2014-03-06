[![androidhive](http://www.androidhive.info/wp-content/themes/androidhive_v2_analytics/img/logo.png)](http://www.androidhive.info)
August 26, 2012 02:15 PM

Android User Session Management using Shared Preferences
========================================================
Session are useful when you want to store user data globally through out
the application. This can be done in two ways. One is storing them in a
global variables and second is storing the data in shared preferences.
The problem with storing data in global variable is data will be lost
once user closes the application, but storing the data in shared
preferences will be persistent even though user closes the application.

[![Download Code](http://www.androidhive.info/wp-content/uploads/2011/08/download_btn.png "Download Code")](http://download.androidhive.info/download?code=rMlczdOS6Az7Q8pvKTmfS9THmzI8qjcqByg6QOMZAtkPZrKWSxtCeATtumBaywCDosdaxG2zVSPdtbuCM7NfvhQRalBqAaSKhyr7Sia7GtKfgVkpjXc1JSCGg%3D%3D2Xv8f3uVzfMGBgEcqwK0ox6AO0mj6bNb5qV)

 Application shared preferences allows you to save and retrieve key,
value pair data. Before getting into tutorial, I am giving basic
information needed to work with shared preferences.

### Initialization

Application shared preferences can be fetched using
[getSharedPreferences()](http://developer.android.com/reference/android/content/Context.html#getSharedPreferences(java.lang.String,%20int))
method.You also need an editor to edit and save the changes in shared
preferences. The following code can be used to get application shared
preferences.

    SharedPreferences pref = getApplicationContext().getSharedPreferences("MyPref", 0); // 0 - for private mode
    Editor editor = pref.edit();

  

### Storing Data

You can save data into shared preferences using editor. All the
primitive data types like booleans, floats, ints, longs, and strings are
supported. Call **editor.commit()** in order to save changes to shared
preferences.

    editor.putBoolean("key_name", true); // Storing boolean - true/false
    editor.putString("key_name", "string value"); // Storing string
    editor.putInt("key_name", "int value"); // Storing integer
    editor.putFloat("key_name", "float value"); // Storing float
    editor.putLong("key_name", "long value"); // Storing long

    editor.commit(); // commit changes

  

### Retrieving Data

Data can be retrived from saved preferences by calling
[getString()](http://developer.android.com/reference/android/content/SharedPreferences.html#getString(java.lang.String,%20java.lang.String))
(For string) method. Remember this method should be called on Shared
Preferences not on Editor.

    // returns stored preference value
    // If value is not present return second param value - In this case null
    pref.getString("key_name", null); // getting String
    pref.getInt("key_name", null); // getting Integer
    pref.getFloat("key_name", null); // getting Float
    pref.getLong("key_name", null); // getting Long
    pref.getBoolean("key_name", null); // getting boolean

  

### Clearing / Deleting Data

If you want to delete from shared preferences you can call
**remove(“key\_name”)** to delete that particular value. If you want to
delete all the data, call **clear()**

    editor.remove("name"); // will delete key name
    editor.remove("email"); // will delete key email

    editor.commit(); // commit changes

Following will clear all the data from shared preferences

    editor.clear();
    editor.commit(); // commit changes

  
  
 The following is a simple tutorial which will have a login form and a
dashboard screen. At first user will login using login details and once
he successfully logged in his credentials (name, email) will be stored
in shared preferences.

——————————————————————————————————————————————————-

### User Session Management using Shared Preferences

**1**. Create a new project in Eclipse IDE **File ⇒ New ⇒ Android
Application Project** and fill all the required details  
 **2**. I am adding alert dialog manager class to show alert messages
while validating the login form. Create a new class and name it as
**AlertDialogManager.java** and paste the following code.

    package com.androidhive.sessions;


    import android.app.AlertDialog;
    import android.content.Context;
    import android.content.DialogInterface;
     
    public class AlertDialogManager {
        /**
         * Function to display simple Alert Dialog
         * @param context - application context
         * @param title - alert dialog title
         * @param message - alert message
         * @param status - success/failure (used to set icon)
         *               - pass null if you don't want icon
         * */
        public void showAlertDialog(Context context, String title, String message,
                Boolean status) {
            AlertDialog alertDialog = new AlertDialog.Builder(context).create();
     
            // Setting Dialog Title
            alertDialog.setTitle(title);
     
            // Setting Dialog Message
            alertDialog.setMessage(message);
     
            if(status != null)
                // Setting alert dialog icon
                alertDialog.setIcon((status) ? R.drawable.success : R.drawable.fail);
     
            // Setting OK Button
            alertDialog.setButton("OK", new DialogInterface.OnClickListener() {
                public void onClick(DialogInterface dialog, int which) {
                }
            });
     
            // Showing Alert Message
            alertDialog.show();
        }
    }

**3**. I am writing all session related functions in one class to make
them available in all activities. Create a new class named
**SessionManagement.java** and add following lines of code

    package com.androidhive.sessions;

    import java.util.HashMap;

    import android.content.Context;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    public class SessionManager {
        // Shared Preferences
        SharedPreferences pref;
        
        // Editor for Shared preferences
        Editor editor;
        
        // Context
        Context _context;
        
        // Shared pref mode
        int PRIVATE_MODE = 0;
        
        // Sharedpref file name
        private static final String PREF_NAME = "AndroidHivePref";
        
        // All Shared Preferences Keys
        private static final String IS_LOGIN = "IsLoggedIn";
        
        // User name (make variable public to access from outside)
        public static final String KEY_NAME = "name";
        
        // Email address (make variable public to access from outside)
        public static final String KEY_EMAIL = "email";
        
        // Constructor
        public SessionManager(Context context){
            this._context = context;
            pref = _context.getSharedPreferences(PREF_NAME, PRIVATE_MODE);
            editor = pref.edit();
        }

**4**. In this example i am storing **login status**, **name**,
**email** in shared preferences, so i added a function called
**createLoginSession(String name, String email)** to SessionManagement
class. Add the following function to **SessionManagement.java**.

**\>** This function simply stores login status(true), name, email in
shared preferences.

    /**
         * Create login session
         * */
        public void createLoginSession(String name, String email){
            // Storing login value as TRUE
            editor.putBoolean(IS_LOGIN, true);
            
            // Storing name in pref
            editor.putString(KEY_NAME, name);
            
            // Storing email in pref
            editor.putString(KEY_EMAIL, email);
            
            // commit changes
            editor.commit();
        }

**5**.In order to get the stored preferences data, I added a function
called **getUserDetails()** with the following code.

**\>** The following function will read shared preferences and returns
user data in **HashMap**

    /**
         * Get stored session data
         * */
        public HashMap<String, String> getUserDetails(){
            HashMap<String, String> user = new HashMap<String, String>();
            // user name
            user.put(KEY_NAME, pref.getString(KEY_NAME, null));
            
            // user email id
            user.put(KEY_EMAIL, pref.getString(KEY_EMAIL, null));
            
            // return user
            return user;
        }

**6**. To check whether user logged in or not i added a function
**checkLogin()** which you can call in all Activities to check user
login status.

**\>** This function simply check user login status from shared
preferences and **if user is not login it will redirect user to
LoginActivity**

    /**
         * Check login method wil check user login status
         * If false it will redirect user to login page
         * Else won't do anything
         * */
        public void checkLogin(){
            // Check login status
            if(!this.isLoggedIn()){
                // user is not logged in redirect him to Login Activity
                Intent i = new Intent(_context, LoginActivity.class);
                // Closing all the Activities
                i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
                
                // Add new Flag to start new Activity
                i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                
                // Staring Login Activity
                _context.startActivity(i);
            }
            
        }

**7**. Add a function called **logoutUser()** to clear all the data from
shared preferences. Call this function when you want to logout the user.

**\>** This function clears all session data and redirect the user to
LoginActivity

    /**
         * Clear session details
         * */
        public void logoutUser(){
            // Clearing all data from Shared Preferences
            editor.clear();
            editor.commit();
            
            // After logout redirect user to Loing Activity
            Intent i = new Intent(_context, LoginActivity.class);
            // Closing all the Activities
            i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
            
            // Add new Flag to start new Activity
            i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            
            // Staring Login Activity
            _context.startActivity(i);
        }

**Final Code**

    package com.androidhive.sessions;

    import java.util.HashMap;

    import android.content.Context;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    public class SessionManager {
        // Shared Preferences
        SharedPreferences pref;
        
        // Editor for Shared preferences
        Editor editor;
        
        // Context
        Context _context;
        
        // Shared pref mode
        int PRIVATE_MODE = 0;
        
        // Sharedpref file name
        private static final String PREF_NAME = "AndroidHivePref";
        
        // All Shared Preferences Keys
        private static final String IS_LOGIN = "IsLoggedIn";
        
        // User name (make variable public to access from outside)
        public static final String KEY_NAME = "name";
        
        // Email address (make variable public to access from outside)
        public static final String KEY_EMAIL = "email";
        
        // Constructor
        public SessionManager(Context context){
            this._context = context;
            pref = _context.getSharedPreferences(PREF_NAME, PRIVATE_MODE);
            editor = pref.edit();
        }
        
        /**
         * Create login session
         * */
        public void createLoginSession(String name, String email){
            // Storing login value as TRUE
            editor.putBoolean(IS_LOGIN, true);
            
            // Storing name in pref
            editor.putString(KEY_NAME, name);
            
            // Storing email in pref
            editor.putString(KEY_EMAIL, email);
            
            // commit changes
            editor.commit();
        }   
        
        /**
         * Check login method wil check user login status
         * If false it will redirect user to login page
         * Else won't do anything
         * */
        public void checkLogin(){
            // Check login status
            if(!this.isLoggedIn()){
                // user is not logged in redirect him to Login Activity
                Intent i = new Intent(_context, LoginActivity.class);
                // Closing all the Activities
                i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
                
                // Add new Flag to start new Activity
                i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                
                // Staring Login Activity
                _context.startActivity(i);
            }
            
        }
        
        
        
        /**
         * Get stored session data
         * */
        public HashMap<String, String> getUserDetails(){
            HashMap<String, String> user = new HashMap<String, String>();
            // user name
            user.put(KEY_NAME, pref.getString(KEY_NAME, null));
            
            // user email id
            user.put(KEY_EMAIL, pref.getString(KEY_EMAIL, null));
            
            // return user
            return user;
        }
        
        /**
         * Clear session details
         * */
        public void logoutUser(){
            // Clearing all data from Shared Preferences
            editor.clear();
            editor.commit();
            
            // After logout redirect user to Loing Activity
            Intent i = new Intent(_context, LoginActivity.class);
            // Closing all the Activities
            i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
            
            // Add new Flag to start new Activity
            i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            
            // Staring Login Activity
            _context.startActivity(i);
        }
        
        /**
         * Quick check for login
         * **/
        // Get Login State
        public boolean isLoggedIn(){
            return pref.getBoolean(IS_LOGIN, false);
        }
    }

**8**. Until now we are done creating Session Management class and now
we are going to learn how to use this class in your application. For
this create a simple login form asking username, password.

Create a layout xml file and a class to create login form. Name the xml
file as **activity\_login.xml** and class name as **LoginActivity.java**

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="10dip">

        <!-- Email Label -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Username (Enter 'test')"
            android:singleLine="true"
            android:layout_marginBottom="5dip"/>
        
        <!-- Email input text -->
        <EditText android:id="@+id/txtUsername"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="10dip"/>
        
        <!-- Password Label -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Password (Enter 'test')"
            android:layout_marginBottom="5dip"/>
        
        <!-- Password input text -->
        <EditText android:id="@+id/txtPassword"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="20dip"
            android:password="true"
            android:singleLine="true"/>
        
        <!-- Login button -->
        <Button android:id="@+id/btnLogin"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Login"/>

    </LinearLayout>

For testing purpose i am checking **username**, **password** as
**test**, **test**.

Once the user enters correct login details a session will be created by
calling **session.createLoginSession(“Android Hive”,
“anroidhive@gmail.com”)** and user is redirected to **MainActivity**.

    package com.androidhive.sessions;

    import android.app.Activity;
    import android.content.Intent;
    import android.os.Bundle;
    import android.view.View;
    import android.widget.Button;
    import android.widget.EditText;
    import android.widget.Toast;

    public class LoginActivity extends Activity {
        
        // Email, password edittext
        EditText txtUsername, txtPassword;
        
        // login button
        Button btnLogin;
        
        // Alert Dialog Manager
        AlertDialogManager alert = new AlertDialogManager();
        
        // Session Manager Class
        SessionManager session;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_login); 
            
            // Session Manager
            session = new SessionManager(getApplicationContext());                
            
            // Email, Password input text
            txtUsername = (EditText) findViewById(R.id.txtUsername);
            txtPassword = (EditText) findViewById(R.id.txtPassword); 
            
            Toast.makeText(getApplicationContext(), "User Login Status: " + session.isLoggedIn(), Toast.LENGTH_LONG).show();
            
            
            // Login button
            btnLogin = (Button) findViewById(R.id.btnLogin);
            
            
            // Login button click event
            btnLogin.setOnClickListener(new View.OnClickListener() {
                
                @Override
                public void onClick(View arg0) {
                    // Get username, password from EditText
                    String username = txtUsername.getText().toString();
                    String password = txtPassword.getText().toString();
                    
                    // Check if username, password is filled                
                    if(username.trim().length() > 0 && password.trim().length() > 0){
                        // For testing puspose username, password is checked with sample data
                        // username = test
                        // password = test
                        if(username.equals("test") && password.equals("test")){
                            
                            // Creating user login session
                            // For testing i am stroing name, email as follow
                            // Use user real data
                            session.createLoginSession("Android Hive", "anroidhive@gmail.com");
                            
                            // Staring MainActivity
                            Intent i = new Intent(getApplicationContext(), MainActivity.class);
                            startActivity(i);
                            finish();
                            
                        }else{
                            // username / password doesn't match
                            alert.showAlertDialog(LoginActivity.this, "Login failed..", "Username/Password is incorrect", false);
                        }               
                    }else{
                        // user didn't entered username or password
                        // Show alert asking him to enter the details
                        alert.showAlertDialog(LoginActivity.this, "Login failed..", "Please enter username and password", false);
                    }
                    
                }
            });
        }        
    }

![android session login form](http://www.androidhive.info/wp-content/uploads/2012/08/android_session_login_form.png "android session login form")

![android login failed](http://www.androidhive.info/wp-content/uploads/2012/08/sessions_login_failed.png "android login failed")

**9**. After user redirected to MainActivity, I am getting stored
session data by calling **getUserDetails()** and displayed in textviews.

**\>** **session.checkLogin()** is called to check user login status.
Here if user is not login he will be redirected to LoginActivity.java  
 **\>** If user is logged in, user details are fetched by calling
**session.getUserDetails()** and displayed in textviews.  
 **\>** Also I have logout button which invokes a function
**session.logoutUser()** to clear the session data.  
 Layout code for **activity\_main.xml**

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="10dip">
        
        <!-- Name Label -->
        <TextView
            android:id="@+id/lblName"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:singleLine="true"
            android:layout_marginTop="40dip"
            android:layout_marginBottom="10dip"/>

        <!-- Email Label -->
        <TextView
            android:id="@+id/lblEmail"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:singleLine="true"
            android:layout_marginBottom="40dip"/>
        
        <!-- Button to show session data -->
        <Button android:id="@+id/btnLogout"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Logout"/>
    </LinearLayout>

Code for **MainActivity.java**

    package com.androidhive.sessions;

    import java.util.HashMap;

    import android.app.Activity;
    import android.os.Bundle;
    import android.text.Html;
    import android.view.View;
    import android.widget.Button;
    import android.widget.TextView;
    import android.widget.Toast;

    public class MainActivity extends Activity {
        
        // Alert Dialog Manager
        AlertDialogManager alert = new AlertDialogManager();
        
        // Session Manager Class
        SessionManager session;
        
        // Button Logout
        Button btnLogout;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            // Session class instance
            session = new SessionManager(getApplicationContext());
            
            TextView lblName = (TextView) findViewById(R.id.lblName);
            TextView lblEmail = (TextView) findViewById(R.id.lblEmail);
            
            // Button logout
            btnLogout = (Button) findViewById(R.id.btnLogout);
            
            Toast.makeText(getApplicationContext(), "User Login Status: " + session.isLoggedIn(), Toast.LENGTH_LONG).show();
            
            
            /**
             * Call this function whenever you want to check user login
             * This will redirect user to LoginActivity is he is not
             * logged in
             * */
            session.checkLogin();
            
            // get user data from session
            HashMap<String, String> user = session.getUserDetails();
            
            // name
            String name = user.get(SessionManager.KEY_NAME);
            
            // email
            String email = user.get(SessionManager.KEY_EMAIL);
            
            // displaying user data
            lblName.setText(Html.fromHtml("Name: <b>" + name + "</b>"));
            lblEmail.setText(Html.fromHtml("Email: <b>" + email + "</b>"));
            
            
            /**
             * Logout button click event
             * */
            btnLogout.setOnClickListener(new View.OnClickListener() {
                
                @Override
                public void onClick(View arg0) {
                    // Clear the session data
                    // This will clear all session data and 
                    // redirect user to LoginActivity
                    session.logoutUser();
                }
            });
        }
            
    }

![android user session data](http://www.androidhive.info/wp-content/uploads/2012/08/session_get_details.png "android user session data")

**10**. Open **AndroidManifest.xml** file, add the following code and
run the project.

    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.androidhive.sessions"
        android:versionCode="1"
        android:versionName="1.0" >

        <uses-sdk
            android:minSdkVersion="8"
            android:targetSdkVersion="15" />

        <application
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name=".MainActivity"
                android:label="Dashboard Screen" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
            
            <!-- DashBoard / MainActivity -->
            <activity
                android:name=".LoginActivity"
                android:label="User Login" >
            </activity>
        </application>

    </manifest>

![android user session management](http://www.androidhive.info/wp-content/uploads/2012/08/banner_android_sessions2.png "android user session management")