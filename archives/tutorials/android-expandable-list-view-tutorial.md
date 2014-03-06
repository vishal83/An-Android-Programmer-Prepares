[![androidhive](http://www.androidhive.info/wp-content/themes/androidhive_v2_analytics/img/logo.png)](http://www.androidhive.info)

July 27, 2013 08:32 PM

Android Expandable List View Tutorial
=====================================
Expandable list view is used to group list data by categories. It has
the capability of expanding and collapsing the groups when user touches
header.

If you are not aware of list view before please refer to this tutorial
[Android ListView Tutorial](http://www.androidhive.info/2011/10/android-listview-tutorial/)

[![Download Code](http://www.androidhive.info/wp-content/uploads/2011/08/download_btn.png "Download Code")](http://download.androidhive.info/download?code=dYyIlJRXHdloHtGVSHUhaO5HpwDl4zBCfyZ9vDcVuGZk%2BamTcclSBiFGz%2FAKGNXPJGfLfxFjNwNAtJqGYCoYAzDpldshsZocJjRNrKc0gs340iYuOEaYkuOuA%3D%3DXXnaCiv4AXbpk8DtFJnwcgrqr1XueZROb8K)

This image is for thumbnail purpose only  
 ![android expandable list view](http://www.androidhive.info/wp-content/uploads/2013/07/expandable-list-banner.png)

  

Let’s start by creating a new project..

**1**. Create a new project in the Eclipse IDE from **File ⇒ Android
Application Project** and fill all required details. I left my main
activity name as *MainActivity.java*

**2**. In order to create an expandable list view, we need three xml
layout files. First one is for main listview, 2nd one for list view
group item and 3rd one is for list view child item. Open your
*activity\_main.xml* and add *ExpandableListView* element.

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical"
        android:background="#f4f4f4" >

                <ExpandableListView
                    android:id="@+id/lvExp"
                    android:layout_height="match_parent"
                    android:layout_width="match_parent"/>   

    </LinearLayout>

**3**. Create another xml layout for list view group header. I created
an xml file named *list\_group.xml* and pasted following code.

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="8dp" 
        android:background="#000000">


        <TextView
            android:id="@+id/lblListHeader"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingLeft="?android:attr/expandableListPreferredItemPaddingLeft"
            android:textSize="17dp"
            android:textColor="#f9f93d" />

    </LinearLayout>

**4**.Create one more xml file named *list\_item.xml* for child list
item. This will contain simple TextView element.

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="55dip"
        android:orientation="vertical" >

        <TextView
            android:id="@+id/lblListItem"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:textSize="17dip"
            android:paddingTop="5dp"
            android:paddingBottom="5dp"
            android:paddingLeft="?android:attr/expandableListPreferredChildPaddingLeft" />

    </LinearLayout>

**5**. I am using a custom adapter class to create list view. Create a
new class file called *ExpandableListAdapter.java* and extend this from
*BaseExpandableListAdapter*. This class provides required methods to
render listview.

*getGroupView()* – Returns view for the list group header  
 *getChildView()* – Returns view for list child item

    package info.androidhive.expandablelistview;

    import java.util.HashMap;
    import java.util.List;

    import android.content.Context;
    import android.graphics.Typeface;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.BaseExpandableListAdapter;
    import android.widget.TextView;

    public class ExpandableListAdapter extends BaseExpandableListAdapter {

        private Context _context;
        private List<String> _listDataHeader; // header titles
        // child data in format of header title, child title
        private HashMap<String, List<String>> _listDataChild;

        public ExpandableListAdapter(Context context, List<String> listDataHeader,
                HashMap<String, List<String>> listChildData) {
            this._context = context;
            this._listDataHeader = listDataHeader;
            this._listDataChild = listChildData;
        }

        @Override
        public Object getChild(int groupPosition, int childPosititon) {
            return this._listDataChild.get(this._listDataHeader.get(groupPosition))
                    .get(childPosititon);
        }

        @Override
        public long getChildId(int groupPosition, int childPosition) {
            return childPosition;
        }

        @Override
        public View getChildView(int groupPosition, final int childPosition,
                boolean isLastChild, View convertView, ViewGroup parent) {

            final String childText = (String) getChild(groupPosition, childPosition);

            if (convertView == null) {
                LayoutInflater infalInflater = (LayoutInflater) this._context
                        .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
                convertView = infalInflater.inflate(R.layout.list_item, null);
            }

            TextView txtListChild = (TextView) convertView
                    .findViewById(R.id.lblListItem);

            txtListChild.setText(childText);
            return convertView;
        }

        @Override
        public int getChildrenCount(int groupPosition) {
            return this._listDataChild.get(this._listDataHeader.get(groupPosition))
                    .size();
        }

        @Override
        public Object getGroup(int groupPosition) {
            return this._listDataHeader.get(groupPosition);
        }

        @Override
        public int getGroupCount() {
            return this._listDataHeader.size();
        }

        @Override
        public long getGroupId(int groupPosition) {
            return groupPosition;
        }

        @Override
        public View getGroupView(int groupPosition, boolean isExpanded,
                View convertView, ViewGroup parent) {
            String headerTitle = (String) getGroup(groupPosition);
            if (convertView == null) {
                LayoutInflater infalInflater = (LayoutInflater) this._context
                        .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
                convertView = infalInflater.inflate(R.layout.list_group, null);
            }

            TextView lblListHeader = (TextView) convertView
                    .findViewById(R.id.lblListHeader);
            lblListHeader.setTypeface(null, Typeface.BOLD);
            lblListHeader.setText(headerTitle);

            return convertView;
        }

        @Override
        public boolean hasStableIds() {
            return false;
        }

        @Override
        public boolean isChildSelectable(int groupPosition, int childPosition) {
            return true;
        }
    }

**6**. Once you are done with customer adapter, open your
*MainActivity.java* and do the following changes. In the following I
created required data needed for list view and passed it to custom
adapter.

    package info.androidhive.expandablelistview;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import android.app.Activity;
    import android.os.Bundle;
    import android.view.View;
    import android.widget.ExpandableListView;
    import android.widget.ExpandableListView.OnChildClickListener;
    import android.widget.ExpandableListView.OnGroupClickListener;
    import android.widget.ExpandableListView.OnGroupCollapseListener;
    import android.widget.ExpandableListView.OnGroupExpandListener;
    import android.widget.Toast;

    public class MainActivity extends Activity {

        ExpandableListAdapter listAdapter;
        ExpandableListView expListView;
        List<String> listDataHeader;
        HashMap<String, List<String>> listDataChild;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // get the listview
            expListView = (ExpandableListView) findViewById(R.id.lvExp);

            // preparing list data
            prepareListData();

            listAdapter = new ExpandableListAdapter(this, listDataHeader, listDataChild);

            // setting list adapter
            expListView.setAdapter(listAdapter);
        }

        /*
         * Preparing the list data
         */
        private void prepareListData() {
            listDataHeader = new ArrayList<String>();
            listDataChild = new HashMap<String, List<String>>();

            // Adding child data
            listDataHeader.add("Top 250");
            listDataHeader.add("Now Showing");
            listDataHeader.add("Coming Soon..");

            // Adding child data
            List<String> top250 = new ArrayList<String>();
            top250.add("The Shawshank Redemption");
            top250.add("The Godfather");
            top250.add("The Godfather: Part II");
            top250.add("Pulp Fiction");
            top250.add("The Good, the Bad and the Ugly");
            top250.add("The Dark Knight");
            top250.add("12 Angry Men");

            List<String> nowShowing = new ArrayList<String>();
            nowShowing.add("The Conjuring");
            nowShowing.add("Despicable Me 2");
            nowShowing.add("Turbo");
            nowShowing.add("Grown Ups 2");
            nowShowing.add("Red 2");
            nowShowing.add("The Wolverine");

            List<String> comingSoon = new ArrayList<String>();
            comingSoon.add("2 Guns");
            comingSoon.add("The Smurfs 2");
            comingSoon.add("The Spectacular Now");
            comingSoon.add("The Canyons");
            comingSoon.add("Europa Report");

            listDataChild.put(listDataHeader.get(0), top250); // Header, Child data
            listDataChild.put(listDataHeader.get(1), nowShowing);
            listDataChild.put(listDataHeader.get(2), comingSoon);
        }
    }

  
  
 Run your project, you should see following output. (Note: The list view
group indicators might change depending upon your android version)

![android expandable list view](http://www.androidhive.info/wp-content/uploads/2013/07/android-expandable-listview.jpg)

  

### ListView child item click listener

Detecting the child item click can be done by implementing
*setOnChildClickListener* listener on listview.

    // Listview on child click listener
            expListView.setOnChildClickListener(new OnChildClickListener() {

                @Override
                public boolean onChildClick(ExpandableListView parent, View v,
                        int groupPosition, int childPosition, long id) {
                    Toast.makeText(
                            getApplicationContext(),
                            listDataHeader.get(groupPosition)
                                    + " : "
                                    + listDataChild.get(
                                            listDataHeader.get(groupPosition)).get(
                                            childPosition), Toast.LENGTH_SHORT)
                            .show();
                    return false;
                }
            });

  

### Listening when group is expanded

You may want to execute some lines of code when the listview group is
expanded. For this you can use *setOnGroupExpandListener* which triggers
an event when listview group expanded.

            // Listview Group expanded listener
            expListView.setOnGroupExpandListener(new OnGroupExpandListener() {

                @Override
                public void onGroupExpand(int groupPosition) {
                    Toast.makeText(getApplicationContext(),
                            listDataHeader.get(groupPosition) + " Expanded",
                            Toast.LENGTH_SHORT).show();
                }
            });

  

### Listening when group is collapsed

Implementing *setOnGroupCollapseListener* will trigger an event when
listview group is collapsed.

            // Listview Group collasped listener
            expListView.setOnGroupCollapseListener(new OnGroupCollapseListener() {

                @Override
                public void onGroupCollapse(int groupPosition) {
                    Toast.makeText(getApplicationContext(),
                            listDataHeader.get(groupPosition) + " Collapsed",
                            Toast.LENGTH_SHORT).show();

                }
            });

  

### Complete Code

Here is the final code for *MainActivity.java*

    package info.androidhive.expandablelistview;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import android.app.Activity;
    import android.os.Bundle;
    import android.view.View;
    import android.widget.ExpandableListView;
    import android.widget.ExpandableListView.OnChildClickListener;
    import android.widget.ExpandableListView.OnGroupClickListener;
    import android.widget.ExpandableListView.OnGroupCollapseListener;
    import android.widget.ExpandableListView.OnGroupExpandListener;
    import android.widget.Toast;

    public class MainActivity extends Activity {

        ExpandableListAdapter listAdapter;
        ExpandableListView expListView;
        List<String> listDataHeader;
        HashMap<String, List<String>> listDataChild;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // get the listview
            expListView = (ExpandableListView) findViewById(R.id.lvExp);

            // preparing list data
            prepareListData();

            listAdapter = new ExpandableListAdapter(this, listDataHeader, listDataChild);

            // setting list adapter
            expListView.setAdapter(listAdapter);

            // Listview Group click listener
            expListView.setOnGroupClickListener(new OnGroupClickListener() {

                @Override
                public boolean onGroupClick(ExpandableListView parent, View v,
                        int groupPosition, long id) {
                    // Toast.makeText(getApplicationContext(),
                    // "Group Clicked " + listDataHeader.get(groupPosition),
                    // Toast.LENGTH_SHORT).show();
                    return false;
                }
            });

            // Listview Group expanded listener
            expListView.setOnGroupExpandListener(new OnGroupExpandListener() {

                @Override
                public void onGroupExpand(int groupPosition) {
                    Toast.makeText(getApplicationContext(),
                            listDataHeader.get(groupPosition) + " Expanded",
                            Toast.LENGTH_SHORT).show();
                }
            });

            // Listview Group collasped listener
            expListView.setOnGroupCollapseListener(new OnGroupCollapseListener() {

                @Override
                public void onGroupCollapse(int groupPosition) {
                    Toast.makeText(getApplicationContext(),
                            listDataHeader.get(groupPosition) + " Collapsed",
                            Toast.LENGTH_SHORT).show();

                }
            });

            // Listview on child click listener
            expListView.setOnChildClickListener(new OnChildClickListener() {

                @Override
                public boolean onChildClick(ExpandableListView parent, View v,
                        int groupPosition, int childPosition, long id) {
                    // TODO Auto-generated method stub
                    Toast.makeText(
                            getApplicationContext(),
                            listDataHeader.get(groupPosition)
                                    + " : "
                                    + listDataChild.get(
                                            listDataHeader.get(groupPosition)).get(
                                            childPosition), Toast.LENGTH_SHORT)
                            .show();
                    return false;
                }
            });
        }

        /*
         * Preparing the list data
         */
        private void prepareListData() {
            listDataHeader = new ArrayList<String>();
            listDataChild = new HashMap<String, List<String>>();

            // Adding child data
            listDataHeader.add("Top 250");
            listDataHeader.add("Now Showing");
            listDataHeader.add("Coming Soon..");

            // Adding child data
            List<String> top250 = new ArrayList<String>();
            top250.add("The Shawshank Redemption");
            top250.add("The Godfather");
            top250.add("The Godfather: Part II");
            top250.add("Pulp Fiction");
            top250.add("The Good, the Bad and the Ugly");
            top250.add("The Dark Knight");
            top250.add("12 Angry Men");

            List<String> nowShowing = new ArrayList<String>();
            nowShowing.add("The Conjuring");
            nowShowing.add("Despicable Me 2");
            nowShowing.add("Turbo");
            nowShowing.add("Grown Ups 2");
            nowShowing.add("Red 2");
            nowShowing.add("The Wolverine");

            List<String> comingSoon = new ArrayList<String>();
            comingSoon.add("2 Guns");
            comingSoon.add("The Smurfs 2");
            comingSoon.add("The Spectacular Now");
            comingSoon.add("The Canyons");
            comingSoon.add("Europa Report");

            listDataChild.put(listDataHeader.get(0), top250); // Header, Child data
            listDataChild.put(listDataHeader.get(1), nowShowing);
            listDataChild.put(listDataHeader.get(2), comingSoon);
        }
    }

