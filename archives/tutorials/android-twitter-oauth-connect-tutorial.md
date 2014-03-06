[![androidhive](http://www.androidhive.info/wp-content/themes/androidhive_v2_analytics/img/logo.png)](http://www.androidhive.info)

September 16, 2012 07:51 PM

Android Twitter oAuth Connect Tutorial
======================================

Integrating twitter in your android application will make user easily
login into your app using their twitter account which avoids filling a
long registration forms. In this tutorial i explained how to integrate
twitter in your android application using twitter oAuth procedure. This
is very basic tutorial with simple login and updating twitter status
message.

[![Download Code](http://www.androidhive.info/wp-content/uploads/2011/08/download_btn.png "Download Code")](http://download.androidhive.info/download?code=jWGDaoBFkP7JRcy9jci4H9tKt6BKtsA7DXXEa06TxmAGwNctcgruAq%2BNoVaZDlj3HVI%2F%2FY2SoBeVBpqMj3EFu6BMAXzJHiGCA0FCrJbZnRsAr2A6lRGEk1ojA%3D%3DmpHtHnyVqR8nynGxHAEZyYp60tlQaYdaZdk)

  

### Registering twitter app – getting Consumer Key & Consumer Secret

In order to implement twitter oAuth in your application you need twitter
**consumer key** and **consumer secret** which are used to make twitter
API calls. So register a new twitter application and get the keys. Check
the following video about creating a twitter app

**1**. Go to
[https://dev.twitter.com/apps/new](https://dev.twitter.com/apps/new) and
register new application. Fill application name, description and
website.  
 **2**. Give some **dummy url in the callback url field** to make the
app as browser app. (If you leave it as blank it will act as Desktop app
which won’t work in mobile device)

![android registering twitter application](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_creating_app.png "android registering twitter application")

**3**. Under the **settings**tab upload icon and change the access type
to **Read and Write**.

![android registering twitter application uploading icon](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_creating_app_1.png "android registering twitter application uploading icon")

**4**. Copy **Consumer Key & Consumer Secret key**

![android registering twitter application consumer key & secret](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_creating_app_2.png "android registering twitter application consumer key & secret")

  

### Downloading twitter4j library

In this tutorial to integrate twitter i used one of the most popular
libraries **twitter4j**. You can read their [official documentation](http://twitter4j.org/en/code-examples.html) to know more
about it.

**1**. Download & extract twitter4j library from
[twitter4j-android-2.2.6.zip (slimmed version for Android platform)](http://twitter4j.org/en/index.html#download). Here is the [direct link](http://twitter4j.org/en/twitter4j-android-2.2.6.zip)  
   

### Creating new Project

In this tutorial i explained basic twitter API calls **login and
updating status** only. If you want to integrate more api calls like
reading users tweet timeline, sending direct message etc., you need to
include other required .jar files too. Read more [Code Examples](http://twitter4j.org/en/code-examples.html) to get know the
usage.

**1**. Create a new project in Eclipse **File New ⇒ Android ⇒
Application Project** and fill the required details.  
 After creating project open **AndroidManifest.xml** file and paste the
following code. I added following code to manifest file

- INTERNET Permission  
 - ACCESS\_NETWORK\_STATE Permission  
 - and added an Intent Filter

    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.androidhive.twitterconnect"
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
                android:label="@string/title_activity_main" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
                    <data android:scheme="oauth" android:host="t4jsample"/>
                </intent-filter>
            </activity>
        </application>
        
        <!-- Permission - Internet Connect -->
        <uses-permission android:name="android.permission.INTERNET" />
        
        <!-- Network State Permissions -->
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    </manifest>

**2**. Copy the required twitter4j **.jar** files into your project’s
**libs** folder. In this tutorial i copied
**twitter4j-core-android-2.2.6** as it is the only required file in this
tutorial.

![android twitter4j jar files](http://www.androidhive.info/wp-content/uploads/2012/09/android-twitter-4j-jar-files.png "android twitter4j jar files")

![android adding twitter4j jar files to project](http://www.androidhive.info/wp-content/uploads/2012/09/android-adding-twitter4j-jar-files-to-project.png "android adding twitter4j jar files to project")

**3**. Before getting into twitter integration i am including two quick
class files to the project. Create a new class file called
**AlertDialogManager.java** and paste the following code. This class
file used to show alert dialog.

    package com.androidhive.twitterconnect;

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

**4**. Create another class file named **ConnectionDetector.java** and
paste the following code. This class is used to detect internet
connection status.

    package com.androidhive.twitterconnect;

    import android.content.Context;
    import android.net.ConnectivityManager;
    import android.net.NetworkInfo;
     
    public class ConnectionDetector {
     
        private Context _context;
     
        public ConnectionDetector(Context context){
            this._context = context;
        }
     
        /**
         * Checking for all possible internet providers
         * **/
        public boolean isConnectingToInternet(){
            ConnectivityManager connectivity = (ConnectivityManager) _context.getSystemService(Context.CONNECTIVITY_SERVICE);
              if (connectivity != null)
              {
                  NetworkInfo[] info = connectivity.getAllNetworkInfo();
                  if (info != null)
                      for (int i = 0; i < info.length; i++)
                          if (info[i].getState() == NetworkInfo.State.CONNECTED)
                          {
                              return true;
                          }
     
              }
              return false;
        }
    }

**5**. Open your **activity\_main.xml** file and type the following
code. In the following code i am placing login button, update status
edittext and update status button. By default all the ui elements will
be hidden except login button.

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        
        <!-- Twitter Login Button -->
        <Button android:id="@+id/btnLoginTwitter"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Login with Twitter"
            android:layout_marginLeft="10dip"
            android:layout_marginRight="10dip"
            android:layout_marginTop="30dip"/>
        
        <!-- user name label -->
        <TextView android:id="@+id/lblUserName"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:padding="10dip"
            android:layout_marginTop="30dip"/>

        <!-- label update status -->
        <TextView android:id="@+id/lblUpdate" 
            android:text="Update Status"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="10dip"
            android:layout_marginRight="10dip"
            android:visibility="gone"/>
        
        <!-- Tweet EditText -->
        <EditText android:id="@+id/txtUpdateStatus"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_margin="10dip"
            android:visibility="gone"/>
        
        <!-- Tweet Button -->
        <Button android:id="@+id/btnUpdateStatus"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Tweet"
            android:layout_marginLeft="10dip"
            android:layout_marginRight="10dip"
            android:visibility="gone"/>
        
        <!-- Twitter Logout button -->
        <Button android:id="@+id/btnLogoutTwitter"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Logout from Twitter"
            android:layout_marginLeft="10dip"
            android:layout_marginRight="10dip"
            android:layout_marginTop="50dip"
            android:visibility="gone"/>

    </LinearLayout>

**6**. Now open your main activity class (In my case
**MainActivity.java**) file and type the following code. In the
following code i am declaring required variables.

    package com.androidhive.twitterconnect;

    import twitter4j.Twitter;
    import twitter4j.TwitterException;
    import twitter4j.TwitterFactory;
    import twitter4j.User;
    import twitter4j.auth.AccessToken;
    import twitter4j.auth.RequestToken;
    import twitter4j.conf.Configuration;
    import twitter4j.conf.ConfigurationBuilder;
    import android.app.Activity;
    import android.app.ProgressDialog;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    import android.content.pm.ActivityInfo;
    import android.net.Uri;
    import android.os.AsyncTask;
    import android.os.Bundle;
    import android.text.Html;
    import android.util.Log;
    import android.view.View;
    import android.widget.Button;
    import android.widget.EditText;
    import android.widget.TextView;
    import android.widget.Toast;

    public class MainActivity extends Activity {
        // Constants
        /**
         * Register your here app https://dev.twitter.com/apps/new and get your
         * consumer key and secret
         * */
        static String TWITTER_CONSUMER_KEY = "LsCQaPOwd8k7WkyRFRZF4Q";
        static String TWITTER_CONSUMER_SECRET = "KJbJu5IQrlwxW7Cwnax3mMzAc4j3n6Wd2dG125srgk";

        // Preference Constants
        static String PREFERENCE_NAME = "twitter_oauth";
        static final String PREF_KEY_OAUTH_TOKEN = "oauth_token";
        static final String PREF_KEY_OAUTH_SECRET = "oauth_token_secret";
        static final String PREF_KEY_TWITTER_LOGIN = "isTwitterLogedIn";

        static final String TWITTER_CALLBACK_URL = "oauth://t4jsample";

        // Twitter oauth urls
        static final String URL_TWITTER_AUTH = "auth_url";
        static final String URL_TWITTER_OAUTH_VERIFIER = "oauth_verifier";
        static final String URL_TWITTER_OAUTH_TOKEN = "oauth_token";

        // Login button
        Button btnLoginTwitter;
        // Update status button
        Button btnUpdateStatus;
        // Logout button
        Button btnLogoutTwitter;
        // EditText for update
        EditText txtUpdate;
        // lbl update
        TextView lblUpdate;
        TextView lblUserName;

        // Progress dialog
        ProgressDialog pDialog;

        // Twitter
        private static Twitter twitter;
        private static RequestToken requestToken;
        
        // Shared Preferences
        private static SharedPreferences mSharedPreferences;
        
        // Internet Connection detector
        private ConnectionDetector cd;
        
        // Alert Dialog Manager
        AlertDialogManager alert = new AlertDialogManager();

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
            
            cd = new ConnectionDetector(getApplicationContext());

            // Check if Internet present
            if (!cd.isConnectingToInternet()) {
                // Internet Connection is not present
                alert.showAlertDialog(MainActivity.this, "Internet Connection Error",
                        "Please connect to working Internet connection", false);
                // stop executing code by return
                return;
            }
            
            // Check if twitter keys are set
            if(TWITTER_CONSUMER_KEY.trim().length() == 0 || TWITTER_CONSUMER_SECRET.trim().length() == 0){
                // Internet Connection is not present
                alert.showAlertDialog(MainActivity.this, "Twitter oAuth tokens", "Please set your twitter oauth tokens first!", false);
                // stop executing code by return
                return;
            }

            // All UI elements
            btnLoginTwitter = (Button) findViewById(R.id.btnLoginTwitter);
            btnUpdateStatus = (Button) findViewById(R.id.btnUpdateStatus);
            btnLogoutTwitter = (Button) findViewById(R.id.btnLogoutTwitter);
            txtUpdate = (EditText) findViewById(R.id.txtUpdateStatus);
            lblUpdate = (TextView) findViewById(R.id.lblUpdate);
            lblUserName = (TextView) findViewById(R.id.lblUserName);

            // Shared Preferences
            mSharedPreferences = getApplicationContext().getSharedPreferences(
                    "MyPref", 0);

  

### Login with Twitter Account

Login with twitter account in your android application involves
following process.

**-** First user asked to authenticate into your application using their
twitter account.  
 **-** Once user successfully logged into his twitter account we will
receive his oauth token and oauth secret.  
 **-** oauth token and oauth secret will be stored in android
application shared preferences.  
 **-** Whenever you need oauth tokens, you can read from shared
preferences.  
 **-** Once user clicks logout button we will delete oauth token &
secret from shared prefrences

**7**. Open your main activity and write click event for login button.

\> In click event i am calling **loginToTwitter()**.  
 \> Once the user logins into twitter he will be redirected to your
android app again with **oauth verifier**.  
 \> Using oauth verifier we do another request to **get access token and
access token secret**.

            /**
             * Twitter login button click event will call loginToTwitter() function
             * */
            btnLoginTwitter.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View arg0) {
                    // Call login twitter function
                    loginToTwitter();
                }
            });

            /** This if conditions is tested once is
             * redirected from twitter page. Parse the uri to get oAuth
             * Verifier
             * */
            if (!isTwitterLoggedInAlready()) {
                Uri uri = getIntent().getData();
                if (uri != null && uri.toString().startsWith(TWITTER_CALLBACK_URL)) {
                    // oAuth verifier
                    String verifier = uri
                            .getQueryParameter(URL_TWITTER_OAUTH_VERIFIER);

                    try {
                        // Get the access token
                        AccessToken accessToken = twitter.getOAuthAccessToken(
                                requestToken, verifier);

                        // Shared Preferences
                        Editor e = mSharedPreferences.edit();

                        // After getting access token, access token secret
                        // store them in application preferences
                        e.putString(PREF_KEY_OAUTH_TOKEN, accessToken.getToken());
                        e.putString(PREF_KEY_OAUTH_SECRET,
                                accessToken.getTokenSecret());
                        // Store login status - true
                        e.putBoolean(PREF_KEY_TWITTER_LOGIN, true);
                        e.commit(); // save changes

                        Log.e("Twitter OAuth Token", "> " + accessToken.getToken());

                        // Hide login button
                        btnLoginTwitter.setVisibility(View.GONE);

                        // Show Update Twitter
                        lblUpdate.setVisibility(View.VISIBLE);
                        txtUpdate.setVisibility(View.VISIBLE);
                        btnUpdateStatus.setVisibility(View.VISIBLE);
                        btnLogoutTwitter.setVisibility(View.VISIBLE);
                        
                        // Getting user details from twitter
                        // For now i am getting his name only
                        long userID = accessToken.getUserId();
                        User user = twitter.showUser(userID);
                        String username = user.getName();
                        
                        // Displaying in xml ui
                        lblUserName.setText(Html.fromHtml("<b>Welcome " + username + "</b>"));
                    } catch (Exception e) {
                        // Check log for login errors
                        Log.e("Twitter Login Error", "> " + e.getMessage());
                    }
                }
            }

        }

and code for **loginToTwitter()** function is

    /**
         * Function to login twitter
         * */
        private void loginToTwitter() {
            // Check if already logged in
            if (!isTwitterLoggedInAlready()) {
                ConfigurationBuilder builder = new ConfigurationBuilder();
                builder.setOAuthConsumerKey(TWITTER_CONSUMER_KEY);
                builder.setOAuthConsumerSecret(TWITTER_CONSUMER_SECRET);
                Configuration configuration = builder.build();
                
                TwitterFactory factory = new TwitterFactory(configuration);
                twitter = factory.getInstance();

                try {
                    requestToken = twitter
                            .getOAuthRequestToken(TWITTER_CALLBACK_URL);
                    this.startActivity(new Intent(Intent.ACTION_VIEW, Uri
                            .parse(requestToken.getAuthenticationURL())));
                } catch (TwitterException e) {
                    e.printStackTrace();
                }
            } else {
                // user already logged into twitter
                Toast.makeText(getApplicationContext(),
                        "Already Logged into twitter", Toast.LENGTH_LONG).show();
            }
        }

        /**
         * Check user already logged in your application using twitter Login flag is
         * fetched from Shared Preferences
         * */
        private boolean isTwitterLoggedInAlready() {
            // return twitter login status from Shared Preferences
            return mSharedPreferences.getBoolean(PREF_KEY_TWITTER_LOGIN, false);
        }

![android twitter login](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_login.png "android twitter login")

![android twitter login oauth](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_oauth_login.png "android twitter login oauth")

![android twitter login get username](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_update_status.png "android twitter login get username")

  

### Update Twitter Status

**8**. Once you login with twitter account you can see interface to
update the status. Open your main activity class write a click event for
update status button.

\> In the following code async thread **updateTwitterStatus()** is
called on clicking update status button.

    /**
             * Button click event to Update Status, will call updateTwitterStatus()
             * function
             * */
            btnUpdateStatus.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View v) {
                    // Call update status function
                    // Get the status from EditText
                    String status = txtUpdate.getText().toString();

                    // Check for blank text
                    if (status.trim().length() > 0) {
                        // update status
                        new updateTwitterStatus().execute(status);
                    } else {
                        // EditText is empty
                        Toast.makeText(getApplicationContext(),
                                "Please enter status message", Toast.LENGTH_SHORT)
                                .show();
                    }
                }
            });

and code for **updateTwitterStatus()** async task is

        /**
         * Function to update status
         * */
        class updateTwitterStatus extends AsyncTask<String, String, String> {

            /**
             * Before starting background thread Show Progress Dialog
             * */
            @Override
            protected void onPreExecute() {
                super.onPreExecute();
                pDialog = new ProgressDialog(MainActivity.this);
                pDialog.setMessage("Updating to twitter...");
                pDialog.setIndeterminate(false);
                pDialog.setCancelable(false);
                pDialog.show();
            }

            /**
             * getting Places JSON
             * */
            protected String doInBackground(String... args) {
                Log.d("Tweet Text", "> " + args[0]);
                String status = args[0];
                try {
                    ConfigurationBuilder builder = new ConfigurationBuilder();
                    builder.setOAuthConsumerKey(TWITTER_CONSUMER_KEY);
                    builder.setOAuthConsumerSecret(TWITTER_CONSUMER_SECRET);
                    
                    // Access Token 
                    String access_token = mSharedPreferences.getString(PREF_KEY_OAUTH_TOKEN, "");
                    // Access Token Secret
                    String access_token_secret = mSharedPreferences.getString(PREF_KEY_OAUTH_SECRET, "");
                    
                    AccessToken accessToken = new AccessToken(access_token, access_token_secret);
                    Twitter twitter = new TwitterFactory(builder.build()).getInstance(accessToken);
                    
                    // Update status
                    twitter4j.Status response = twitter.updateStatus(status);
                    
                    Log.d("Status", "> " + response.getText());
                } catch (TwitterException e) {
                    // Error in updating status
                    Log.d("Twitter Update Error", e.getMessage());
                }
                return null;
            }

            /**
             * After completing background task Dismiss the progress dialog and show
             * the data in UI Always use runOnUiThread(new Runnable()) to update UI
             * from background thread, otherwise you will get error
             * **/
            protected void onPostExecute(String file_url) {
                // dismiss the dialog after getting all products
                pDialog.dismiss();
                // updating UI from Background Thread
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(getApplicationContext(),
                                "Status tweeted successfully", Toast.LENGTH_SHORT)
                                .show();
                        // Clearing EditText field
                        txtUpdate.setText("");
                    }
                });
            }

        }

![android twitter update status](http://www.androidhive.info/wp-content/uploads/2012/09/android_twitter_update_status_message.png "android twitter update status")

![andorid update twitter status](http://www.androidhive.info/wp-content/uploads/2012/09/anroid-twitter-update-from-device.png "andorid update twitter status")

  

### Logout from Twitter

**9**. Open your main activity and write a click event for logout
button. Please remember this logout code won’t logout user from the
twitter in the browser. It will just clear the access tokens from your
application shared preferences.

            /**
             * Button click event for logout from twitter
             * */
            btnLogoutTwitter.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View arg0) {
                    // Call logout twitter function
                    logoutFromTwitter();
                }
            });

and code for **logoutFromTwitter()** function is

        /**
         * Function to logout from twitter
         * It will just clear the application shared preferences
         * */
        private void logoutFromTwitter() {
            // Clear the shared preferences
            Editor e = mSharedPreferences.edit();
            e.remove(PREF_KEY_OAUTH_TOKEN);
            e.remove(PREF_KEY_OAUTH_SECRET);
            e.remove(PREF_KEY_TWITTER_LOGIN);
            e.commit();

            // After this take the appropriate action
            // I am showing the hiding/showing buttons again
            // You might not needed this code
            btnLogoutTwitter.setVisibility(View.GONE);
            btnUpdateStatus.setVisibility(View.GONE);
            txtUpdate.setVisibility(View.GONE);
            lblUpdate.setVisibility(View.GONE);
            lblUserName.setText("");
            lblUserName.setVisibility(View.GONE);

            btnLoginTwitter.setVisibility(View.VISIBLE);
        }

  

### Final Code:

Final code of **MainActivity.java**

    package com.androidhive.twitterconnect;

    import twitter4j.Twitter;
    import twitter4j.TwitterException;
    import twitter4j.TwitterFactory;
    import twitter4j.User;
    import twitter4j.auth.AccessToken;
    import twitter4j.auth.RequestToken;
    import twitter4j.conf.Configuration;
    import twitter4j.conf.ConfigurationBuilder;
    import android.app.Activity;
    import android.app.ProgressDialog;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    import android.content.pm.ActivityInfo;
    import android.net.Uri;
    import android.os.AsyncTask;
    import android.os.Bundle;
    import android.text.Html;
    import android.util.Log;
    import android.view.View;
    import android.widget.Button;
    import android.widget.EditText;
    import android.widget.TextView;
    import android.widget.Toast;

    public class MainActivity extends Activity {
        // Constants
        /**
         * Register your here app https://dev.twitter.com/apps/new and get your
         * consumer key and secret
         * */
        static String TWITTER_CONSUMER_KEY = "LsCQaPOwd8k7WkyRFRZF4Q";
        static String TWITTER_CONSUMER_SECRET = "KJbJu5IQrlwxW7Cwnax3mMzAc4j3n6Wd2dG125srgk";

        // Preference Constants
        static String PREFERENCE_NAME = "twitter_oauth";
        static final String PREF_KEY_OAUTH_TOKEN = "oauth_token";
        static final String PREF_KEY_OAUTH_SECRET = "oauth_token_secret";
        static final String PREF_KEY_TWITTER_LOGIN = "isTwitterLogedIn";

        static final String TWITTER_CALLBACK_URL = "oauth://t4jsample";

        // Twitter oauth urls
        static final String URL_TWITTER_AUTH = "auth_url";
        static final String URL_TWITTER_OAUTH_VERIFIER = "oauth_verifier";
        static final String URL_TWITTER_OAUTH_TOKEN = "oauth_token";

        // Login button
        Button btnLoginTwitter;
        // Update status button
        Button btnUpdateStatus;
        // Logout button
        Button btnLogoutTwitter;
        // EditText for update
        EditText txtUpdate;
        // lbl update
        TextView lblUpdate;
        TextView lblUserName;

        // Progress dialog
        ProgressDialog pDialog;

        // Twitter
        private static Twitter twitter;
        private static RequestToken requestToken;
        
        // Shared Preferences
        private static SharedPreferences mSharedPreferences;
        
        // Internet Connection detector
        private ConnectionDetector cd;
        
        // Alert Dialog Manager
        AlertDialogManager alert = new AlertDialogManager();

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
            
            cd = new ConnectionDetector(getApplicationContext());

            // Check if Internet present
            if (!cd.isConnectingToInternet()) {
                // Internet Connection is not present
                alert.showAlertDialog(MainActivity.this, "Internet Connection Error",
                        "Please connect to working Internet connection", false);
                // stop executing code by return
                return;
            }
            
            // Check if twitter keys are set
            if(TWITTER_CONSUMER_KEY.trim().length() == 0 || TWITTER_CONSUMER_SECRET.trim().length() == 0){
                // Internet Connection is not present
                alert.showAlertDialog(MainActivity.this, "Twitter oAuth tokens", "Please set your twitter oauth tokens first!", false);
                // stop executing code by return
                return;
            }

            // All UI elements
            btnLoginTwitter = (Button) findViewById(R.id.btnLoginTwitter);
            btnUpdateStatus = (Button) findViewById(R.id.btnUpdateStatus);
            btnLogoutTwitter = (Button) findViewById(R.id.btnLogoutTwitter);
            txtUpdate = (EditText) findViewById(R.id.txtUpdateStatus);
            lblUpdate = (TextView) findViewById(R.id.lblUpdate);
            lblUserName = (TextView) findViewById(R.id.lblUserName);

            // Shared Preferences
            mSharedPreferences = getApplicationContext().getSharedPreferences(
                    "MyPref", 0);

            /**
             * Twitter login button click event will call loginToTwitter() function
             * */
            btnLoginTwitter.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View arg0) {
                    // Call login twitter function
                    loginToTwitter();
                }
            });

            /**
             * Button click event to Update Status, will call updateTwitterStatus()
             * function
             * */
            btnUpdateStatus.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View v) {
                    // Call update status function
                    // Get the status from EditText
                    String status = txtUpdate.getText().toString();

                    // Check for blank text
                    if (status.trim().length() > 0) {
                        // update status
                        new updateTwitterStatus().execute(status);
                    } else {
                        // EditText is empty
                        Toast.makeText(getApplicationContext(),
                                "Please enter status message", Toast.LENGTH_SHORT)
                                .show();
                    }
                }
            });

            /**
             * Button click event for logout from twitter
             * */
            btnLogoutTwitter.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View arg0) {
                    // Call logout twitter function
                    logoutFromTwitter();
                }
            });

            /** This if conditions is tested once is
             * redirected from twitter page. Parse the uri to get oAuth
             * Verifier
             * */
            if (!isTwitterLoggedInAlready()) {
                Uri uri = getIntent().getData();
                if (uri != null && uri.toString().startsWith(TWITTER_CALLBACK_URL)) {
                    // oAuth verifier
                    String verifier = uri
                            .getQueryParameter(URL_TWITTER_OAUTH_VERIFIER);

                    try {
                        // Get the access token
                        AccessToken accessToken = twitter.getOAuthAccessToken(
                                requestToken, verifier);

                        // Shared Preferences
                        Editor e = mSharedPreferences.edit();

                        // After getting access token, access token secret
                        // store them in application preferences
                        e.putString(PREF_KEY_OAUTH_TOKEN, accessToken.getToken());
                        e.putString(PREF_KEY_OAUTH_SECRET,
                                accessToken.getTokenSecret());
                        // Store login status - true
                        e.putBoolean(PREF_KEY_TWITTER_LOGIN, true);
                        e.commit(); // save changes

                        Log.e("Twitter OAuth Token", "> " + accessToken.getToken());

                        // Hide login button
                        btnLoginTwitter.setVisibility(View.GONE);

                        // Show Update Twitter
                        lblUpdate.setVisibility(View.VISIBLE);
                        txtUpdate.setVisibility(View.VISIBLE);
                        btnUpdateStatus.setVisibility(View.VISIBLE);
                        btnLogoutTwitter.setVisibility(View.VISIBLE);
                        
                        // Getting user details from twitter
                        // For now i am getting his name only
                        long userID = accessToken.getUserId();
                        User user = twitter.showUser(userID);
                        String username = user.getName();
                        
                        // Displaying in xml ui
                        lblUserName.setText(Html.fromHtml("<b>Welcome " + username + "</b>"));
                    } catch (Exception e) {
                        // Check log for login errors
                        Log.e("Twitter Login Error", "> " + e.getMessage());
                    }
                }
            }

        }

        /**
         * Function to login twitter
         * */
        private void loginToTwitter() {
            // Check if already logged in
            if (!isTwitterLoggedInAlready()) {
                ConfigurationBuilder builder = new ConfigurationBuilder();
                builder.setOAuthConsumerKey(TWITTER_CONSUMER_KEY);
                builder.setOAuthConsumerSecret(TWITTER_CONSUMER_SECRET);
                Configuration configuration = builder.build();
                
                TwitterFactory factory = new TwitterFactory(configuration);
                twitter = factory.getInstance();

                try {
                    requestToken = twitter
                            .getOAuthRequestToken(TWITTER_CALLBACK_URL);
                    this.startActivity(new Intent(Intent.ACTION_VIEW, Uri
                            .parse(requestToken.getAuthenticationURL())));
                } catch (TwitterException e) {
                    e.printStackTrace();
                }
            } else {
                // user already logged into twitter
                Toast.makeText(getApplicationContext(),
                        "Already Logged into twitter", Toast.LENGTH_LONG).show();
            }
        }

        /**
         * Function to update status
         * */
        class updateTwitterStatus extends AsyncTask<String, String, String> {

            /**
             * Before starting background thread Show Progress Dialog
             * */
            @Override
            protected void onPreExecute() {
                super.onPreExecute();
                pDialog = new ProgressDialog(MainActivity.this);
                pDialog.setMessage("Updating to twitter...");
                pDialog.setIndeterminate(false);
                pDialog.setCancelable(false);
                pDialog.show();
            }

            /**
             * getting Places JSON
             * */
            protected String doInBackground(String... args) {
                Log.d("Tweet Text", "> " + args[0]);
                String status = args[0];
                try {
                    ConfigurationBuilder builder = new ConfigurationBuilder();
                    builder.setOAuthConsumerKey(TWITTER_CONSUMER_KEY);
                    builder.setOAuthConsumerSecret(TWITTER_CONSUMER_SECRET);
                    
                    // Access Token 
                    String access_token = mSharedPreferences.getString(PREF_KEY_OAUTH_TOKEN, "");
                    // Access Token Secret
                    String access_token_secret = mSharedPreferences.getString(PREF_KEY_OAUTH_SECRET, "");
                    
                    AccessToken accessToken = new AccessToken(access_token, access_token_secret);
                    Twitter twitter = new TwitterFactory(builder.build()).getInstance(accessToken);
                    
                    // Update status
                    twitter4j.Status response = twitter.updateStatus(status);
                    
                    Log.d("Status", "> " + response.getText());
                } catch (TwitterException e) {
                    // Error in updating status
                    Log.d("Twitter Update Error", e.getMessage());
                }
                return null;
            }

            /**
             * After completing background task Dismiss the progress dialog and show
             * the data in UI Always use runOnUiThread(new Runnable()) to update UI
             * from background thread, otherwise you will get error
             * **/
            protected void onPostExecute(String file_url) {
                // dismiss the dialog after getting all products
                pDialog.dismiss();
                // updating UI from Background Thread
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(getApplicationContext(),
                                "Status tweeted successfully", Toast.LENGTH_SHORT)
                                .show();
                        // Clearing EditText field
                        txtUpdate.setText("");
                    }
                });
            }

        }

        /**
         * Function to logout from twitter
         * It will just clear the application shared preferences
         * */
        private void logoutFromTwitter() {
            // Clear the shared preferences
            Editor e = mSharedPreferences.edit();
            e.remove(PREF_KEY_OAUTH_TOKEN);
            e.remove(PREF_KEY_OAUTH_SECRET);
            e.remove(PREF_KEY_TWITTER_LOGIN);
            e.commit();

            // After this take the appropriate action
            // I am showing the hiding/showing buttons again
            // You might not needed this code
            btnLogoutTwitter.setVisibility(View.GONE);
            btnUpdateStatus.setVisibility(View.GONE);
            txtUpdate.setVisibility(View.GONE);
            lblUpdate.setVisibility(View.GONE);
            lblUserName.setText("");
            lblUserName.setVisibility(View.GONE);

            btnLoginTwitter.setVisibility(View.VISIBLE);
        }

        /**
         * Check user already logged in your application using twitter Login flag is
         * fetched from Shared Preferences
         * */
        private boolean isTwitterLoggedInAlready() {
            // return twitter login status from Shared Preferences
            return mSharedPreferences.getBoolean(PREF_KEY_TWITTER_LOGIN, false);
        }

        protected void onResume() {
            super.onResume();
        }

    }

This image is for thumbnail purpose  
 ![android twitter connect](http://www.androidhive.info/wp-content/uploads/2012/09/banner_twitter_connect.png "android twitter connect")
