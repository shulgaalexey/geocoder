# How to use Tizen Native Geocode API in 3 simple steps

*Since Tizen 2.4*

## Video Tutorial
--------------

<a href="http://www.youtube.com/watch?feature=player_embedded&v=rlEp7EV1lfU
" target="_blank"><img src="http://img.youtube.com/vi/rlEp7EV1lfU/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="480" height="360" border="10" /></a>




## What is Geocode?

**Geocode** translates the place address into its geographical coordinates.

Geocode API is one of Maps Services, provided by Tizen Native Location Framework.

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/maps_service.png" alt="Tizen Native Geocode API" style="width:500px"/>


To start using Geocode API we are going to:

1. Create empty Tizen Native Application
2. Start Maps Service
3. Run Geocode



## Prerequisites
*The following assumes that you have already basic knowledge in Tizen development:* https://developer.tizen.org/development/getting-started/preface

Maps Service API requires a security key, issued by Maps Provider.

In case of HERE, the security key is a concatenation of app_id and app_code.

This is generated on https://developer.here.com/plans/api/consumer-mapping accordingly to your consumer plan.
```
“your-security-key” is “app_id/app_code”
```

Note: Be sure your deveice or emulator has a valid internet connection.

## 1. Create empty Tizen Native Application
In the IDE create an empty Application and run it on emulator or device.

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/create_empty_prj.png" alt="Create Empty Tizen Native Project" style="width:500px"/>

When the application started, the "Hello Tizen" label should appear.

NOTE: Get familiar with instructions on how to create an empty application at https://developer.tizen.org/development/
Go to "Native Application" -> "Creating Your First Tizen Application".


## 2. Start Maps Service
Include the Maps Service API header file:

```C
#include <maps_service.h>
```

NOTE: This allows using all Native Maps Service API. For more details visit https://developer.tizen.org/development/api-references/. 
Navigate to 2.4 API References -> Native Application ->Mobile Native -> Native API Reference -> Location -> Maps Service.

Add a Maps Service handle to the appdata_s structure:

```C
typedef struct appdata {
	Evas_Object *win;
	Evas_Object *conform;
	Evas_Object *label;
	maps_service_h maps; /* Handle of Maps Service */
} appdata_s;
```

Create an instance of Maps Service API in the app_create() function:

```C
static bool
app_create(void *data)
{
	appdata_s *ad = data;
	create_base_gui(ad);

	/* Specify Maps Provider name. */
	if(maps_service_create("HERE", &ad->maps) != MAPS_ERROR_NONE)
		return false;

	/* Set security key, issued by Maps Provider */
	maps_service_set_provider_key(ad->maps, "your-security-key");

	return true;
}
```

Release The Maps Service in app_terminate() function:

```C
static void
app_terminate(void *data)
{
	/* Release all resources. */
	appdata_s *ad = data;
	maps_service_destroy(ad->maps);
}
```

Add privileges, required for Maps Service API:

 * mapservice
 * internet
 * network.get


<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/set_privileges.png" alt="Set Privileges" style="width:500px"/>


## 3. Run Geocode
Add Geocode request into the app_create() function:

```C
/* Use Geocoder API */
int req_id = 0;
maps_service_geocode(ad->maps, "Moscow Bolshoi Theatre", NULL, geocode_cb, ad, &req_id);
```

Implement Geocode callback:

```C
static bool
geocode_cb(maps_error_e result, int request_id, int index, int total, maps_coordinates_h coordinates, void *user_data)
{
	/* Get latitude and longitude of the geocode */
	double latitude = .0;
	double longitude = .0;
	maps_coordinates_get_latitude(coordinates, &latitude);
	maps_coordinates_get_longitude(coordinates, &longitude);

	/* Output the geocode */
	char geocode[64] = {0};
	snprintf(geocode, 64, "Geocode is: (%f, %f)", latitude, longitude);
	appdata_s *ad = user_data;
	elm_object_text_set(ad->label, geocode);

	/* Don't forget to release the coordinates handle */
	maps_coordinates_destroy(coordinates);

	/* If return true, we will receive other geocodes,
	 * of the address we put to the Geocoder request.
	 * In this example, one geocode is enough for us. */
	return false;
}
```

Run the Application.

At first we will see the familiar "Hello Tizen" line, but in a couple of moments it should change to "Geocode is: (55.756970, 37.615020)”.
This would indicate the geocode of the Bolshoi Theatre in Moscow.



## Reference
https://developer.tizen.org/development/tutorials/native-application/location/maps-service#geocode

https://developer.tizen.org/community/tip-tech/how-use-tizen-native-geocode-api-3-simple-steps
