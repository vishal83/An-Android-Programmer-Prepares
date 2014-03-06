[![androidhive](http://www.androidhive.info/wp-content/themes/androidhive_v2_analytics/img/logo.png)](http://www.androidhive.info)

September 2, 2012 10:54 AM

Android Adding Search Functionality to ListView
===============================================
Adding search functionality to listview will filters the list data with
a matching string, hence provides user an easy way to find the
information he needs. In this tutorial i am discussing how to enable
search filter to android ListView.

[![Download Code](http://www.androidhive.info/wp-content/uploads/2011/08/download_btn.png "Download Code")](http://download.androidhive.info/download?code=LmrfHFvXtbTvvp2KpVe0xyL2KArKNU0qM5D0RTLO%2B2U0yZ3YwgqyT9OVT7hlHMxB5D%2BDpFeKf2idme2Olori8AduTKddyTHnVGpQnCi%2ByWF2v5ElVWBgrY7EA%3D%3Dk1Wb9SlGLFzh9EVmvTwDO1AXfpyzTuDjUr1)

  
  
 **1**. Create a new project in Eclipse **File New ⇒ Android ⇒
Application Project** and fill the required details.  
 **2**. Create required files needed to generate a listview. I am using
my default **activity\_main.xml** as listview and created a new xml file
for single listitem named **list\_item.xml**. Also make sure that you
have created a EditText above the listview which will be used to search
the listview.

**activity\_main.xml**

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        
        <!-- Editext for Search -->
        <EditText android:id="@+id/inputSearch"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:hint="Search products.."
            android:inputType="textVisiblePassword"/>
     
        <!-- List View -->
        <ListView
            android:id="@+id/list_view"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
     
    </LinearLayout>

**list\_item.xml**

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical" >
        
        <!-- Single ListItem -->
        
        <!-- Product Name -->
        <TextView android:id="@+id/product_name"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:padding="10dip"
            android:textSize="16dip"
            android:textStyle="bold"/>    

    </LinearLayout>

**3**. Now open your **MainActivity.java** and paste the following code
to create a simple ListView. In the following code i stored all the list
data in an array called **products[]** and attached to listview using
simple **ArrayAdapter**.

    package com.androidhive.androidlistviewwithsearch;

    import java.util.ArrayList;
    import java.util.HashMap;

    import android.app.Activity;
    import android.os.Bundle;
    import android.text.Editable;
    import android.text.TextWatcher;
    import android.widget.ArrayAdapter;
    import android.widget.EditText;
    import android.widget.ListView;

    public class MainActivity extends Activity {
        
        // List view
        private ListView lv;
        
        // Listview Adapter
        ArrayAdapter<String> adapter;
        
        // Search EditText
        EditText inputSearch;
        
        
        // ArrayList for Listview
        ArrayList<HashMap<String, String>> productList;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            // Listview Data
            String products[] = {"Dell Inspiron", "HTC One X", "HTC Wildfire S", "HTC Sense", "HTC Sensation XE",
                                    "iPhone 4S", "Samsung Galaxy Note 800",
                                    "Samsung Galaxy S3", "MacBook Air", "Mac Mini", "MacBook Pro"};
            
            lv = (ListView) findViewById(R.id.list_view);
            inputSearch = (EditText) findViewById(R.id.inputSearch);
            
            // Adding items to listview
            adapter = new ArrayAdapter<String>(this, R.layout.list_item, R.id.product_name, products);
            lv.setAdapter(adapter);       
            
        }
        
    }

![android listview search](http://www.androidhive.info/wp-content/uploads/2012/09/android_listview_search.png "android listview search")

  

### Enabling Search Functionality

**4**. Search functionality can be enabled by writing simple lines of
code. All you need to do is adding **addTextChangedListener** to
EditText. Once user enters a new data in EditText we need to get the
text from it and passing it to array adapter filter. All the following
code in your **MainActivity.java**

            inputSearch.addTextChangedListener(new TextWatcher() {
                
                @Override
                public void onTextChanged(CharSequence cs, int arg1, int arg2, int arg3) {
                    // When user changed the Text
                    MainActivity.this.adapter.getFilter().filter(cs);   
                }
                
                @Override
                public void beforeTextChanged(CharSequence arg0, int arg1, int arg2,
                        int arg3) {
                    // TODO Auto-generated method stub
                    
                }
                
                @Override
                public void afterTextChanged(Editable arg0) {
                    // TODO Auto-generated method stub                          
                }
            });

**5**. Finally add the following property in your
**AndroidManifest.xml** file to hide the keyboard on loading Activity.

    android:windowSoftInputMode="stateHidden"

**AndroidManifest.xml**

    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.androidhive.androidlistviewwithsearch"
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
                android:label="@string/title_activity_main"
                android:windowSoftInputMode="stateHidden">
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>

![android listview search filter](http://www.androidhive.info/wp-content/uploads/2012/09/android_listview_search_results.png "android listview search filter")

  

### Final Code:

    package com.androidhive.androidlistviewwithsearch;

    import java.util.ArrayList;
    import java.util.HashMap;

    import android.app.Activity;
    import android.os.Bundle;
    import android.text.Editable;
    import android.text.TextWatcher;
    import android.widget.ArrayAdapter;
    import android.widget.EditText;
    import android.widget.ListView;

    public class MainActivity extends Activity {
        
        // List view
        private ListView lv;
        
        // Listview Adapter
        ArrayAdapter<String> adapter;
        
        // Search EditText
        EditText inputSearch;
        
        
        // ArrayList for Listview
        ArrayList<HashMap<String, String>> productList;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            // Listview Data
            String products[] = {"Dell Inspiron", "HTC One X", "HTC Wildfire S", "HTC Sense", "HTC Sensation XE",
                                    "iPhone 4S", "Samsung Galaxy Note 800",
                                    "Samsung Galaxy S3", "MacBook Air", "Mac Mini", "MacBook Pro"};
            
            lv = (ListView) findViewById(R.id.list_view);
            inputSearch = (EditText) findViewById(R.id.inputSearch);
            
            // Adding items to listview
            adapter = new ArrayAdapter<String>(this, R.layout.list_item, R.id.product_name, products);
            lv.setAdapter(adapter);
            
            /**
             * Enabling Search Filter
             * */
            inputSearch.addTextChangedListener(new TextWatcher() {
                
                @Override
                public void onTextChanged(CharSequence cs, int arg1, int arg2, int arg3) {
                    // When user changed the Text
                    MainActivity.this.adapter.getFilter().filter(cs);   
                }
                
                @Override
                public void beforeTextChanged(CharSequence arg0, int arg1, int arg2,
                        int arg3) {
                    // TODO Auto-generated method stub
                    
                }
                
                @Override
                public void afterTextChanged(Editable arg0) {
                    // TODO Auto-generated method stub                          
                }
            });
        }    
    }

This image is for thumbnail purpose.  
 ![android listview search filter](http://www.androidhive.info/wp-content/uploads/2012/09/banner_android_listview_search1.png "android listview search filter")

