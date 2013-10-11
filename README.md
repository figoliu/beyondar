beyondar
========

This framework has been designed to offer some resources to those developers with an interest in working with Augmented Reality based on geolocalization on SmartPhones and tablets.

[BeyondAR Game](https://play.google.com/store/apps/details?id=com.beyondar#?t=W251bGwsMSwxLDIxMiwiY29tLmJleW9uZGFyIl0.) is using this platform to show the creatures arround the user. Here some images:

![Screenshot](http://beyondar.com/pictures/screenshots/screen_4.jpg)
![Screenshot](http://beyondar.com/pictures/screenshots/screen_1.jpg)

##How to build your first app

To create the UI it we can choose using an Android Layout XML or using java code. For both of them we can use the `BeyondarFragmentSupport` or the `BeyondarFragment` fragments.

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <fragment
        android:id="@+id/beyondarFragment"
        android:name="com.beyondar.android.fragment.BeyondarFragmentSupport"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</FrameLayout>

```
Once we have the layout ready it is time to load it.

```java
private BeyondarFragmentSupport mBeyondarFragment;
...
@Override
public void onCreate(Bundle savedInstanceState) {
     super.onCreate(savedInstanceState);
     setContentView(R.layout.main);
     setContentView(R.layout.simple_camera);
     mBeyondarFragment = (BeyondarFragmentSupport)
     getSupportFragmentManager().findFragmentById(R.id.beyondarFragment);
     ...
}
```

The next step is to create the `World` that holds the information related to the objects that need to be displayed in the app using augmented reality.

```java
// The user can set the default bitmap. This is useful if you are
// loading images form Internet and the connection get lost
world.setDefaultBitmap(R.drawable.beyondar_default_unknow_icon);

// User position (you can change it using the GPS listeners form Android
// API)
world.setGeoPosition(41.26533734214473d, 1.925848038959814d);

// Create an object with an image in the app resources.
GeoObject go1 = new GeoObject(1l);
go1.setGeoPosition(41.26523339794433d, 1.926036406654116d);
go1.setImageResource(R.drawable.creature_1);
go1.setName("Creature 1");

// Is it also possible to load the image asynchronously form internet
GeoObject go2 = new GeoObject(2l);
go2.setGeoPosition(41.26518966360719d, 1.92582424468222d);
go2.setImageUri("http://beyondar.com/sites/default/files/logo_reduced.png");
go2.setName("Online image");

// Also possible to get images from the SDcard
GeoObject go3 = new GeoObject(3l);
go3.setGeoPosition(41.26550959641445d, 1.925873388087619d);
go3.setImageUri("/sdcard/TheAvengers_IronMan.jpeg");
go3.setName("IronMan from sdcard");

// And the same goes for the app assets
GeoObject go4 = new GeoObject(4l);
go4.setGeoPosition(41.26518862002349d, 1.925662767707665d);
go4.setImageUri("assets://creature_7.png");
go4.setName("Image from assets");

//We add this GeoObjects to the world
world.addBeyondarObject(go1);
world.addBeyondarObject(go2);
world.addBeyondarObject(go3);
world.addBeyondarObject(go4);

//Finally we add the Wold data in to the fragment
mBeyondarFragment.setWorld(mWorld);
```
Now we have the app ready to show the GeoObjects. But we also want to capture events, for instance, when the user click on a GeoObject. For that we need to implement `OnARTouchListener`
```java
...
mBeyondarFragment.setonARTouchListener(this);
...
@Override
public void onTouchARView(MotionEvent event, BeyondarGLSurfaceView beyondarView) {

     // We need the coordinates to know where the user has touched the screen
     float x = event.getX();
     float y = event.getY();

     ArrayList<BeyondarObject> geoObjects = new ArrayList<BeyondarObject>();

     //Now we use the view to collect all the objects that are touched by the user
     beyondarView.getARObjectOnScreenCoordinates(x, y, geoObjects);

     String textEvent = "";

     switch (event.getAction()) {
     case MotionEvent.ACTION_DOWN:
          textEvent = "Event type ACTION_DOWN: ";
          break;
     case MotionEvent.ACTION_UP:
          textEvent = "Event type ACTION_UP: ";
          break;
     case MotionEvent.ACTION_MOVE:
          textEvent = "Event type ACTION_MOVE: ";
          break;
     default:
          break;
     }
     // We just iterate and display the Objects. The first object will be the closest to the user
     Iterator<BeyondarObject> iterator = geoObjects.iterator();
     while (iterator.hasNext()) {
          BeyondarObject geoObject = iterator.next();
          textEvent = textEvent + " " + geoObject.getName();

     }
     doSomethingInTheUIThread(textEvent);
}
```

If we want to run the `onTouchARView` method in the UI thread, we can use the annotation `@OnUiThread` for that purpose.
